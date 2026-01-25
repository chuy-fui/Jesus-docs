# Task Scheduling and Priorities

## Introduction
This section is focused on identifying and classifying system tasks based on their triggers, timing constraints, blocking behavior, and priority. These activities apply fundamental concepts of task scheduling and real-time system design in a practical context.

## System description
The system is an embedded application running on a microcontroller that executes multiple concurrent functions with different timing requirements. It periodically reads a temperature sensor every 50 ms and transmits the collected data over Wi-Fi every 2 seconds. The system continuously monitors an emergency button to detect critical events and blinks a status LED at a frequency of 1 Hz to indicate normal operation. Additionally, it stores error messages whenever system failures occur. Due to the use of communication and storage operations, some system tasks may involve blocking behavior, making timing management a critical aspect of the system design.

## Task Characteristics and Priority Assignment
| Task Name                  | Trigger (Time/Event) | Periodic or Event-Based | Is it time critical? | Can it block safely? | What happens if its delayed?              | Priority |
|----------------------------|----------------------|--------------------------|----------------------|----------------------|-------------------------------------------|----------|
| Read temperature           | Every 50ms           | Periodic                 | Yes                  | Not-blocked          | Data loss                                 | Medium   |
| Send data                  | Every 2s             | Periodic                 | No                   | Not-blocked          | Data loss or comms corruption              | Medium   |
| Monitor emergency button   | Button pressed       | Event-based              | Yes                  | Not-blocked          | Big boom-boom                             | High     |
| Blink status LED           | Every second         | Periodic                 | No                   | Block                | Blink gets delayed                        | Low      |
| Store error message        | Event – Failure      | Event-based              | No                   | Block                | Error message arrives eventually          | Low      |

### Priority Justification

#### Monitor emergency button — High priority
This task is time-critical and event-based, as it responds to an emergency condition triggered by user input. Any delay could result in severe consequences, making immediate system response essential. Therefore, it must have the highest priority to ensure safety.

#### Read temperature — Medium priority
This task is periodic and time-critical, as delayed execution may lead to loss of sensor data. However, a short delay does not pose an immediate safety risk. For this reason, it is assigned a medium priority to balance responsiveness and system load.

#### Send data — Medium priority
Although this task runs periodically, it is not time-critical. Delays may cause data loss or communication issues but do not directly affect system safety. It is assigned medium priority to ensure regular operation without interfering with more critical tasks.

#### Blink status LED — Low priority
This task is periodic and not time-critical. Delays only affect visual feedback and do not compromise system functionality or safety. Consequently, it is assigned a low priority.

#### Store error message — Low priority
This task is event-based but not time-critical. Delays do not prevent the system from functioning correctly, as error messages can be stored later without significant impact. Therefore, it is assigned a low priority.
