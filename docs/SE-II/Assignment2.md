# RTOS Basics with ESP-IDF LAB

This session focused on developing multiple skills in ESP-IDF using FreeRTOS, including task creation, correct use of blocking mechanisms, inter-task communication through queues, and protection of shared resources with mutexes.

## 1. Activity goals

By the end, you should be able to:

Create multiple FreeRTOS tasks in ESP-IDF.
- Use blocking correctly (vTaskDelay, waiting on a queue/mutex) so tasks don't hog the CPU.
- Pass data between tasks using a queue (producer/consumer pattern).
- Protect shared resources using a mutex (avoid race conditions).

## 2. Analysis

## 2.1 Lab 1 — Two tasks, delays, priorities

``` c
#include 
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "driver/gpio.h"
#include "esp_log.h"

#define LED_GPIO GPIO_NUM_2   // CHANGE for your board

static const char *TAG = "LAB1";

static void blink_task(void *pvParameters)
{
    gpio_reset_pin(LED_GPIO);
    gpio_set_direction(LED_GPIO, GPIO_MODE_OUTPUT);

    while (1) {
        gpio_set_level(LED_GPIO, 1);
        vTaskDelay(pdMS_TO_TICKS(300));
        gpio_set_level(LED_GPIO, 0);
        vTaskDelay(pdMS_TO_TICKS(300));
    }
}

static void hello_task(void *pvParameters)
{
    int n = 0;
    while (1) {
        ESP_LOGI(TAG, "hello_task says hi, n=%d", n++);
        vTaskDelay(pdMS_TO_TICKS(1000));
    }
}

void app_main(void)
{
    ESP_LOGI(TAG, "Starting Lab 1 (two tasks)");

    // Stack size in ESP-IDF FreeRTOS is in BYTES
    xTaskCreate(blink_task, "blink_task", 2048, NULL, 5, NULL);
    xTaskCreate(hello_task, "hello_task", 2048, NULL, 2, NULL);
}
```

1. Priority experiment: change hello_task priority from 5 to 2.
2. Does behavior change? Why might it (or might it not)?
No behavioural changes, theorized to be because the task dellays allow for both tasks to work
3. Starvation demo: temporarily remove vTaskDelay(...) from hello_task.
4. What happens to blinking?
The hello task runs much faster but the blink task remains unchanged.
5. Put the delay back and explain in one sentence why blocking helps.
Blocking lets our task voluntarily give up the CPU so other tasks get scheduled and executed.

## 2.2 Lab 2 — Queue: producer/consumer

``` c
#include 
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"
#include "freertos/queue.h"
#include "esp_log.h"

static const char *TAG = "LAB2";
static QueueHandle_t q_numbers;

static void producer_task(void *pvParameters)
{
    int value = 0;

    while (1) {
        value++;

        // Send to queue; wait up to 50ms if full
        if (xQueueSend(q_numbers, &value, pdMS_TO_TICKS(50)) == pdPASS) {
            ESP_LOGI(TAG, "Produced %d", value);
        } else {
            ESP_LOGW(TAG, "Queue full, dropped %d", value);
        }

        vTaskDelay(pdMS_TO_TICKS(20));
    }
}

static void consumer_task(void *pvParameters)
{
    int rx = 0;

    while (1) {
        // Wait up to 1000ms for data
        if (xQueueReceive(q_numbers, &rx, pdMS_TO_TICKS(1000)) == pdPASS) {
            ESP_LOGI(TAG, "Consumed %d", rx);
        } else {
            ESP_LOGW(TAG, "No data in 1s");
        }
    }
}

void app_main(void)
{
    ESP_LOGI(TAG, "Starting Lab 2 (queue)");

    q_numbers = xQueueCreate(20, sizeof(int)); // length 5
    if (q_numbers == NULL) {
        ESP_LOGE(TAG, "Queue create failed");
        return;
    }

    xTaskCreate(producer_task, "producer_task", 2048, NULL, 5, NULL);
    xTaskCreate(consumer_task, "consumer_task", 2048, NULL, 5, NULL);
}
```

1. Make the producer faster: change producer delay 200ms → 20ms.
2. When do you see “Queue full”?
The queue never filled up
3. Increase the queue length 5 → 20.
4. What changes?
No changes
5. Make the consumer “slow”: after a successful receive, add:
vTaskDelay(pdMS_TO_TICKS(300));
6. What pattern is happening now (buffering / backlog)?
We started losing data, this is because data is being generated and transmitted quicker than we can receive it.

