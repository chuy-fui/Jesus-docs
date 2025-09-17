# Medición en osciloscopio del periodo y jitter usando ALARM0 (modo µs)
 
## Qué debe hacer
 
Genera una onda cuadrada por toggle de un pin de salida en la ISR de ALARM0, con rearme acumulativo y periodo nominal definido por ti (p. ej., 100 µs–5 ms). Mide con osciloscopio:
 
*   Periodo promedio y tolerancia.
 
*   Jitter pico-a-pico y, si tu equipo lo permite, RMS.
 
*   Describe la configuración del osciloscopio (acoplamiento, escala de tiempo/voltaje, modo de medición). No cambiar la resolución del timer (mantener modo µs).
 
 
``` codigo
// Blink con timer (SDK alto nivel): cambia BLINK_MS para ajustar
#include "pico/stdlib.h"
#include "pico/time.h"
 
#define LED_PIN 0
static const int BLINK_MS = 50;
 
bool blink_cb(repeating_timer_t *t) {
    static bool on = false;
    gpio_put(LED_PIN, on = !on);
    return true; // seguir repitiendo la alarma
}
 
int main() {
    stdio_init_all();
 
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, true);
 
    repeating_timer_t timer;
    // Programa una interrupción periódica cada BLINK_MS:
    add_repeating_timer_ms(BLINK_MS, blink_cb, NULL, &timer);
 
    while (true) {
        // El trabajo "pesado" debería ir aquí (no en la ISR).
        tight_loop_contents();
    }
}
```
Para comprobar el funcionamiento del generador de onda cuadrada mediante el toggle de un pin de salida controlado por la ISR de ALARM0, se realizó la medición con el osciloscopio Tektronix (modelo digital). Se configuró el rearme acumulativo de la alarma con un periodo nominal definido en **50 ms**.
 
En el esquema se muestra la forma de onda obtenida del osciloscopio.
 
## Esquema
![Diagrama del sistema](recursos/osc_ms.jpeg)
 
* **Periodo promedio medido:** 49.94 ms
* **Tolerancia:** aproximadamente ±0.56 ms
* **Jitter pico-a-pico:** ~560 µs
---
* **Acoplamiento:** CC
* **Escala de tiempo:** 20 ms/div y 10 ms/div
* **Escala de voltaje:** 1 V/div.
* **Modo de disparo:** flanco, canal 1, nivel medio de la señal (~1.3 V)
 
tarea 5 ejercicio 1
 
# Comparar jitter/precisión con osciloscopio: modo µs vs modo ciclos
 
## Qué debe hacer
 
Genera una señal por toggle en la ISR de ALARM0, primero en modo µs y luego en modo ciclos de clk_sys (mismo periodo nominal). En ambos casos:
 
*   Usa rearme acumulativo.
 
*   Mantén el resto del código idéntico.
 
*   Con el osciloscopio, mide y registra para cada modo:
 
    - Periodo promedio y desviación respecto al nominal.
    - Jitter pico-a-pico y/o RMS.
    - Compara resultados y discute el compromiso entre resolución de tick y horizonte de programación.
 
## Código modo µs
 
``` codigo
// Blink con timer (SDK alto nivel): cambia BLINK_MS para ajustar
#include "pico/stdlib.h"
#include "pico/time.h"
 
#define LED_PIN 0
static const int BLINK_MS = 50;
 
bool blink_cb(repeating_timer_t *t) {
    static bool on = false;
    gpio_put(LED_PIN, on = !on);
    return true; // seguir repitiendo la alarma
}
 
int main() {
    stdio_init_all();
 
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, true);
 
    repeating_timer_t timer;
    // Programa una interrupción periódica cada BLINK_MS:
    add_repeating_timer_ms(BLINK_MS, blink_cb, NULL, &timer);
 
    while (true) {
        // El trabajo "pesado" debería ir aquí (no en la ISR).
        tight_loop_contents();
    }
}
```
 
## Código modo ciclos
 
