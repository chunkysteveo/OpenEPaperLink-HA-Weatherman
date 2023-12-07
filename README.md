# OpenEPaperLink-HA-Weatherman
Weatherman EPaper display using OpenEPaperLink and the HA Integration. Shows current weather state and temperature, the next four hour forecast, and the next four day forecast. Also shows Moon phase. Based heavily from https://github.com/Madelena/esphome-weatherman-dashboard, of which I have been a contributer, and made my own ESPHome based version (code somewhere?!!).

<img src="20231128_074529_resized.jpg" width="50%" alt="Epaper Tag using Weatherman data - Now with Color!">
<img src="20230925_143005.jpg" width="50%" alt="Epaper Tag using Weatherman data">

Home Assistant and a working [OpenEpaper](https://openepaperlink.de/) setup, with HA Integration - https://github.com/jonasniesner/open_epaper_link_homeassistant

## Sensors/Integrations needed:

* https://github.com/jonasniesner/open_epaper_link_homeassistant (Install via HACS)
* https://www.home-assistant.io/integrations/met - Weather info
* https://www.home-assistant.io/integrations/moon - Moon phases
* https://www.home-assistant.io/integrations/sun - Sun sensor, help with weather icons if it's clear at night (so you don't see a Sun at night!)

The scripts assume your Met.no weather sensor is called `weather.home`, your Moon sensor is called `sensor.moon_phase`. There is a service call to `weather.get_forecasts` at the top of the HA configuration file to get the hourly weather, which calls this data variable `weather_home_hourly`. `get_forecasts` is a different response to `get_forecast`, so make sure you are on HA version 2023.12 or greater - it will break if you use `get_forecast`!

## Tag size - 1.54", 2.9" or 4.2"
I made a 2.9" weather tag (pictured above) but there is also one made for 4.2" by [@svenove](https://github.com/svenove/):
<img src="4.2-tag.jpg" width="50%" alt="4.2 Epaper Tag using Weatherman data">

I've also added a small 1.54" mini version, which shows the current weather, and the following hour and day ahead.
<img src="20231207_130735_resized.jpg" width="50%" alt="1.54 Epaper Tag using Weatherman data">

## Installation
* Add font `GothamRnd-Bold.ttf` to `/config/media` Home Assistant (create the folder "media" too).
* Add template sensor `ha-configuration.yaml` to your configuration file in Home Assistant.
* Add contents of `automation.yaml` (2.9") or `automation-4.2.yaml` (4.2") to a new automation in Home Assistant (Choose "Edit in Yaml" from top right three dots in a new automation). The automation is using a time template of every 15 minutes - adjust according to taste! There is also a condition on the automation to stop updating between 11pm and 6am - to aid in the lifespan of the display. Other checks could be put in place to only allow for updating the display on human presense etc.

## Customizing
### Time format
Time format for the hourly conditions can be formatted using the python function `timestamp_custom()` for, e.g. 12h time - 2 PM, or e.g. 24h time - 14:

`{{ as_timestamp(weather_home_hourly.forecast[0].datetime) | timestamp_custom('%I %p') }}` = 02 PM  
`{{ as_timestamp(weather_home_hourly.forecast[0].datetime) | timestamp_custom('%I') | int }} {{ as_timestamp(weather_home_hourly.forecast[0].datetime) | timestamp_custom('%p') }}` = 2 PM  
`{{ as_timestamp(weather_home_hourly.forecast[0].datetime) | timestamp_custom('%H') }}` = 14

If you go with a 24h hour display (e.g. 14) - amend your automation yaml file `wm_time_0`, `wm_time_1`, `wm_time_2` and `wm_time_3` from `{{ state_attr('sensor.weatherman_data_tag','wm_time_0') | string | upper }}` to `{{ '%0.2d' | format(state_attr('sensor.weatherman_data_tag','wm_time_0')) | string | upper }}` or you will get erorrs as it tries to format a string as a deciaml ('%0.2d').

### Day names
To change the display of the day names to your prefered language, replace these occurences in the configuration with your own:
`{{ "%s" % (["Sun","Mon","Tue","Wed","Thu","Fri","Sat"][as_timestamp(state_attr('weather.home', 'forecast')[0].datetime) | timestamp_custom('%w') | int]) }}`

### Temperature right now
If you have an outdoor temperature sensor available in HASS, you could display that for the current temperature instead of the forecasted temperature.
Replace this with your temperature entity: `state_attr('sensor.weatherman_data_tag','wm_temp_0')`

### Units
The units for temperature, wind speed and precipitation are set in the forecast entity (cog wheel).
You might need to change the units in the automation to (e.g from C to F, mm to in, etc).

### Battery
There is a battery percentage for a tag in the attributes of the "weatherman" sensor (battery_strength_0000028329e608ff) for one of my tags, but this is no longer used - as the tags have a built in low battery sensor icon. You can remove this code in the weatherman template sensor if you wish. Or if you want a more dynamic battery icon - disable the low battery icon in the tag using your OEPL Access Point tag config command, and add in the following code to the automation:

```
- type: icon
          value: "{{ state_attr('sensor.weatherman_data_tag','battery_strength_0000028329e608ff') | string }}"
          x: 0
          y: 0
          size: 16
          color: >
            {% if state_attr('sensor.weatherman_data_tag','battery_strength_0000028329e608ff') == 'battery-low' or state_attr('sensor.weatherman_data_tag','battery_strength_0000028329e608ff') == 'battery-unknown' %}
              red
            {% else %}
              black
            {% endif %}
```

