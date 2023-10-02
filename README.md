# OpenEPaperLink-HA-Weatherman
Wetherman EPaper display using OpenEPaperLink and the HA Integration. Shows current weather state and temperature, the next four hour forecast, and the next four day forecast. Also shows Moon phase.

![Epaper Tag using Weatherman data](/20230925_143005.jpg?raw=true "Example")

Home Assistant and a working [OpenEpaper](https://openepaperlink.de/)https://openepaperlink.de/ setup, with HA Integration - https://github.com/jonasniesner/open_epaper_link_homeassistant

Sensors/Integrations Needed:

https://github.com/jonasniesner/open_epaper_link_homeassistant (Install via HACS)
https://www.home-assistant.io/integrations/met - Weather info
https://www.home-assistant.io/integrations/moon - Moon phases
https://www.home-assistant.io/integrations/sun - Sun sensor, help with weather icons if it's clear at night (so you don't see a Sun at night!)

Add font GothamRnd-Bold.ttf to /config/custom_components/open_epaper_link in Home Assistant.

Add template sensor ha-configuration.yaml to your configuration file in Home Assistant.

Add contents of automation.yaml to a new automation in Home Assistant (Choose Edit in Yaml from top right three dots in a new automation). The automation is using a time template of every 20 minutes - adjust according to taste!