``` codigo
// Blink con timer de sistema (bajo nivel): programando ALARM0 e IRQ
#include "pico/stdlib.h"
#include "hardware/irq.h"
#include "hardware/structs/timer.h"
 
#define LED_PIN       0
#define ALARM_NUM     0
 
// Calcula el número de IRQ para esa alarma
#define ALARM_IRQ     timer_hardware_alarm_get_irq_num(timer_hw, ALARM_NUM)
 
static volatile uint32_t next_deadline;   // próximo instante (en us) en 32 bits bajos
// Por defecto el timer cuenta µs (no cambiamos la fuente).
static volatile uint32_t intervalo_us = 50000u;    // periodo en microsegundos
 
void on_alarm_irq(void) {
    // 1. Limpiar el flag de la alarma
    hw_clear_bits(&timer_hw->intr, 1u << ALARM_NUM);
 
    // 2. Hacer el trabajo toggle LED
    sio_hw->gpio_togl = 1u << LED_PIN;
 
    // 3. Rearmar la siguiente alarma con "deadline acumulativo"
    next_deadline += intervalo_us;
    timer_hw->alarm[ALARM_NUM] = next_deadline;
}
 
int main() {
    stdio_init_all();
 
    // Configura el LED
    gpio_init(LED_PIN);
    gpio_set_dir(LED_PIN, true);
 
    // "now" = 32 bits bajos del contador (tiempo en µs)
    uint32_t now_us = timer_hw->timerawl;          // lectura 32b (low) del contador
    next_deadline = now_us + intervalo_us;         // primer deadline
 
    // Programa la alarma
    timer_hw->alarm[ALARM_NUM] = next_deadline;
 
    // Crea un handler exclusivo para ligar el callback a la IRQ de la alarma
    irq_set_exclusive_handler(ALARM_IRQ, on_alarm_irq);
    // Habilita dentro del periférico TIMER la fuente de interrupción para la alarma ALARM_NUM inte = interrupt enable
    hw_set_bits(&timer_hw->inte, 1u << ALARM_NUM);
    //Habilita la IRQ en el NVIC (controlador de interrupciones del núcleo)
    irq_set_enabled(ALARM_IRQ, true);
 
    while (true) {
        // Mantén el bucle principal libre; lo pesado va aquí, no en la ISR
        tight_loop_contents();
    }
}
```
 
Se generó una señal cuadrada mediante el toggle de un pin en la ISR de ALARM0, empleando rearme acumulativo. Se programó el mismo periodo nominal en dos configuraciones:
 
1. Modo µs (resolución en microsegundos).
 
2. Modo ciclos de clk_sys (resolución en ciclos de reloj).
 
En ambos casos se mantuvo idéntico el resto del código.
 
## Esquema modo µs
![Diagrama del sistema](recursos/osc_ms.jpeg)
 
## Esquema modo ciclos
![Diagrama del sistema](recursos/osc_ciclos.jpeg)
 
### Resultado de medición con osciloscopio
 
1. **Modo µs**
    * **Periodo nominal configurado:** 50 ms
    * **Periodo promedio medido:** 49.94 ms
    * **Desviación:** ~1.1%
    * **Jitter pico-a-pico:** ~560 µs
---
2. **Modo ciclos**
    * **Periodo nominal configurado:** 50 000 (Periodo en ms)
    * **Periodo promedio medido:** 49.96 ms
    * **Desviación:** ~0.08%
    * **Jitter pico-a-pico:** ~14 µs
 
En modo µs, el temporizador está limitado por la resolución del tick de microsegundo. Esto produce un jitter más grande. La ventaja es que permite programar periodos largos sin desbordar el contador.
 
En modo ciclos, la resolución es mucho más fina. El resultado es un jitter significativamente menor y una desviación más pequeña respecto al periodo nominal. Sin embargo, el horizonte de programación es más corto, el contador puede desbordarse más rápido si se intentan programar retardos largos únicamente con ciclos.
 
tarea 5 ejercicio 2
 
# Cuatro alarmas / LEDs a distintas frecuencias
 
## Qué debe hacer
 
Configurar ALARM0..ALARM3 del timer de sistema en modo µs. Cada alarma controla un LED distinto con un periodo propio.
 
