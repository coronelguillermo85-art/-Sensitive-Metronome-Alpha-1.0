<div align="center">
  <img src="https://img.shields.io/badge/Project-Sensitive_Lab-blueviolet?style=for-the-badge" alt="Sensitive Lab">
  <img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" alt="MIT License">
  
  <h1>🛡️ SENSITIVE METRONOME ALPHA 1.0</h1>
  <p><i>"Para una conexión de red sólida, trabajemos en la conexión humana primero."</i></p>

  <hr />

  <p><b>Innovación Mínima | Luthería Digital | Soberanía Tecnológica</b></p>
  <p>Un proyecto de <b>Guillermo Coronel</b> para el desarrollo de herramientas de proximidad.</p>
</div>

### 📋 Descripción del Proyecto
El **Sensitive Metronome** es una herramienta de luthería digital de código abierto diseñada para músicos y bandas que requieren una referencia de pulso infalible. Este prototipo, desarrollado bajo el protocolo de investigación de **Sensitive Lab**, resuelve la necesidad de un seguimiento rítmico visual y táctil en entornos de alta exigencia (ensayos, vivo, grabación).

A diferencia de los metrónomos estándar, este dispositivo utiliza una interfaz **OLED** de alta lectura para mostrar no solo el tempo, sino también la estructura del **compás en tiempo real**.

---

### 🚀 Funciones Principales
<ul>
  <li><b>Control de BPM Dinámico:</b> Ajuste analógico mediante potenciómetro de alta precisión (rango 40-200 BPM).</li>
  <li><b>Monitor de Compás Visual:</b> Visualización del tiempo activo (1, 2, 3, 4) en formato gigante para lectura periférica.</li>
  <li><b>Interfaz Sensitive Custom:</b> Identidad visual del laboratorio integrada en el firmware.</li>
  <li><b>Feedback Dual Sincronizado:</b> Click sonoro diferenciado para el primer tiempo y parpadeo visual.</li>
</ul>

---

### 🛠️ Lista de Materiales (BOM)
<table align="center">
  <tr>
    <th>Componente</th>
    <th>Especificación</th>
    <th>Propósito</th>
  </tr>
  <tr>
    <td><b>Microcontrolador</b></td>
    <td>Arduino Nano / Uno</td>
    <td>Procesamiento de lógica rítmica.</td>
  </tr>
  <tr>
    <td><b>Pantalla</b></td>
    <td>OLED 0.96" SSD1306 (I2C)</td>
    <td>Interfaz visual y visualización de compás.</td>
  </tr>
  <tr>
    <td><b>Potenciómetro</b></td>
    <td>10K Ohm Lineal</td>
    <td>Control manual de velocidad (BPM).</td>
  </tr>
  <tr>
    <td><b>Buzzer / Piezo</b></td>
    <td>5V Pasivo</td>
    <td>Referencia sonora del pulso.</td>
  </tr>
</table>

---

### 📸 Evidencia de Prototipado y Testeo
<div align="center">
  <p><b>Interfaz de Usuario (Compás y BPM)</b></p>
  <img src="![Uploading IMG_20260317_143741825_NV (1).jpg…]()
">
  <br />
  <p><b>
  <img 
</div>

---

### 💻 Firmware (Arduino C++)
<details>
<summary><b>Expandir para ver el código fuente (Licencia MIT)</b></summary>

```cpp
/* * SENSITIVE METRONOME ALPHA 1.0
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
const int signature = 4; // Compás de 4/4

void setup() {
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  pinMode(buzzerPin, OUTPUT);
}

void loop() {
  int bpm = map(analogRead(potPin), 0, 1023, 40, 200);
  unsigned long interval = 60000 / bpm;

  if (millis() - lastTime >= interval) {
    lastTime = millis();
    display.clearDisplay();
    
    // Header
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(37, 0);
    display.println("SENSITIVE");
    
    // Compás Gigante
    display.setTextSize(4);
    display.setCursor(55, 20);
    display.print(beatCount); 

    // BPM Info
    display.setTextSize(1);
    display.setCursor(0, 55);
    display.print("BPM: "); 
    display.print(bpm);
    display.display();

    // Sonido diferenciado
    tone(buzzerPin, (beatCount == 1) ? 1000 : 800, 40);
    
    beatCount = (beatCount >= signature) ? 1 : beatCount + 1;
  }
}
