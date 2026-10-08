<div align="center">

# SISTEMA DE  DETECCIÓN DE CAÍDAS Y MONITOREO BIOMÉDICO PARA ADULTOS MAYORES

[![PlatformIO](https://img.shields.io/badge/PlatformIO-Arduino-orange.svg?style=flat-square&logo=platformio)](https://platformio.org/)
[![ESP32-S3](https://img.shields.io/badge/Hardware-ESP32--S3-blue.svg?style=flat-square&logo=espressif)](https://www.espressif.com/)
[![Raspberry Pi](https://img.shields.io/badge/Gateway-Raspberry%20Pi%204-red.svg?style=flat-square&logo=raspberrypi)](https://www.raspberrypi.org/)
[![MQTT](https://img.shields.io/badge/Protocol-MQTT-green.svg?style=flat-square&logo=mqtt)](https://mqtt.org/)

</div>

---

## 📖 Tabla de Contenidos
- [Acerca del Proyecto](#-acerca-del-proyecto)
- [Arquitectura del Sistema](#️-arquitectura-del-sistema)
- [Flujo de Funcionamiento y Detección](#-flujo-de-funcionamiento-y-detección)
- [Stack Tecnológico](#-stack-tecnológico)
- [Estructura del Repositorio](#-estructura-del-repositorio)
- [Proyección y Trabajos Futuros](#-proyección-y-trabajos-futuros)

---

## 📌 Acerca del Proyecto

**SafePulse** es una solución integral de salud asistencial basada en Internet de las Cosas (IoT) y computación en el borde (*Edge Computing*). Su propósito principal es la **detección automatizada de caídas**, el monitoreo pasivo de constantes vitales y la gestión inteligente de recordatorios médicos en adultos mayores. 

> **Nota de Privacidad:** El sistema prioriza la soberanía y privacidad de los datos mediante procesamiento local, garantizando autonomía operativa incluso ante interrupciones de conectividad externa con la nube.

---

## 🏗️ Arquitectura del Sistema

El proyecto se estructura bajo un modelo descentralizado de tres niveles interconectados:

* **Nivel 1: Dispositivo Portátil (Wearable)**
  * **Hardware:** Placa ESP32-S3 con pantalla táctil IPS de 1.47".
  * **Sensores:** IMU QMI8658 (acelerómetro y giroscopio), barómetro BMP280, sensores biométricos MAX30102 (SpO2/pulso) y MAX30205 (temperatura).
  * **Función:** Captura telemetría de campo y gestiona la interfaz local de cuenta regresiva para cancelación de falsas alarmas.

* **Nivel 2: Servidor Hogareño (Gateway Local)**
  * **Hardware:** Raspberry Pi 4B ejecutando Raspberry Pi OS Lite.
  * **Conectividad:** Enlace BLE permanente con el reloj y red local.
  * **Función:** Aplica un motor de reglas para la fusión de datos multicriterio (cruce cinemático, barométrico y biométrico) para filtrar falsos positivos de forma local.

* **Nivel 3: Estación de Monitoreo Central (Centro de Salud)**
  * **Backend:** Servidor en Node.js con recepción de mensajes por protocolo MQTT.
  * **Frontend:** Panel de control (*Dashboard*) web interactivo para el personal de guardia.
  * **Base de datos:** Almacenamiento relacional (PostgreSQL/MySQL) para historiales médicos y trazabilidad.

---

## 🔄 Flujo de Funcionamiento y Detección

```text
[Lectura IMU: Giroscopio y Acelerómetro]
                   │
                   ▼
[Validación de Cambio de Altura: Barómetro BMP280]
                   │
                   ▼
[Lectura de Sensores Biométricos: Pulso y SpO2]
                   │
                   ▼
[Activación de Alerta Local en el Reloj (Vibración y Cuenta Atrás)]
                   │
         ┌─────────┴─────────┐
         │ Cancelación Manual│ (Sin respuesta / Tiempo agotado)
         ▼                   ▼
    (Falso Positivo)   [Transmisión Inalámbrica BLE al Servidor Hogareño]
                             │
                             ▼
                       [Fusión Multicriterio en Edge (Raspberry Pi)]
                             │
                             ▼
                       [Despacho de Emergencia vía MQTT a Estación Central]
                             │
                             ▼
                       [Atención y Coordinación en Guardia Médica]
