# Sistema de Control Embebido por Enlace Bluetooth-Serial con Parada de Emergencia en Arduino Uno

Proyecto desarrollado para la asignatura de **Arquitectura del Computador / Sistemas Embebidos** — **Politécnico Grancolombiano**.

## 👥 Integrantes del Equipo
* **Luis Ángel Beltrán Rodríguez**
* **Miguel Ángel Borray Rivera**
* **Nicolas Alejandro Bohorquez Gross**

---

## 🔗 Enlace Directo a la Simulación en Vivo
* **Simulador Wokwi (Proyecto Público):** [Abrir simulación interactiva en Wokwi]https://wokwi.com/projects/481616077862717441

---

## 📋 Descripción de la Arquitectura
El sistema implementa una interfaz de comunicación serial asíncrona (**UART a 9600 baudios**) que emula la recepción de tramas procedentes de un módulo Bluetooth **HC-05 / HC-06** hacia un microcontrolador **ATmega328P (Arduino Uno)**.

A diferencia de implementaciones secuenciales tradicionales, el firmware emplea una **arquitectura orientada a eventos sin bloqueo** (`millis()` e interrupciones externas por hardware `attachInterrupt`), garantizando respuesta determinística en el orden de microsegundos ante eventos críticos de seguridad.

### Mapa de Conexiones (Pinout)
| Pin Arduino | Componente | Función | Tipo de Señal |
| :--- | :--- | :--- | :--- |
| `D0 (RX)` | Módulo BT / Monitor Serial | Recepción de tramas UART (9600 bps) | Entrada Serial |
| `D1 (TX)` | Módulo BT / Monitor Serial | Transmisión de telemetría CSV y ACK | Salida Serial |
| `D2 (INT0)` | Pulsador Rojo (`INPUT_PULLUP`) | Parada de emergencia por hardware (`FALLING`) | Interrupción Externa |
| `D8` | LED Verde + Resistencia 220 Ω | Actuador principal / Carga controlada | Salida Digital |
| `D9` | LED Rojo + Resistencia 220 Ω | Indicador luminoso de alarma de emergencia | Salida Digital |
| `D13` | LED Integrado (`LED_BUILTIN`) | Indicador de estado / Heartbeat del sistema | Salida Digital |
| `D0, D2, D8, D9` | Analizador Lógico Wokwi | Captura de trazas digitales en formato `.vcd` | Instrumentación |

---

## 🎮 Protocolo de Comandos Bluetooth / Serial
Envíe los siguientes caracteres ASCII a través del Monitor Serial (9600 baudios, fin de línea `LF`):

* **`A`** (o `1`): Enciende el actuador principal (`Pin D8 = HIGH`), siempre que no exista una parada de emergencia activa.
* **`B`** (o `0`): Apaga el actuador principal (`Pin D8 = LOW`).
* **`R`**: Restablece el estado de parada de emergencia (`Pin D9 = LOW`) tras verificar condiciones seguras.

---

## 📊 Instrumentación y Pruebas Experimentales (60 segundos)
En la carpeta `/datos_experimentales` se adjuntan los registros obtenidos durante una prueba cronometrada de 60 segundos:
1. **`telemetria_serial_60s.csv`**: Registro tabular con marcas de tiempo, conteo de paquetes RX, latencia de procesamiento UART ($\mu s$) y latencia de respuesta de la interrupción de emergencia ($\mu s$).
2. **`wokwi-logic.vcd`**: Archivo estándar *Value Change Dump* capturado mediante el analizador lógico de 4 canales de Wokwi para verificación de tiempos de conmutación eléctrica.
