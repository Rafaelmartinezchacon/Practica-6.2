# PRACTICA 6.2: Lectura de etiqueta RFID

## Codigo

```
#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 9 //Pin 9 para el reset del RC522
#define SS_PIN 10 //Pin 10 para el SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
Serial.begin(9600); //Iniciamos la comunicación serial
SPI.begin(); //Iniciamos el Bus SPI
mfrc522.PCD_Init(); // Iniciamos el MFRC522
Serial.println("Lectura del UID");
}
void loop() {
// Revisamos si hay nuevas tarjetas presentes
if ( mfrc522.PICC_IsNewCardPresent())
{
//Seleccionamos una tarjeta
if ( mfrc522.PICC_ReadCardSerial())
{
// Enviamos serialemente su UID
Serial.print("Card UID:");
for (byte i = 0; i < mfrc522.uid.size; i++) {
Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
Serial.print(mfrc522.uid.uidByte[i], HEX);
}
Serial.println();
// Terminamos la lectura de la tarjeta actual
mfrc522.PICC_HaltA();
}
}
}
```

# Funcionamiento

Antes de empezar cargaremos las dos librerias, la primera nos va a permitir comunicarnos con los dispositivos SPI y con la segunda podremos leer y escribir en una tarjeta o etiqueta RFID utilizando la interfaz ISO/IEC 14443A/MIFARE.

```
#include <SPI.h>
#include <MFRC522.h>
```

Primero especificamos los pines reset y SDA del modulo, los demas no hace falta especificarlos porque trabajan con los pines SPI del Arduino. Despues creamos el objeto para el RC522.

```
#define RST_PIN 9
#define SS_PIN 10
MFRC522 mfrc522(SS_PIN, RST_PIN);
```

Seguidamente empezamos ocn el `void setup()`donde iniciaremos una comunicación serie con una velocidad de 9600 bauds. Después en la segunda linea iniciamos el bus SPI y con la función `mfrc522.PCD_Init();` iniciaremos y configuraremos al RC522 para su lectura. Por ultimo haremos euna impresión por pantalla:

```
void setup() {
Serial.begin(9600);
SPI.begin();
mfrc522.PCD_Init();
Serial.println("Lectura del UID");
}
```

Dentro del `void loop()` haremos un ``if ( mfrc522.PICC_IsNewCardPresent()) `
que nos devolvera un verdadero o falso dependiendo de si hay una tarjeta cerca del modulo RC522. En este if haremos una función `mfrc522.PICC_ReadCardSerial() ` que nos servira para comunicarnos con una tarjeta. Lo que hara sera devolvernos un valor verdadero si logra seleccionar una tarjera para la lectura, en el caso contrario, nos retornara un valor falso. Dentro de este if crearemos un bucle for donde imprimiremos por pantalla el codigo de identifiación con la siguiente función `mfrc522.uid.uidByte[i] ` donde la variable "i" sera la que recorrera el bucle. 

Por ultimo utilizamos la función `mfrc522.PICC_HaltA()` donde indicaremos que la lectura de la tarjeta ha finalizado y `sNewCardPresent() ` devolvera un valor falso para esta tarjeta mientras no se retire. El codigo quedaria algo asi:

```
void loop() {
if ( mfrc522.PICC_IsNewCardPresent())
{
if ( mfrc522.PICC_ReadCardSerial())
{
Serial.print("Card UID:");
for (byte i = 0; i < mfrc522.uid.size; i++) {
Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ");
Serial.print(mfrc522.uid.uidByte[i], HEX);
}
Serial.println();
mfrc522.PICC_HaltA();
}
}
```

