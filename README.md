# IFTTT Dash Button
Push a button, do a POST (or GET) Request.

## Features
 - [X] POST/GET Requests
    - [X] Trigger Action
    - [X] Monitor Battery
 - [X] Power Saving
 - [X] Reconfigurable
 
## About
Forked from https://github.com/Luigi-Pizzolito/IFTTT-Dash-Button. Power Latch idea originally by Rui Santos at https://randomnerdtutorials.com but I wanted to use an off-the-shelf component, namely the "Flip-Flop Latch Bistable Self-locking Trigger Switch" available on Aliexpress.

### GET Requests
When the button is pushed a POST or GET request is made to a webpage.
#### Triggering Actions
Depending on the webpage, many different actions can be triggered by the button. I use mine to toggle a ESPHome/Tasmota switch.
#### Monitoring Battery
When a request is made, if the setting is set, the button will also pass on the battery voltage with your web request. This way you can monitor the battery's charge. The server will recieve:
 > yoururl.com/yourrequest/_?VCC_Param.=_**VCC_Voltage**

### Power Saving
To keep the button down to a small size, a small battery needs to be used. Pressing the button activates the latch and allows power. The ESP reboots, makes a POST/GET request and then activates the latch to cut power again.

### Configuration
You dont need to take apart your button to re-program the url or action. If you connect `GPIO_03[RX]` to `GND` during startup the button will enter configuration mode. Then you can
1. Connect to 'ESP_Button' WiFi Access Point, with the password 'wifibutton'
2. Visit http://192.168.4.1 to open the configuration page
3. After setting your values, click on the 'Save' button then the 'Restart'
![Configuration Interface](https://luigi-pizzolito.github.io/Gangster45671.github.io/IFTTT-Dash-Button/pictures/Config.png)

## Useful Links and References
- An off-the-shelf power latch:
    - [Aliexpress, Tested and works!] https://www.aliexpress.com/item/33054170454.html
    - [Amazon, Untested] https://www.amazon.com/Bistable-flip-Flop-Circuit-Trigger-Power-Off/dp/B09464R4VV
- Similiar Projects
    - [Bitluni's DashButton] (https://github.com/bitluni/wifiButton)
- ESP Info
    - Pinouts
        - [ESP-12 Pinout](https://esp8266.github.io/Arduino/versions/2.0.0/doc/esp12.png)
        - [ESP-01 Pinout](https://os.mbed.com/media/uploads/sschocke/esp8266-pinout_etch_copper_top.png)
    - General Guides
        - [Sparkfun Guide](https://learn.sparkfun.com/tutorials/esp8266-thing-hookup-guide/using-the-arduino-addon)
        - [Excellent Beginner's Guide](https://github.com/tttapa/ESP8266)
- GitHub Markdown Info
    - [Formatting and Syntax](https://help.github.com/articles/basic-writing-and-formatting-syntax/)
    - [Emoji Cheat Sheet](https://www.webpagefx.com/tools/emoji-cheat-sheet/)
