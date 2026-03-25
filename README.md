<div align="center">

<img src="https://img.shields.io/badge/Project-Sensitive_Lab-blueviolet?style=for-the-badge" alt="Sensitive Lab">
<img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License">

<h1>S E N S I T I V E &nbsp; M E T R O N O M E &nbsp; A L P H A 1.0</h1>

<p><i>"Para una conexión de red sólida, trabajemos en la conexión humana primero."</i></p>

<hr>

<p><b>Innovación Mínima | Luthería Digital | Soberanía Tecnológica</b></p>
<p>Un proyecto de <b>Guillermo Coronel</b></p>

</div>

---

<div align="center">

## 📋 Descripción del Proyecto

</div>

<p align="center">
El <b>Sensitive Metronome</b> es una herramienta de luthería digital de código abierto diseñada para músicos que requieren una referencia de pulso precisa, estable y visible en todo momento.  
Este prototipo, desarrollado bajo el protocolo de investigación de <b>Sensitive Lab</b>, responde a la necesidad de un seguimiento rítmico visual y sonoro en entornos de ensayo y ejecución en vivo.
<br><br>
A diferencia de los metrónomos tradicionales, este sistema incorpora una pantalla <b>OLED SSD1306</b> para representar el tempo (BPM) y la estructura del <b>compás en tiempo real</b>, facilitando la lectura periférica del pulso.
</p>

---

<div align="center">

## 📸 Prototipo Alpha Funcional

<p><b>Interfaz de Usuario — SENSITIVE / 40 BPM</b></p>

<img 
  src="https://drive.google.com/uc?export=view&id=18FnlAM1md5h_4qxYPEVcu_rsOnHZ64GK"
  width="420"
  alt="Sensitive Metronome Alpha 1.0"
/>

<br><br>

<p><i>Vista general del prototipo Alpha 1.0 integrado en protoboard.</i></p>

</div>

---

<div align="center">

## 🚀 Funciones Principales

<ul style="text-align:left; display:inline-block;">

<li><b>Control de BPM Dinámico:</b> ajuste mediante potenciómetro en rango de 40 a 200 BPM.</li>

<li><b>Monitor de Compás Visual:</b> visualización del tiempo activo en gran tamaño para lectura rápida.</li>

<li><b>Identidad Sensitive Custom:</b> estética de laboratorio integrada en la interfaz del firmware.</li>

<li><b>Feedback Dual Sincronizado:</b> click sonoro diferenciado y referencia visual del pulso.</li>

</ul>

<br>

## 🛠️ Lista de Materiales (BOM)

<table>
<tr>
<th>Componente</th>
<th>Especificación</th>
<th>Propósito</th>
</tr>

<tr>
<td><b>Microcontrolador</b></td>
<td>Arduino Nano / Uno</td>
<td>Procesamiento de la lógica rítmica</td>
</tr>

<tr>
<td><b>Pantalla</b></td>
<td>OLED 0.96" SSD1306 (I2C)</td>
<td>Interfaz visual</td>
</tr>

<tr>
<td><b>Potenciómetro</b></td>
<td>10K lineal</td>
<td>Control de BPM</td>
</tr>

<tr>
<td><b>Buzzer / Piezo</b></td>
<td>5V pasivo</td>
<td>Referencia sonora</td>
</tr>

</table>

</div>

---

<div align="center">

## 💻 Firmware (Arduino C++)

</div>

```cpp
/*
 * SENSITIVE METRONOME ALPHA 1.1
 * Guillermo Coronel
 * Sensitive Lab
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
const int signature = 4;

void drawHeader() {
  display.drawLine(0, 10, 32, 10, WHITE);
  display.drawLine(96, 10, 127, 10, WHITE);

  display.setTextSize(1);
  display.setCursor(36, 2);
  display.print("SENSITIVE");
}

void setup() {
  pinMode(buzzerPin, OUTPUT);

  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
}

void loop() {

  int bpm = map(analogRead(potPin), 0, 1023, 40, 200);
  unsigned long interval = 60000UL / bpm;

  if (millis() - lastTime >= interval) {

    lastTime = millis();
    display.clearDisplay();

    drawHeader();

    display.setTextSize(4);
    display.setCursor(54, 20);
    display.print(beatCount);

    display.setTextSize(1);
    display.setCursor(0, 54);
    display.print("BPM ");
    display.print(bpm);

    if (beatCount == 1) {
      display.fillCircle(10, 30, 4, WHITE);
    } else {
      display.drawCircle(10, 30, 4, WHITE);
    }

    display.display();

    tone(buzzerPin, (beatCount == 1) ? 1200 : 850, 45);

    beatCount++;
    if (beatCount > signature) beatCount = 1;
  }
}
