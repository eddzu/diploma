

### Overview: Detecting Pessimism Using Mobile Sensing and Large Language Models
The mobile application will collect sensor data (such as physical activity, location, and phone usage) and **scrape on-screen text**. Additionally, the application will deploy experience sampling questionnaires to measure the user’s current level of pessimism (it is necessary to determine the most suitable test, likely something similar to existing validated measures). The application will also integrate a lightweight LLM for on-device inference, which may require fine-tuning to achieve optimal performance. A user study will be conducted with at least 20 participants over a period of 2–3 weeks.

#### ESM + LLM
The app combines automated mobile sensing with ESM surveys to track users' mental states throughout the day. ESM sends short questions at random times to measure mood and well-being in real-time.
Instead of using a traditional classifier (which struggles with sensor data), we use a Large Language Model (LLM) to analyze on-screen text. The idea is that LLMs can better understand emotions from text than from raw sensor data.

#### Motivation: Privacy-Preserving Mental State Tracking
The goal is to create a privacy-friendly way to track mental states. If the model runs on-device, it can detect emotional trends without sending raw data to external servers. This could help therapists get insights into a person's well-being without seeing their actual activities.


## AWARE Framework

[AWARE](https://www.awareframework.com/) is a mobile sensing framework that collects data from smartphone sensors for research. It allows passive data collection and integrates with surveys to gather user-reported insights.

We design our own study via their [configurator](https://www.awareframework.com/configurator/). We use only the ScreenText sensor, which captures text displayed on the screen. Additionally, we configure ESM (Experience Sampling Method) surveys to ask users about their well-being at scheduled times. Surveys are triggered at specific times during the day to match collected text data with self-reported well-being.

To deploy the study on a user’s phone, we install the AWARE app and enroll the user in the study using a study URL or QR code. The app runs in the background, collecting screen text and sending survey prompts.

We set up a MySQL database to store collected data. The AWARE app is configured to send sensor data and survey responses directly to this database, allowing further analysis.

## ScreenText Sensor 
#### How it works
The screentext sensor captures all visible text on a smartphone screen by periodically taking screenshots and extracting text using **OCR (Optical Character Recognition)**. It also records the coordinates of the detected text, mapping it to its position on the screen. The sensor leverages Android's accessibility services to monitor UI changes and detect when new text appears. Unlike keyloggers, it does not track typed input but only reads what is displayed, enabling studies on app usage, digital behavior, and mental state analysis while preserving privacy. The full paper on the sensor can be found [here](https://dl.acm.org/doi/10.1145/3613904.3642347).

#### Data structure
The **screentext** sensor captures the following columns:

- **_id**: A unique identifier for each entry.
- **timestamp**: The exact time when the data was recorded, in milliseconds since 1970.
- **device_id**: A unique identifier for the device capturing the data.
- **class_name**: The name of the widget class associated with the captured text.
- **package_name**: The application package name currently in the foreground.
- **text**: The text displayed on the screen, structured with its coordinates. 
  - Each "text pack" combines text and its coordinates, separated by `||`.  
  - Example:  
    ```
    Privacy StatementRect(129, 1802 – 330, 1601)||
    Website Terms and ConditionsRect(346, 1802 – 684, 1601)
    ```
- **user_action**: The type of user action detected:
  - `0` = ACTION_DOWN
  - `1` = ACTION_UP
  - `2` = ACTION_MOVE
  - `3` = ACTION_CANCEL
- **event_type**: The type of event triggered by the user:
  - `1` = TYPE_VIEW_CLICKED
  - `2048` = TYPE_WINDOW_CONTENT_CHANGED
  - `4096` = TYPE_VIEW_SCROLLED
  - `32` = TYPE_WINDOW_STATE_CHANGED
  - `64` = CONTENT_CHANGE_TYPE_STATE_DESCRIPTION

This structure allows us to analyze user interactions by correlating on-screen text with specific user actions and events.

### ESM 
**Ideal scale:**
- Short and quick to minimize participant burden.
- Allow frequent daily measurements to capture fluctuations effectively.
- Specifically measure pessimism or closely related emotional states (e.g., mood, anxiety, negativity).
- Produce results suitable as input for the LLM, enabling accurate inference of emotional trends.

Some options:
- [Beck Hopelessness Scale](https://www.carepatron.com/files/beck-hopelessness-scale.pdf) -> Too long, subset(?)
- [LOT-R](https://www.cmu.edu/dietrich/psychology/pdf/scales/LOTR_Scale.pdf)

Maybe questions that link mood to the screen text they saw on the screen? We scrape the text then every few hrs ask them if the text affected their mood or if it is relevant to their mood, wellbeing, and also ask them about their current mood?


[Social Media and Depression Symptoms: A Network Perspective](https://www.researchgate.net/publication/328261571_Social_Media_and_Depression_Symptoms_A_Network_Perspective)




