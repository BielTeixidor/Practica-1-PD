# Pràctica 1: BLINK

L'objectiu d'aquesta pràctica és fer parpellejar un LED de manera periòdica, utilitzant un microcontrolador ESP32 amb l'entorn de desenvolupament Visual Studio Code mitjançant PlatformIO i programant en C++.

## 1. Codi Bàsic:
```c++
#define LED_BUILTIN 2
#define DELAY 500

void setup() {
 pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
 digitalWrite(LED_BUILTIN, HIGH);
 delay(DELAY);
 digitalWrite(LED_BUILTIN, LOW);
 delay(DELAY);
}
```

## 2. Modificar el programa (ON, OFF)
```c++
#include <Arduino.h>

#define LED_BUILTIN 2
#define DELAY 1000 //ms

void setup() {
  Serial.begin(115200); // Inicialitza la comunicació sèrie entre l'Arduino i un altre dispositiu.
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  digitalWrite(LED_BUILTIN, HIGH);
  Serial.println("ON");
  delay(DELAY);
  digitalWrite(LED_BUILTIN, LOW);
  Serial.println("OFF");
  delay(DELAY);
}
```
Aquest codi mantindrà un bucle infinit on el LED s'encendrà, es transmetrà "ON" pel port sèrie, esperarà 1000 milisegons, apagarà el LED, enviarà "OFF" pel port sèrie, i esperarà altres 1000 milisegons, repetint aquest procés indefinidament.

## 3. Modificar el programa perquè actuï sobre els registres d'entrada/sortida.
```c++
#include <Arduino.h>

#define LED_BUILTIN 2
#define DELAY 1000

#define GPIO_OUT_REG 0x3FF4400C

void setup() {
  Serial.begin(115200);
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop() {
  volatile uint32_t *gpio_out = (volatile uint32_t *)GPIO_OUT_REG;

  *gpio_out |= (1 << LED_BUILTIN);
  digitalWrite(LED_BUILTIN, HIGH);

  Serial.println("ON");
  
  delay(DELAY);

  *gpio_out ^= (1 << LED_BUILTIN);
  digitalWrite(LED_BUILTIN, LOW);

  Serial.println("OFF");

  delay(DELAY);
}
```
En aquesta versió del codi, s'han introduït modificacions per interactuar directament amb els registres d'entrada/sortida. Les adaptacions s'han fet tenint en compte les modificacions relacionades amb *gpio_out*.

## 4. Mesurar la freqüència màxima
En aquest apartat, l'objectiu és mesurar la freqüència màxima d'entrada/sortida que permet el microcontrolador en 4 casos diferents.

### 4.1. Amb l'enviament pel port sèrie del missatge i utilitzant les funcions d'Arduino:
```c++
#include <Arduino.h>

int led = 2;

void setup() {                
  pinMode(led, OUTPUT);   
  Serial.begin(115200);
}

void loop() {
  Serial.println("ON");
  digitalWrite(led, HIGH);
  Serial.println("OFF");      
  digitalWrite(led, LOW);
}
```
La freqüència registrada en l'oscil·loscopi és de 18.06 kHz.

### 4.2. Amb l'enviament pel port sèrie i accedint directament als registres:
```c++
#include <Arduino.h>

int led = 2;
uint32_t *gpio_out = (uint32_t *)GPIO_OUT_REG;

void setup() {                
  pinMode(led, OUTPUT);   
  Serial.begin(115200);
}

void loop() {
  Serial.println("ON");
  *gpio_out |= (1 << led);
  Serial.println("OFF");      
  *gpio_out ^= (1 << led);
}
```
La freqüència registrada en l'oscil·loscopi és de 18.47 kHz.

### 4.3. Sense l'enviament pel port sèrie del missatge i utilitzant les funcions d'Arduino:
```c++
#include <Arduino.h>

int led = 2;

void setup() {                
  pinMode(led, OUTPUT);   
}

void loop() {
  digitalWrite(led, HIGH);
  digitalWrite(led, LOW);
}
```
La freqüència registrada en l'oscil·loscopi és de 3.13 MHz.

### 4.4. Sense l'enviament pel port sèrie i accedint directament als registres:
```c++
#include <Arduino.h>

int led = 2;
uint32_t *gpio_out = (uint32_t *)GPIO_OUT_REG;

void setup() {                
  pinMode(led, OUTPUT);   
}

void loop() {
  *gpio_out |= (1 << led);
  *gpio_out ^= (1 << led);
}
```
La freqüència registrada en l'oscil·loscopi és de 10.20 MHz.

### 4.5. Conclusions
En general, es pot concloure que l'accés directe als registres sense utilitzar el port sèrie permet aconseguir freqüències molt més altes en comparació amb l'ús de les funcions d'Arduino juntament amb el port sèrie.

