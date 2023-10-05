# OpenEPaperLink-HA-Weatherman
Weatherman EPaper display using OpenEPaperLink and the HA Integration. Shows current weather state and temperature, the next four hour forecast, and the next four day forecast. Also shows Moon phase.

![Epaper Tag using Weatherman data](/20230925_143005.jpg?raw=true "Example")

Home Assistant and a working [OpenEpaper](https://openepaperlink.de/)https://openepaperlink.de/ setup, with HA Integration - https://github.com/jonasniesner/open_epaper_link_homeassistant

Sensors/Integrations Needed:

*https://github.com/jonasniesner/open_epaper_link_homeassistant (Install via HACS)
*https://www.home-assistant.io/integrations/met - Weather info
*https://www.home-assistant.io/integrations/moon - Moon phases
*https://www.home-assistant.io/integrations/sun - Sun sensor, help with weather icons if it's clear at night (so you don't see a Sun at night!)

When you have installed the Weather integration, go to it via Settings, Integrations, and Enable the hourly forecast (it's disabled by default).

Add font GothamRnd-Bold.ttf to /config/custom_components/open_epaper_link in Home Assistant.

Add template sensor ha-configuration.yaml to your configuration file in Home Assistant.

Add contents of automation.yaml to a new automation in Home Assistant (Choose Edit in Yaml from top right three dots in a new automation). The automation is using a time template of every 20 minutes - adjust according to taste! There is also a condition on the automation to stop updating between 10pm and 7am - to aid in the lifespan of the display. Other checks could be put in place to only allow for updating the display on human presense etc.

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

