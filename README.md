# Small HTTP server for NodeMCU
This small webserver supports the following HTTP verbs/methods:
* GET returns the contents of a file in flash
* PUT creates a new file in flash
* DELETE remove the file from flash
* POST executes the given lua script (may return a function that receives the payload)
* OPTIONS returns minimal CORS headers allowing POST from any origin

## Development
This project uses *direnv* and *nix* to install `nodemcu-uploader` and `esptool.py` in a painless way. It works in both Mac Darwin and Linux variants.

Make sure you install nix and direnv first:

* https://nixos.org/download/
* https://direnv.net/docs/installation.html and hook https://direnv.net/docs/hook.html

After installing these, cd into the project directory. Type `direnv allow` to be dropped into a shell with Python and other dependencies available.

## Flash NodeMCU
This project requires NodeMCU (integer version) and needs at least the following modules: `adc`, `file`, `net`, `node`, `pwm`, `timer`, `wifi`. Get it from https://nodemcu-build.com

To flash a new firmware, pull GPIO0 to GND (or press the PROGRAM button, if any) while turning the ESP8266 ON.
This will start the ESP in flash mode. Connect TX and RX to a serial cable and use `esptool.py` to flash the firmware, for example:
```
$ esptool.py write_flash 0x00000 nodemcu-1.5.4.1-final-9-modules-2019-03-12-11-50-48-integer.bin
```
Add `--baud 115200` or `--port /dev/tty.usbmodem14101` (for example) to specify the baudrate and serial device, respectively.

## Installation
Clone the project and edit the Wi-Fi settings in `init.lua`. You can use the shell script `up` or execute the following:
```
$ nodemcu-uploader upload init.lua httpserver.lua rgb.lua index.html
```

## Verification and Debugging
After uploading, connect the serial console (`screen /dev/ttyUSB0 115200` under most unix flavors) and reboot the device by executing `node.restart()`. The device will print its IP address in the console after connecting to the wifi.

To see what files have been uploaded, use the script `ls.lua`. Uploade the script using `nodemcu-uploader` as above. Connect to the serial console, then execute `ls()`.

## Usage
Once those files have been uploaded you can manage your device with `curl`, for example to PUT new files on flash:
```
curl --upload-file example.lua http://serial.console.shows.ip/
```

To reboot your device (for example after uploading a new `init.lua` or `httpserver.lua`) use `curl` to POST anything to `/`:
```
curl --data anything http://serial.console.shows.ip/
```
