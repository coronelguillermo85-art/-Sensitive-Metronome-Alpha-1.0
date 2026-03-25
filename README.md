<div align="center">

  <img src="https://img.shields.io/badge/Project-Sensitive_Lab-blueviolet?style=for-the-badge" alt="Sensitive Lab">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License">

  <h1>🛡️ <span style="letter-spacing: 3px;">SENSITIVE METRONOME ALPHA 1.0</span></h1>
  <p><i>"Para una conexión de red sólida, trabajemos en la conexión humana primero."</i></p>

  <hr />

  <p><b>Innovación Mínima | Luthería Digital | Soberanía Tecnológica</b></p>
  <p>Un proyecto de <b>Guillermo Coronel</b> enfocado en hardware abierto.</p>

</div>

---

## 📋 Descripción del Proyecto

El **Sensitive Metronome** es una herramienta de luthería digital de código abierto diseñada para músicos que requieren una referencia de pulso precisa, estable y legible en todo momento. Este prototipo, desarrollado bajo el protocolo de investigación de **Sensitive Lab**, responde a la necesidad de un seguimiento rítmico visual y sonoro en entornos de ensayo, estudio y ejecución en vivo.

A diferencia de los metrónomos estándar, este dispositivo incorpora una interfaz **OLED** de alta visibilidad para mostrar no solo el tempo en **BPM**, sino también la estructura del **compás en tiempo real**, facilitando la lectura periférica del pulso.

---

## 📸 Prototipo Alpha Funcional

<div align="center">
  <p><b>Interfaz de Usuario — SENSITIVE / 40 BPM</b></p>

  <img 
    src="./assets/metronome-alpha.jpg" 
    alt="Prototipo Alpha 1.0 del Sensitive Metronome en protoboard"
    width="420"
    style="border-radius: 14px; box-shadow: 0 8px 24px rgba(0,0,0,0.25);"
  />

  <br><br>
  <p><i>Vista general del prototipo Alpha 1.0 integrado en protoboard con pantalla OLED, potenciómetro y cableado funcional.</i></p>
</div>

---

## 🚀 Funciones Principales

- **Control de BPM Dinámico:** ajuste analógico mediante potenciómetro de precisión en un rango de **40 a 200 BPM**.
- **Monitor de Compás Visual:** visualización del tiempo activo (**1, 2, 3, 4**) en gran tamaño para lectura rápida.
- **Identidad Sensitive Custom:** marca visual integrada directamente en la interfaz del firmware.
- **Feedback Dual Sincronizado:** pulso sonoro diferenciado para el primer tiempo y referencia visual simultánea.

---

## 🛠️ Lista de Materiales (BOM)

<div align="center">

| Componente | Especificación | Propósito |
|---|---|---|
| **Microcontrolador** | Arduino Nano / Uno | Procesamiento de la lógica rítmica |
| **Pantalla** | OLED 0.96" SSD1306 (I2C) | Interfaz visual y visualización de compás |
| **Potenciómetro** | 10K Ohm lineal | Control manual de velocidad |
| **Buzzer / Piezo** | 5V pasivo | Referencia sonora del pulso |

</div>

---

## 💻 Firmware (Arduino C++)

<details>
<summary><b>Expandir para ver el código fuente (Licencia MIT)</b></summary>

```cpp
/*
 * SENSITIVE METRONOME ALPHA 1.1
 * Desarrollado por Guillermo Coronel
 * Think Tank: Sensitive Lab
 */

#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

const int potPin = A0;
const int buzzerPin = 9;

unsigned long lastTime = 0;
int beatCount = 1;
const int signature = 4; // Compás 4/4

void drawSensitiveHeader() {
  // Línea superior decorativa
  display.drawLine(0, 10, 32, 10, WHITE);
  display.drawLine(96, 10, 127, 10, WHITE);

  // Marca estilo “tech”
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.setCursor(36, 2);
  display.print("SENSITIVE");
}

void drawBeat(int beat) {
  display.setTextSize(4);
  display.setCursor(54, 20);
  display.print(beat);
}

void drawBPM(int bpm) {
  display.setTextSize(1);
  display.setCursor(0, 54);
  display.print("BPM ");
  display.print(bpm);

  display.setCursor(84, 54);
  display.print("4/4");
}

void setup() {
  pinMode(buzzerPin, OUTPUT);

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    while (true);
  }

  display.clearDisplay();
  display.display();
}

void loop() {
  int bpm = map(analogRead(potPin), 0, 1023, 40, 200);
  unsigned long interval = 60000UL / bpm;

  if (millis() - lastTime >= interval) {
    lastTime = millis();

    display.clearDisplay();

    drawSensitiveHeader();
    drawBeat(beatCount);
    drawBPM(bpm);

    // Indicador visual del pulso
    if (beatCount == 1) {
      display.fillCircle(10, 30, 4, WHITE);
    } else {
      display.drawCircle(10, 30, 4, WHITE);
    }

    display.display();

    // Click sonoro: primer tiempo más agudo
    tone(buzzerPin, (beatCount == 1) ? 1200 : 850, 45);

    beatCount++;
    if (beatCount > signature) {
      beatCount = 1;
    }
  }
}
