# OpenEPaperLink-HA-Weatherman
Weatherman EPaper display using OpenEPaperLink and the HA Integration. Shows current weather state and temperature, the next four hour forecast, and the next four day forecast. Also shows Moon phase. Based heavily from https://github.com/Madelena/esphome-weatherman-dashboard, of which I have been a contributer, and made my own ESPHome based version (code somewhere?!!).

![Epaper Tag using Weatherman data](/20230925_143005.jpg?raw=true "Example")

Home Assistant and a working [OpenEpaper](https://openepaperlink.de/) setup, with HA Integration - https://github.com/jonasniesner/open_epaper_link_homeassistant

## Sensors/Integrations needed:

* https://github.com/jonasniesner/open_epaper_link_homeassistant (Install via HACS)
* https://www.home-assistant.io/integrations/met - Weather info
* https://www.home-assistant.io/integrations/moon - Moon phases
* https://www.home-assistant.io/integrations/sun - Sun sensor, help with weather icons if it's clear at night (so you don't see a Sun at night!)

## Tag size - 2.9" or 4.2"
I made a 2.9" weather tag (pictured above) but there is also one made for 4.2" by [@svenove](https://github.com/svenove/):
<img src="4.2-tag.jpg" width="50%" alt="4.2 Epaper Tag using Weatherman data">

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

