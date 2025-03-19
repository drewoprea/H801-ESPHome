# H801-ESPHome

Here's my ESPHome config for the H801 controller I use with some RGBW Led strips

## Description

I bought a H801 controller from a random importer, powered it with a spare laptop power supply, and connected it to similarly sourced RGBW light strips. 

It's easy to configure ESPHome to work with HomeAssistant, but I didn't see any examples of ways to control a H801 locally in addition to the API.
I added a microswitch to the GPIO0 / Reset / Boot pair of pins to allow local control of the H801.
The single button allows on/off operaton, white / color selection, brightness control (white) and color selection (cycle through 8 colors)

## Getting Started
If you use ESPHome on your H801 already, you should be able to add your network SSID / password / API encryption key and flash your H801 over the air.
If you are starting with a fresh H801, you'll need to connect to the board with a serial adapter and flash the first ESPHome firmware manually.
 
### Dependencies

* An H801 controler:  https://devices.esphome.io/devices/H801-RGBW-LED-Controller
* The ESPHome project: https://esphome.io/guides/getting_started_command_line
* A USB to 3.3V serial adapter, example: https://www.sparkfun.com/sparkfun-ftdi-basic-breakout-3-3v.html
  (Some 3.3V adapters don't supply 3.3V power to the ESP8266. If yours doesn't, you can power the H801 with its usual power supply, don't connect the power pin from the USB adapter. Do remember to connect the ground from the USB adapter!)
* This config uses the ESPHome State Machine Found here: https://github.com/muxa/esphome-state-machine
  There's no separate install required, the config points to the repo, ESPHome makes it work.

### Installing

* Connect to and flash your H801 (my config of any other ESPHome config) https://esphome.io/guides/physical_device_connection
* Download and complete the H801-Light.yaml
* Fill in your API Encryption key. You can use the generation tool here: https://esphome.io/components/api.html  
```
api: 
  encryption:
```
* Fill in your wifi SSID and password
```
wifi:
  ssid: "SSID"
  password: "PASSWORD"
```
* Comment out the manual_ip section: (Add back and modify it with the ip address your DHCP provides for a slight improvemnt in future reboot times.)
```
  manual_ip:
    static_ip: 192.168.1.10
    gateway: 192.168.1.1
    subnet: 255.255.255.0
```

### Executing program

* OTA build and flash with:
```
   C:\Users\username>esphome run H801-Lights.yaml
```
## Help
* This config uses the wire order Blue - Red - Green - Power, becuas that's what was neatest with my cable.
   To use the Blue - Green - Red - Power order as printed on the H801 case sticker, swap the numbers 15 and 13 in the Output part of the config:
```
output:
  - platform: esp8266_pwm
    pin: 15
    frequency: 1000 Hz
    id: pwm_r
  - platform: esp8266_pwm
    pin: 13
    frequency: 1000 Hz
    id: pwm_g
```
* This config disables the automatic reboot if there's no connection to the HomeAssistant API. (Because my use case does not always include using HA) If you are having problems conecting from HA, you might try re-enabling it.
```
api: 
  reboot_timeout: 0s
```
* This config defines a button press between 10ms and 250ms as a short click, and a button press longer then 500ms as a long click. That can be adjusted to your prefrence:
```
    id: button
    on_click:
      min_length: 10ms
      max_length: 250ms
      then:
      - state_machine.transition: CLICK
    on_multi_click:
    - timing:
        - ON for at least 0.5s
```

## Authors

drewoprea  
asmith

## Version History

* 0.1
    * Initial Release

## License

This project is licensed under the GPLv3 License

## Acknowledgment / Useful Links:

* https://devices.esphome.io/devices/H801-RGBW-LED-Controller
* https://github.com/muxa/esphome-state-machine
* https://esphome.io/guides/getting_started_command_line
* https://esphome.io/guides/physical_device_connection
