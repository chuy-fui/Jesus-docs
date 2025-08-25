# Análisis de microcontroladores enfocado en un proyecto

## **Proyecto**
### **Sistema de reconocimiento de voz básico**
El proyecto consiste en implementar un sistema embebido capaz de reconocer comandos de voz simples, ejemplo: “encender”, “apagar”, “subir”, “bajar”, y ejecutar acciones predefinidas como activar un LED, encender un ventilador, abrir una puerta o controlar dispositivos del hogar.

## **Microcontroladores**
Para elegir los microcontroladores más adecuados para un proyecto de reconocimiento de voz, se priorizó que tuvieran capacidad de procesar audio de una mejor manera, así como memoria suficiente para ejecutar modelos de reconocimiento de voz de manera eficiente. Lo que facilita el desarrollo y permite implementar funcionalidades de forma más rápida y confiable.

Microcontroladores escogidos:

- **ESP32**
- **STM32F407**
- **nRF52840**
- **RP2040**

### **Tabla comparativa**
| Microcontrolador | Arquitectura | Velocidad de trabajo| Memoria | Periféricos | Costo aprox. (USD) | Ecosistema |
|------------------|--------------|---------------------|---------|-------------|--------------------|------------|
| **ESP32** | Xtensa LX6/LX7 dual-core (32-bit) | Hasta 240 MHz | 520 KB SRAM + PSRAM externa | Wi-Fi, Bluetooth, I²S (audio) | $6 | ESP-IDF, Arduino, ESP-SR, TensorFlow Lite Micro |
| **STM32F407** | ARM Cortex-M4F (32-bit, con FPU) | 168 MHz | 1 MB Flash, 192 KB SRAM | I²S, ADC, timers DSP | $9–12 | STM32CubeIDE, CubeMX, CMSIS-DSP, TensorFlow Lite Micro |
| **nRF52840** | ARM Cortex-M4F (32-bit) | 64 MHz | 1 MB Flash, 256 KB RAM | Bluetooth 5 LE, ADC, I²S | $5–6 | nRF Connect SDK, Zephyr RTOS, TensorFlow Lite Micro |
| **RP2040** | ARM Cortex-M0+ dual-core (32-bit) | 133 MHz | 264 KB SRAM + Flash externa | PIO, ADC, USB, I²C/SPI/UART | $4–6 | Pico SDK (C/C++), MicroPython, TensorFlow Lite Micro |

### **Definiciones**
- **Arquitectura:** La arquitectura de un microcontrolador define su estructura funcional y cómo se organizan sus componentes internos para realizar tareas de procesamiento y control.
- **Velocidad de trabajo:** se refiere a la frecuencia a la que el microcontrolador ejecuta instrucciones, medida en hercios (Hz).
- **Memoria:** La memoria de un microcontrolador es un componente esencial que almacena tanto las instrucciones del programa como los datos necesarios para su ejecución. 
- **Perifericos:** Los periféricos para un microcontrolador son circuitos digitales integrados dentro del chip que permiten la interacción con el mundo exterior y la realización de funciones específicas sin necesidad de componentes externos adicionales.
- **Ecosistema:** El ecosistema se refiere al conjunto de elementos y componentes necesarios para su funcionamiento y desarrollo, que van desde el hardware hasta el software y las herramientas de programación.