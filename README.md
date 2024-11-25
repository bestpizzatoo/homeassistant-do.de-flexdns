# homeassistant-do.de-flexdns

This is for use with FlexDNS and do.de via cURL
Documentation from do.de: https://www.do.de/wiki/flexdns-einrichtung/
particularly "FlexDNS über einen Internet-Browser aktualisieren"

For updating your Domain name to your current IP you need

1. a DNS sensor (that gets your current IP)
2. a Shell Command (for triggering the FlexDNS Update via cURL)
3. an Automation (so that the cURL command is executed when your IP changes)
4. [optional] a Telegram Bot that sends you a status update (even if the update failed!)


## 1. DNS Sensor
   I use the DNS IP Integration.
   Install it from here: https://www.home-assistant.io/integrations/dnsip/
   and follow the setup (Number 1 in particular for your own IP)
   --> this exposes the sensor "myip" (sensor.myip)

## 2. Shell Command
   This integration is already built in in Home Assistant (Doc here: https://www.home-assistant.io/integrations/shell_command/)
   Add this line to your configuration yaml
```   
   shell_command:
      update_ddns: 'curl "{{ states("sensor.ddns_url") + states("sensor.myip") }}"'
```
   and
```
   sensor:
      - platform: template
        sensors:
          ddns_url:
            value_template: !secret ddns_url
```
   Restart your home assistant for reloading your configuration.yaml

   "sensor.ddns_url" will be the URL for FlexDNS. Since it includes credential, it is setup as a secret.
   The secret is parsed to a sensor. This sensor data can then be included in the shell command.
   (Disclaimer: It is possible there's a cleaner solution to write this)

   sensor.myip is your IPv4 IP.

   This is how they look:
   ```
   ddns_url:     "https://ddns.do.de/?username=DDNS-KD000-F000&password=000000000&hostname=*.domain.tld&myip="
   sensor.myip:  "255.255.255.255"
```
   
## 3. Create an Automation that does as follows
  
   When sensor.myip changes state or any attributes

   then do Perform action 'shell_command.update_ddns'

   if the shell command isn't listed yet, restart home assistant for config changes to take effect.
   

   
   