``` codigo
#include "pico/stdlib.h"
#include "hardware/irq.h"
#include "hardware/structs/timer.h"
 
#define LED_PIN0      0
#define LED_PIN1      1
#define LED_PIN2      2
#define LED_PIN3      3
 
 
#define ALARM_NUM0     0  // usaremos la alarma 0
#define ALARM_NUM1     1
#define ALARM_NUM2     2
#define ALARM_NUM3     3
 
// Calcula el número de IRQ para esa alarma
#define ALARM0_IRQ     timer_hardware_alarm_get_irq_num(timer_hw, ALARM_NUM0)
 
static volatile uint32_t next_deadline[4];   // próximo instante (en us) en 32 bits bajos
// Por defecto el timer cuenta µs (no cambiamos la fuente).
static volatile uint32_t intervalo_us[4]=  { // periodo en microsegundos
    50000u,
    100000u,
    150000u,
    200000u  
};
 
void on_alarm_irq(void) {
    if (timer_hw->intr & (1u << ALARM_NUM0)) {
        hw_clear_bits(&timer_hw->intr, 1u << ALARM_NUM0);
        sio_hw->gpio_togl = 1u << LED_PIN0;
        next_deadline[0] += intervalo_us[0];
        timer_hw->alarm[ALARM_NUM0] = next_deadline[0];
    }
 
    if (timer_hw->intr & (1u << ALARM_NUM1)) {
        hw_clear_bits(&timer_hw->intr, 1u << ALARM_NUM1);
        sio_hw->gpio_togl = 1u << LED_PIN1;
        next_deadline[1] += intervalo_us[1];
        timer_hw->alarm[ALARM_NUM1] = next_deadline[1];
    }
 
    if (timer_hw->intr & (1u << ALARM_NUM2)) {
        hw_clear_bits(&timer_hw->intr, 1u << ALARM_NUM2);
        sio_hw->gpio_togl = 1u << LED_PIN2;
        next_deadline[2] += intervalo_us[2];
        timer_hw->alarm[ALARM_NUM2] = next_deadline[2];
    }
 
    if (timer_hw->intr & (1u << ALARM_NUM3)) {
        hw_clear_bits(&timer_hw->intr, 1u << ALARM_NUM3);
        sio_hw->gpio_togl = 1u << LED_PIN3;
        next_deadline[3] += intervalo_us[3];
        timer_hw->alarm[ALARM_NUM3] = next_deadline[3];
    }
}
 
int main() {
    stdio_init_all();
 
    // Configura el LED
    gpio_init(LED_PIN0);
    gpio_set_dir(LED_PIN0, true);
    gpio_init(LED_PIN1);
    gpio_set_dir(LED_PIN1, true);
    gpio_init(LED_PIN2);
    gpio_set_dir(LED_PIN2, true);
    gpio_init(LED_PIN3);
    gpio_set_dir(LED_PIN3, true);
 
    // "now" = 32 bits bajos del contador (tiempo en µs)
    uint32_t now_us = timer_hw->timerawl;          // lectura 32b (low) del contador
   
    next_deadline[0] = now_us + intervalo_us[0];
    next_deadline[1] = now_us + intervalo_us[1];
    next_deadline[2] = now_us + intervalo_us[2];
    next_deadline[3] = now_us + intervalo_us[3];      
 
    // Programa la alarma
    timer_hw->alarm[ALARM_NUM0] = next_deadline[0];
    timer_hw->alarm[ALARM_NUM1] = next_deadline[1];
    timer_hw->alarm[ALARM_NUM2] = next_deadline[2];
    timer_hw->alarm[ALARM_NUM3] = next_deadline[3];
 
    // Crea un handler exclusivo para ligar el callback a la IRQ de la alarma
    irq_set_exclusive_handler(ALARM0_IRQ, on_alarm_irq);
 
    // Habilita dentro del periférico TIMER la fuente de interrupción para la alarma ALARM_NUM inte = interrupt enable
    hw_set_bits(&timer_hw->inte, 1u << ALARM_NUM0);
    hw_set_bits(&timer_hw->inte, 1u << ALARM_NUM1);
    hw_set_bits(&timer_hw->inte, 1u << ALARM_NUM2);
    hw_set_bits(&timer_hw->inte, 1u << ALARM_NUM3);
 
   //Habilita la IRQ en el NVIC (controlador de interrupciones del núcleo)
    irq_set_enabled(ALARM0_IRQ, true);
 
    while (true) {
        // Mantén el bucle principal libre; lo pesado va aquí, no en la ISR
        tight_loop_contents();
    }
}
 
```
## Esquemático
![Diagrama del sistema](recursos/esquematico5.png)
 
## Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/mF6rjDa452A?si=MJhPKmeo25mgGrqz" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> 
YouTube
 