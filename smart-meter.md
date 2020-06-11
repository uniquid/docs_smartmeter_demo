# Smart Meter

The Meter app is a Python application which can run on a Raspberry Pi with an attached LCD display. The Python application updates the values on the display whereas most of the functionality is provided by a C library, the [meter-fw](https://github.com/uniquid/internal-dev-demos/tree/master/RASP-PI-3B-PLUS/meter-fw) library.

### Hardware Requirements

* Raspberry Pi 3 B+
* Pimoroni GFX HAT. 128x64 pixel monochrome LCD display

### Software Requirements

* Raspian Stretch Lite \([https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/)\)

### Connecting to the RPi using the terminal

Initially, the RPi does not have SSH enabled so you must connect a monitor and keyboard to the RPi. You can then use either the graphical desktop to configure the device or else you can switch to the command-line terminal using the `Control + Alt + F1` key combination. You can switch back to the desktop using the `Control + Alt + F7` key combination.

### Connecting to the RPi using SSH

The RPi has SSH access disabled by default. You can use the `raspi-config` application to enable SSH.  
Full instructions available at [https://www.raspberrypi.org/documentation/remote-access/ssh/](https://www.raspberrypi.org/documentation/remote-access/ssh/)

### Configuring Wifi on the RPi

The Wifi connection of the RPi can be configured using the `raspi-config` application. Steps to configure the Wifi connection:

* Execute the command `sudo raspi-config`
* Choose the option `2 Network Options`
* Choose the option `N2 Wifi`
* Configure the SSID and password.

### Installation

Mount the GFX HAT on top of the GPIO header of the Raspberry Pi.

Install the required fonts:

```bash
sudo apt-get install fonts-freefont-ttf
```

Follow the "Full Install" instructions from the main page of the [Python library's page on GitHub](https://github.com/pimoroni/gfx-hat). This will install all of the required dependencies, download example code to the Raspberry Pi and install the Python package gfxhat for Python3.

#### Build the C Library

Login to the Raspberry Pi and clone the '[dev-demos](https://github.com/uniquid/internal-dev-demos)' repository in the user Pi's home directory.

```bash
git clone https://github.com/uniquid/internal-dev-demos.git
```

Refer to required dependencies in the file [meter-fw/README.md](https://github.com/uniquid/internal-dev-demos/blob/master/RASP-PI-3B-PLUS/meter-fw/README.md). These depend must be installed before you can build the library.

```bash
cd internal-dev-demos/RASP-PI-3B-PLUS/meter-fw
make clone_repo
make
```

#### Create the Configuration

Before starting the application, you must create configuration file `demo.ini` in the user Pi's home directory. 

```bash
cp ~/internal-dev-demos/RASP-PI-3B-PLUS/meter-fw/demo.ini ~/
```

Please edit the configuration to match your infrastructure. Fields which must be updated include:

* mqtt\_address: MQTT broker address for environment
* announce\_topic: mqtt topic used by meter to announce its identity creation
* UID\_appliance: Insight-api service URL
* UID\_registry: Registry service URL
* dashboard\_mqtt\_address:: Dashboard MQTT address
* dashboard\_topic: Dashboard MQTT topic

{% code title="demo.ini" %}
```bash
fake MAC: 1
# name prefix of the device
name_prefix: METER
debug level: 7
# uri of the mqtt broker
mqtt_address: tcp://mqtt.uniquid.co:1883
# announce topic
announce_topic: mpalumbible/announce
# url of the insight-api appliance
UID_appliance: https://ltc-testnet.uniquid.co/insight-lite-api
# url of the registry service
UID_registry: http://34.251.183.100:8060/registry
# confirmation needed to accept the contract
UID_confirmations: 1
# uri of the dashboard mqtt broker
dashboard_mqtt_address: tcp://18.195.160.77:1883
# dashbord topic
dashboard_topic: /smart_meter_1
```
{% endcode %}

#### Configure the Ethernet Port

The Ethernet port must be configured with a static IP address. This is done by adding the following lines to the end of the configuration file `/etc/dhcpcd.conf`.

```text
interface eth0
static ip_address=172.116.152.61/24
```

Reboot the RPi and use the `ifconfig` command to verify the Ethernet port now has this static address. The Ethernet port must be connected to another Ethernet port for the IP address to be visible.

#### Start the application

The application can be started from the command-line but the application will exit as soon as the terminal session is closed. It is useful for checking the installation to start the application is this way.

```bash
python3 internal-dev-demos/RASP-PI-3B-PLUS/meter-app/main.py
```

#### Run as a Daemon

Normally, the meter application should start automatically when the RPi is powered-on. Systemd is used to start the application and the Systemd service must therefore be configured.

```bash
sudo cp ~/internal-dev-demos/RASP-PI-3B-PLUS/meter-app/meter-app.service /lib/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable meter-app.service
sudo systemctl start meter-app.service
```

Reboot the Raspberry Pi. The Meter Application should start automatically and display a message on the LCD display.

Note: The file `main.py` must be located at `~/internal-dev-demos/RASP-PI-3B-PLUS/meter-app` or the Systemd file `meter-app.service` will have to be modified accordingly.

### User Instructions

The meter display provides two buttons which can be used to control the smart meter.

#### Minus Button: Wifi

If the LED of this button is on then the Wifi connection of the RPi is connected. When the LED is off, the Wifi connection is disabled or not connected to any network.

Press this button for greater than 2 seconds to change the state of the Wifi connection. If the Wifi connection is disabled, pressing the button will enable the Wifi connection, if a network is pre-configured and available. If the Wifi connection is already enabled, pressing the button will disable the Wifi. Pressing the button for less than 2 seconds has no effect.

#### Back \(Left Arrow\) Button: Shutdown

This button can be used to shutdown the RPi device. Holding the button for greater than 4 seconds will cause the device to shutdown, the display to be cleared of data and the display backlight will turn red.

