# Fluorometer

### Firmware Design Approach
By adjusting the PID control loop to run at a higher frequency and ensuring it has a high priority in the FreeRTOS scheduler, we can meet the fast step response requirement. The ADC data capture remains a high-priority task on Core 0, while less critical tasks, such as IoT data upload, run on Core 1 at lower priority. This approach leverages the dual-core architecture effectively, ensuring real-time performance and overall system functionality.

### 1. System Overview and Task Prioritization
- **Temperature Control (PID Loop)**:
  - Requires high frequency due to fast step response.
  - Will need to run at a higher priority.
- **ADC Data Capture**:
  - High-priority, real-time task.
- **Data Upload to IoT Dashboard**:
  - Less time-critical.

### 2. Adjusting Task Scheduling
To accommodate the fast response of the PID loop, we'll run it at a higher frequency (e.g., 1 kHz or higher) and ensure it has a high priority in the FreeRTOS scheduler. We'll still utilize the dual-core architecture to separate real-time tasks from less critical tasks.

### 3. Task Implementation Details

#### 3.1. Fast PID Control Loop
- **Frequency**: 1 kHz (every 1 ms) or higher.
- **Implementation**: High-priority FreeRTOS task on Core 1.


#### 3.2. ADC Data Capture
- **Frequency**: Triggered by a hardware timer interrupt.
- **Time Constraint**: Capture within 40 microseconds.
- **Implementation**: Use hardware timer and ISR to handle ADC data capture. This will run on Core 0.


#### 3.3. IoT Data Upload
- **Frequency**: Periodic (e.g., every minute).
- **Implementation**: Use FreeRTOS tasks to handle HTTP/MQTT communication. Run this task on Core 1.


### 4. System Initialization
Initialize the tasks and hardware in the Main function:

### 5. Task Synchronization and Data Integrity
- Use of proper synchronization mechanisms (e.g., mutexes and semaphores) to ensure data consistency when ADC data is accessed by multiple tasks.
- Ensure ISR routines are as short as possible and use volatile variables for shared data.

