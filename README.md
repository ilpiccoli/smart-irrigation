# Smart Irrigation V1.2
(English version - Versione in Italiano [qui](https://github.com/ilpiccoli/smart-irrigation/blob/main/README_ita.md))

I've been looking for weeks for a good and simple automation to water my plants on my terrace. I've been using a classic battery-powered irrigation control unit and it worked smoothly but it had two main downsides: 
1) It could not stop if it was raining (or if it rained the day before), so in rainy days water dripped from my terrace (and i was wasting liters of water for nothing);
2) It had limited possibilities in facts of programmability, but above all i had to run water for the same amount of minutes every day, independently from weather conditions.
I found some options but they were either too complex or too grass-oriented (i water my plants for about 1 to 3 minutes per day, while those programs suggested me to water for more than 10 minutes/day), so i developed myself a very simple and customizable automation in HomeAssistant.

# Pre-Requisites
- I use this automation to control my [Shelly 1](https://amzn.to/3kRymqb) connected to a [normally closed solenoid-valve](https://amzn.to/3kRymqb), when i turn on the Shelly the valve opens. You would need some sort of switch connected to homeassistant you can turn on/open, as you can see it's very simple to modify and adapt this automation to your necessity, as it is straightforward;
- **OpenWeatherMap integration configured and working**, you just need to register and get an API, it's very easy, for any information you can [refer to the integration page](https://www.home-assistant.io/integrations/openweathermap/) ;
- You would need some helpers, in particular:
  - **An _input_number_ to determine the default duration of irrigation** (in my case it's called _input_number.durata_irrigazione_);
  - **An _input_number_ that will be used to record the highest temperature of today** (in my case it's called _input_number.temperatura_massima_oggi_ and it has step 1);
  - **An _input_number_ that will be used to record the highest temperature of yesterday** (in my case it's called _input_number.temperatura_massima_ieri_ and it has step 1);
  - **An _input_number_ that will be used to record the rain precipitation for today** (in my case it's called _input_number.pioggia_oggi_ and it has step 0.1);
  - **An _input_number_ that will be used to record the rain precipitation of yesterday** (in my case it's called _input_number.pioggia_ieri_ and it has step 0.1).
- You need **an _history_stats_ sensor that will be used in the notification** to let you know for how long the switch has been powered on (in my case it's called _sensor.irrigazione_terrazzo_oggi_).

# Configuration
0) You need to have the Shelly connected to HomeAssistant (in my case it's called _switch.irrigazione_terrazzo_) and the OpenWeatherMap integration configured;
1) You need to copy _**sensori_history_stats.yaml**_ into your sensor folder (if you [splitted your configuration](https://www.home-assistant.io/docs/configuration/splitting_configuration/)) , or you can copy its content under the _sensor:_ part in your _configuration.yaml_ file;
2) You need to copy _**auto_irrigazione.yaml**_ into your automation folder, or you can copy its content under the _automations:_ part in your _configuration.yaml_ file;
3) Reboot HomeAssistant;
4) If you have done everything good you should now have a new automation showed and enabled.

# How does it work?
1) Every day at 00:00 the sensors of "Rain Today" and "Maximum Temperature Today" is reset;
2) Every hour "Rain Today" sensor updates its value adding eventual rain from "Rain Sensor in last hour" from OpenWeatherMap;
3) Every time the temperature sensor changes, if it's higher than the actual value, the "Maximum Temperature Today" sensor is updated;
4) When the trigger condition is met (in my case 2 hours after sunrise) the automation starts;
5) If "Rain Yesterday" is higher than 4mm or "Rain Forecasted for Today" is higher than 4mm the automation **does not run** and a notification is sent;
5b) If "Maximum Temperature Yesterday" is below 17°C the automation **does not run** and a notification is sent;
6) The switch is switched on;
7) The delay counter starts, based on "Maximum Temperature Yesterday", if T>30 it runs for 3 times the standard time, if T>25 for 2x, if T>20 for the standard time, else (if T<20) it runs for half the standard irrigation time;
8) The switch is switched off;
9) A 30s timer starts, to let "history stats" sensor update;
10) The notification is sent to the mobile app.
11) Every day at 23:59 "Rain Yesterday" gets the value from "rain today" sensor" and "Maximum Temperature Yesterday" gets the value from "Maximum Temperature Today"

# Known Limitations
- **The automation is designed to work with only 1 tap/valve;**
    - *Alternative solution: You may just create a group or add multiple switches before and after the delay to run all the taps at the same time, or you can add another   
    sequence of on-delay-off after the last command (before the notification part).*
    
- **The automation is designed to run between 00:00 and 23:59, so if you start irrigation before 23:59 and stop it after 00:00 the automation won't work correctly;**        
    - *Alternative solution: There are multiple ways to avoid this: you should set the reset automation on a different time and change the _history_stats_ sensor configuration.*    
    
- **The automation is designed to start only 1 time per day;**    
    - *Alternative solution: you can add multiple triggers to trigger it based on various events or on multiple hours.*  

# How can you support me?
Buy through the links above, those are Amazon Affiliates Links, i get a percent from the total amount of what you spend (and you do not pay for this!), or [buy me a coffee](https://www.buymeacoffee.com/ilpiccoli)
