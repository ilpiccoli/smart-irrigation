# Smart Irrigation V1.0
(English version - Versione in Italiano sotto)

I've been looking for weeks for a good and simple automation to water my plants on my terrace. I've been using a classic battery-powered irrigation control unit and it worked smoothly but it had two main downsides: 
1) It could not stop if it was raining (or if it rained the day before), so in rainy days water dripped from my terrace (and i was wasting liters of water for nothing);
2) It had limited possibilities in facts of programmability, but above all i had to run water for the same amount of minutes every day, independently from weather conditions.
I found some options but they were either too complex or too grass-oriented (i water my plants for about 1 to 3 minutes per day, while those programs suggested me to water for more than 10 minutes/day), so i developed myself a very simple and customizable automation in HomeAssistant.

# Pre-Requisites
- I use this automation to control my Shelly 1 connected to a normally closed solenoid-valve, when i turn on the Shelly the valve opens. You would need some sort of switch connected to homeassistant you can turn on/open, as you can see it's very simple to modify and adapt this automation to your necessity, as it is straightforward;
- **OpenWeatherMap integration configured and working**, you just need to register and get an API, it's very easy, for any information you can [refer to the integration page](https://www.home-assistant.io/integrations/openweathermap/) ;
- You would need some helpers, in particular:
  - **An _input_number_ to determine the default duration of irrigation** (in my case it's called _input_number.durata_irrigazione_);
  - **An _input_number_ that will be used to record the highest temperature of today** (in my case it's called _input_number.temperatura_massima_oggi_ and it has step 1);
  - **An _input_number_ that will be used to record the highest temperature of yesterday** (in my case it's called _input_number.temperatura_massima_ieri_ and it has step 1);
  - **An _input_number_ that will be used to record the rain forecast for today** (in my case it's called _input_number.pioggia_oggi_ and it has step 0.1);
  - **An _input_number_ that will be used to record the rain precipitation of yesterday** (in my case it's called _input_number.pioggia_ieri_ and it has step 0.1).
- You need **an _history_stats_ sensor that will be used in the notification** to let you know for how long the switch has been powered on (in my case it's called _sensor.irrigazione_terrazzo_oggi_).

# Configuration
0) You need to have the Shelly connected to HomeAssistant (in my case it's called _switch.irrigazione_terrazzo_) and the OpenWeatherMap integration configured;
1) You need to copy _**sensori_history_stats.yaml**_ into your sensor folder (if you [splitted your configuration](https://www.home-assistant.io/docs/configuration/splitting_configuration/)) , or you can copy its content under the _sensor:_ part in your _configuration.yaml_ file;
2) You need to copy _**auto_irrigazione.yaml**_ into your automation folder, or you can copy its content under the _automations:_ part in your _configuration.yaml_ file;
3) Reboot HomeAssistant;
4) If you have done everything good you should now have a new automation showed and enabled.

# Known Limitations
- **The automation is designed to work with only 1 tap/valve;**
    - *Alternative solution: You may just create a group or add multiple switches before and after the delay to run all the taps at the same time, or you can add another   
    sequence of on-delay-off after the last command (before the notification part).*
    
- **The automation is designed to run between 00:00 and 23:59, so if you start irrigation before 23:59 and stop it after 00:00 the automation won't work correctly;**        
    - *Alternative solution: There are multiple ways to avoid this: you should set the reset automation on a different time and change the _history_stats_ sensor configuration.*    
    
- **The automation is designed to start only 1 time per day;**    
    - *Alternative solution: you can add multiple triggers to trigger it based on various events or on multiple hours.*  
    
- **The automation is designed to start when sun is over 35°, as my terrace is west-oriented and the sun hits the plants when the sun elevation is >40°;**    
    - *Alternative solution: you can choose another trigger type, for example based on time, on event or any other trigger type.*
