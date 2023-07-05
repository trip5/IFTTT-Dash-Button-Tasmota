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

## Supplies

- An off-the-shelf power latch:
    - [Latch - Aliexpress, Tested](https://www.aliexpress.com/item/33054170454.html)
    ![Latch](Latch.jpg?raw=true)
    - [Latch - Amazon, Untested](https://www.amazon.com/Bistable-flip-Flop-Circuit-Trigger-Power-Off/dp/B09464R4VV)
    - [Tiny enclosure - Aliexpress](https://www.aliexpress.com/item/1005003942772806.html)
    - [Small enclosure - Aliexpress](https://www.aliexpress.com/item/1005004466963855.html)
    - [Battery to fit tiny enclosure - Aliexpress](https://www.aliexpress.com/item/1005004468895961.html)

## Diagram

![ArduinoDashButton](ArduinoDashButton.png?raw=true)

## Useful Links
- Similiar Projects
    - [Bitluni's DashButton](https://github.com/bitluni/wifiButton)
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


## Final Thoughts (Tasmota!)
- I updated this github to show what actually worked but I never completed this project partly because I kept ruining batteries trying to squeeze everything into the tiny enclosure (including a charge circuit with a USB port).  Also, I think this type of project could be done just as well using vanilla Tasmota.
- You can still use almost the same circuit as above but because you can just connect the D1 to a computer, there's no need for a config button.  Once connected to a steady power source, the latch can't cut the power anyways.
- The 100K resistor in the diagram below is NOT needed.  It just allows the latch to interrupt Tasmota from booting if you should change your mind and decide to shut off the D1.  I think perhaps the ESP8266 is pulling the circuit high on boot, and the button then has no effect.  Without the 100K, the latch circuit will still operate just fine but it's up to Tasmota to shut off the power.

![TasmotaDashButton](TasmotaDashButton.png?raw=true)

The Template:
```
{"NAME":"DashButton","GPIO":[0,0,0,0,256,288,0,0,0,0,0,0,0,0],"FLAG":0,"BASE":18}
```

Needed console options:
```
Backlog SwitchMode1 2; SetOption13 0; SetOption63 0; SetOption114 1; WifiConfig 7; PowerOnState 0; SetOption65 1;
```

Rule1 (contains boot commands activates Rule2, deactivates Rule3:
```
Rule1 ON Power1#Boot DO Backlog LedPower1 1; Var1 0; Delay 20; Rule2 On; Rule3 Off; RuleTimer1 30 ENDON ON System#Boot DO Backlog LedPower1 0; Var1 1 ENDON
```

Rule2 (contains the WebQuery loop, activates Rule3 when ready to shutdown - change the WebQuery as needed):
```
Rule2 ON Var1#State==1 DO Backlog WebQuery http://192.168.1.82/cm?cmnd=Power%20Toggle POST ENDON ON WebQuery#Data=Done DO Backlog Var1 4 ENDON ON WebQuery#Data$!Done DO Backlog Var1 2 ENDON ON Var1#State==2 DO Backlog LedPower1 0; Delay 20; LedPower1 1; Var1 1 ENDON ON Rules#Timer=1 DO Backlog Var1 3 ENDON ON Var1#State==3 DO Backlog Rule3 On; LedPower 0; LedPower 1; LedPower 0; LedPower 1; LedPower 0; LedPower 1; LedPower 0; Var1 5 ENDON ON Var1#State==4 DO Backlog Rule3 On; LedPower 0; LedPower 1; LedPower 0; Var1 5 ENDON
```

Rule3 (deactivates Rule2 to prevent looping due to Webquery delays and does shutdown):
```
Rule3 ON Var1#State==5 DO Backlog Rule2 Off; Power1 0; Delay 5; Power1 1; Delay 5; Power1 0 ENDON
```

Var1 Values:
```
0 Boot [led on]
1 Wi-fi connected [led off], attempting action [led on]
2 Action Failed (delay 2s, try again) [off 2s]
3 Total Failure & shutdown [off; on; off; on; off; on; off (quick)]
4 Success [off; on; off (quick)]
5 Shutdown
```

## Adding a TP4056 Charger

I can't verify 100% this works or is safe but it seems to work...?  This allows charging of the Battery via the D1 Mini port and still allow the D1 to be flashed via PC.  Of course, the Dash button will come online and execute an action while being charged and the button would be completely useless to stop it.  It would probably work for the Arduino code as well.

![TasmotaDashButton](TasmotaDashButton-w-TP4056.png?raw=true)
