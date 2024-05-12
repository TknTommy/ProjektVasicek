# Hardwareové a software vybavení pro provozovatele památek pro ověřování platnosti vstupenek
<p align="center">
    <a alt="Contributors">
        <img src="https://img.shields.io/github/contributors/TknTommy/ProjektVasicek" /></a>
	<a alt="Stars">
        <img src="https://img.shields.io/github/stars/TknTommy/ProjektVasicek" /></a>
	<a alt="Forks">
        <img src="https://img.shields.io/github/forks/TknTommy/ProjektVasicek" /></a>
	<a alt="Issues">
        <img src="https://img.shields.io/github/issues/TknTommy/ProjektVasicek" /></a>
	<img alt="GitHub License" src="https://img.shields.io/github/license/TknTommy/ProjektVasicek">
	<a alt="Release">
        <img src="https://img.shields.io/github/release/TknTommy/ProjektVasicek" /></a>
	<a alt="Code_coverage>
        <img src="https://img.shields.io/codecov/c/github/TknTommy/ProjektVasicek" /></a>
</p>
## Myšlenková mapa pro tento projekt 
https://coggle.it/diagram/ZkEoO64Gr7SFqhFV/t/-

## Hardware
#### ESP32
Centrlní prvek celého projektu
#### RFID čtečka - EM4100 RDM6300



## Software
### Front-end
#### WebUI na PC
![Frontend_projekt](https://github.com/TknTommy/ProjektVasicek/assets/117047367/05e20204-2b3a-42bd-8eae-a51199f946b8)
### Mobilní aplikace
![image](https://github.com/TknTommy/ProjektVasicek/assets/117047367/466773ed-17e0-4757-9be5-5b3a3d7990b3)

## Kód
Kód pro pro čtení a ověření platnosti
```
#include <Arduino.h>
#include <WiFi.h>
#include <FirebaseESP32.h>

#define FIREBASE_HOST "https://project_name.europe-west1.firebasedatabase.app"
#define FIREBASE_AUTH "Your_API_Key"



char ssid[] = "SSID";
char pass[] = "PASSWORD";

char prijateID[14];
bool shoda = false;

void setup() {
  Serial.begin(9600);
  WiFi.begin(ssid, pass);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println();
  Serial.print("Connected to WiFi: ");
  Serial.println(WiFi.localIP());
  Firebase.begin(FIREBASE_HOST, FIREBASE_AUTH);
}

void loop() {
  if (Serial.available() > 0) {
    if (Serial.read() == '\n') {
      Serial.println("Scanning RFID...");
      for (int i = 0; i < 14; i++) {
        while (!Serial.available()) {}
        prijateID[i] = Serial.read();
        Serial.print(prijateID[i]);
      }
      Serial.println();

      shoda = checkRFID(prijateID);
      if (shoda) {
        Serial.println("RFID identifikátor byl nalezen v databázi.");
      } else {
        Serial.println("RFID identifikátor nebyl nalezen v databázi.");
      }
    }
  }
}

bool checkRFID(char* id) {
  String path = "/rfid/";
  path += id;

  String data = Firebase.getString(path);
  if (data != "null") {
    return true;
  }
  return false;
}
```

