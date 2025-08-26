# Análisis de microcontroladores enfocado en un proyecto

## **Proyecto**
### Sistema de reconocimiento de voz básico
El proyecto consiste en implementar un sistema embebido capaz de reconocer comandos de voz simples, ejemplo: “encender”, “apagar”, “subir”, “bajar”, y ejecutar acciones predefinidas como activar un LED, encender un ventilador, abrir una puerta o controlar dispositivos del hogar.

## **Elecciones**
Para elegir los microcontroladores más adecuados para un proyecto de reconocimiento de voz, se priorizó que tuvieran capacidad de procesar audio de una mejor manera, así como memoria suficiente para ejecutar modelos de reconocimiento de voz de manera eficiente. Lo que facilita el desarrollo y permite implementar funcionalidades de forma más rápida y confiable.

Microcontroladores escogidos:

- **ESP32**
- **STM32F407**
- **nRF52840**
- **RP2040**

### **Tabla comparativa**
| Microcontrolador | Arquitectura | Velocidad de trabajo| Memoria | Periféricos | Costo aprox. (MX) | Ecosistema |
|------------------|--------------|---------------------|---------|-------------|--------------------|------------|
| **ESP32** | Xtensa LX6/LX7 dual-core (32-bit) | Hasta 240 MHz | 520 KB SRAM + PSRAM externa | Wi-Fi, Bluetooth, I²S (audio) | $150-200 | ESP-IDF, Arduino, ESP-SR, TensorFlow Lite Micro |
| **STM32F407** | ARM Cortex-M4F (32-bit, con FPU) | 168 MHz | 1 MB Flash, 192 KB SRAM | I²S, ADC, timers DSP | $200-250 | STM32CubeIDE, CubeMX, CMSIS-DSP, TensorFlow Lite Micro |
| **nRF52840** | ARM Cortex-M4F (32-bit) | 64 MHz | 1 MB Flash, 256 KB RAM | Bluetooth 5 LE, ADC, I²S | $225-240 | nRF Connect SDK, Zephyr RTOS, TensorFlow Lite Micro |
| **RP2040** | ARM Cortex-M0+ dual-core (32-bit) | 133 MHz | 264 KB SRAM + Flash externa | PIO, ADC, USB, I²C/SPI/UART | $200-260 | Pico SDK (C/C++), MicroPython, TensorFlow Lite Micro |

### **Definiciones**
- **Arquitectura:** La arquitectura de un microcontrolador define su estructura funcional y cómo se organizan sus componentes internos para realizar tareas de procesamiento y control.
- **Velocidad de trabajo:** se refiere a la frecuencia a la que el microcontrolador ejecuta instrucciones, medida en hercios (Hz).
- **Memoria:** La memoria de un microcontrolador es un componente esencial que almacena tanto las instrucciones del programa como los datos necesarios para su ejecución. 
- **Perifericos:** Los periféricos para un microcontrolador son circuitos digitales integrados dentro del chip que permiten la interacción con el mundo exterior y la realización de funciones específicas sin necesidad de componentes externos adicionales.
- **Ecosistema:** El ecosistema se refiere al conjunto de elementos y componentes necesarios para su funcionamiento y desarrollo, que van desde el hardware hasta el software y las herramientas de programación.

## **Comparativa**
El ESP32 sobresale como una de las mejores opciones debido a su equilibrio entre potencia, conectividad y costo. Incorpora Wi-Fi y Bluetooth, y cuenta con librerías específicas para reconocimiento de voz como ESP-SR, además de compatibilidad con TensorFlow Lite Micro. Esto permite un desarrollo rápido y con gran respaldo de la comunidad. El STM32F407, por su parte, ofrece mayor robustez en el procesamiento digital de señales gracias a su unidad de punto flotante (FPU) y un ecosistema profesional, aunque carece de conectividad inalámbrica integrada y presenta un costo más alto.

El nRF52840 resulta atractivo para aplicaciones portátiles o de bajo consumo, ya que integra Bluetooth Low Energy y dispone de suficiente memoria para modelos pequeños de reconocimiento de voz. Sin embargo, su frecuencia de trabajo más baja limita su rendimiento en tiempo real. En contraste, el RP2040 destaca por su bajo precio y amplia comunidad maker, lo que lo hace ideal para prototipado y proyectos educativos, aunque no es el más indicado para aplicaciones de voz intensivas debido a su memoria reducida y falta de conectividad integrada.

## **Conclusión**
El ESP32 es la alternativa más conveniente para este proyecto, pues combina conectividad, soporte específico para reconocimiento de voz y facilidad de implementación. El STM32F407 es recomendable si se busca un mayor control y precisión en el procesamiento de audio, mientras que el nRF52840 es idóneo para aplicaciones portátiles de bajo consumo, y el RP2040 queda como una opción económica para pruebas o entornos de aprendizaje.

