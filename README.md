# PRACTICA-6
# 6.1 Manipulación de Archivos en una Tarjeta SD
### CÚAL ES EL FIN DE ESTA PRÁCTICA ? 
En esta práctica se explorará el funcionamiento del bus de comunicación SPI (Interfaz Periférica Serial), como parte del estudio de los protocolos de comunicación digital. A diferencia de la actividad anterior, centrada en el protocolo I2C, en esta ocasión se trabajará con el manejo de archivos en una tarjeta SD y con la lectura de identificadores RFID a través de SPI. El propósito de esta actividad es aprender a realizar operaciones de lectura y escritura de datos en una tarjeta SD utilizando SPI, así como configurar un módulo lector de RFID para obtener el UID (identificador único) de una tarjeta.

## CÓDIGO

El siguiente fragmento de código permite abrir un archivo de texto guardado en una tarjeta SD y visualizar su contenido a través de la consola.

```c++
 #include <SPI.h> 
#include <SD.h> 
File myFile; 
void setup() 
{ 
  Serial.begin(9600); 
  Serial.print("Iniciando SD ..."); 
  if (!SD.begin(4)) { 
    Serial.println("No se pudo inicializar"); 
    return; 
  }
Serial.println("inicializacion exitosa"); 
  myFile = SD.open("archivo.txt");//abrimos  el archivo  
  if (myFile) { 
    Serial.println("archivo.txt:"); 
    while (myFile.available()) { 
Serial.write(myFile.read()); 
    } 
    myFile.close(); //cerramos el archivo 
  } else { 
    Serial.println("Error al abrir el archivo"); 
  } 
} 
void loop() 
{ 
}
```


### EXPECTED OUTPUT
```
Iniciando SD... inicializacion existosa
archivo.txt:
Ejemplo de Salida Esperada
```
### CONCLUSIONES 
El programa inicia la comunicación con la tarjeta SD y localiza un archivo de texto llamado archivo.txt. Si la tarjeta está funcionando correctamente, se accede al archivo, se lee su contenido y se imprime en la consola serial. Para prevenir errores, el archivo se cierra una vez finalizada la lectura.

# 6.2: Lectura de Etiquetas RFID**

## CÚAL ES EL FIN DE ESTA PRÁCTICA? 
En esta actividad se analizará el funcionamiento del bus de comunicación SPI, enfocándose especialmente en la lectura de etiquetas RFID a través de tecnología NFC. El propósito principal es familiarizarse con el uso del protocolo SPI para la interacción con dispositivos periféricos y aplicar este conocimiento en la lectura de datos desde un módulo lector RFID mediante comunicación NFC.

### COMPONENTES REQUERIDOS 
Para esta práctica se emplearon una placa ESP32-S3, un módulo lector RFID y un dispositivo con capacidad NFC.

### CÓDIGO

```c++
#include <Arduino.h>
#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 9 //Pin 9 para el reset del RC522
#define SS_PIN 10 //Pin 10 para el SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
    Serial.begin(115200); //Iniciamos la comunicación serial
    SPI.begin(36, 37, 35); //Iniciamos el Bus SPI
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
            Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0"
            : " ");
            Serial.print(mfrc522.uid.uidByte[i], HEX); 
        } 
        Serial.println();
        // Terminamos la lectura de la tarjeta actual
        mfrc522.PICC_HaltA(); 
        } 
    } 
}
```

#### QUÉ HACE EL CÓDIGO UTILIZADO ? 
El programa está diseñado para detectar y leer etiquetas RFID utilizando el módulo MFRC522, con la finalidad de identificar tarjetas cercanas y extraer su código único (UID).

Para la comunicación con el lector RFID, se emplea la librería MFRC522, configurando previamente los pines necesarios para la conexión mediante SPI. Durante la función setup(), se inicializan tanto la interfaz serial como el bus SPI para establecer la comunicación.

En el ciclo principal (loop()), el sistema monitorea continuamente la presencia de nuevas tarjetas dentro del rango del lector. Cuando se reconoce una tarjeta, se captura su UID y se imprime en el monitor serial. Finalmente, la tarjeta pasa a un estado de espera, facilitando la detección de futuras lecturas.

### EXPECTED OUTPUT
```
Lectura del UID
Card UID: 08 E4 3E AD
Card UID: 08 DB 76 CB
```

## CONCLUSIONES
Mediante esta práctica se lleva a cabo la detección y lectura de etiquetas RFID empleando un módulo MFRC522 conectado a un ESP32-S3. Este método ofrece una solución práctica y eficaz para la interacción con dispositivos RFID en múltiples aplicaciones electrónicas.


