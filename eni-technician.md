# ENI Technician

The Technician App is a Python application which can run on a Raspberry Pi with an attached 7" display. The Python application updates the GUI on the display and controls some of the logic. whereas most of the core functionality is provided by a C library, the [technician-lib](https://github.com/uniquid/internal-dev-demos/tree/master/RASP-PI-3B-PLUS/technician-lib) library.

### Hardware Requirements

* Raspberry Pi 3 B+
* Raspberry Pi 7" Touch Display

### Software Requirements

* Raspian Stretch with desktop \([https://www.raspberrypi.org/downloads/raspbian/](https://www.raspberrypi.org/downloads/raspbian/)\)

### Connecting to the RPi using the terminal

Initially, the RPi does not have SSH enabled so you must connect a monitor and keyboard to the RPi. You can then use either the graphical desktop to configure the device or else you can switch to the command-line terminal using the `Control + Alt + F1` key combination. You can switch back to the desktop using the `Control + Alt + F7` key combination.

### Connecting to the RPi using SSH

The RPi has SSH access disabled by default. You can use the `raspi-config` application to enable SSH.  
Full instructions available at [https://www.raspberrypi.org/documentation/remote-access/ssh/](https://www.raspberrypi.org/documentation/remote-access/ssh/)

### Configuring Wifi on the RPi

The Wifi connection of the RPi can be configured using the `raspi-config` application. Steps to configure the Wifi connection:

* Execute the command `sudo raspi-config`.
* Choose the option `2 Network Options`.
* Choose the option `N2 Wifi`.
* Configure the SSID and password.

### Installation

Connect the 7" Display to the Raspberry Pi using the DSI ribbon cable and 4 wires to connect the GPIO.

#### Python Packages

Install the following Python package which provides the GUI toolkit:

```bash
sudo pip3 install guizero
```

#### Build the C Library

Login to the Raspberry Pi and clone the '[dev-demos](https://github.com/uniquid/internal-dev-demos)' repository in the user Pi's home directory.

```bash
git clone https://github.com/uniquid/internal-dev-demos.git
```

Refer to required dependencies in the file [technician-lib/README.md](https://github.com/uniquid/internal-dev-demos/blob/master/RASP-PI-3B-PLUS/technician-lib/README.md). These depend must be installed before you can build the library.

Build the technician-lib library:

```bash
cd internal-dev-demos/RASP-PI-3B-PLUS/technician-lib
make clone_repo
make
```

#### Create the Configuration

Create the configuration files for the meter-app in the home directory:

{% code title="" %}
```bash
cp ~/internal-dev-demos/RASP-PI-3B-PLUS/technician-lib/engine.ini ~/
cp ~/internal-dev-demos/RASP-PI-3B-PLUS/technician-app/deploy/technician-config.json ~/
```
{% endcode %}

Edit the file `engine.ini` to match the environment configuration. The fields which must be updated include: 

* mqtt\_address: MQTT broker address for environment 
* announce\_topic: mqtt topic used by app to announce its identity creation
* UID\_appliance: Insight-api URL
* UID\_registry: Registry service URL
* meter\_IP: IP address of Smart Meter application
* meter\_SERVICE: port number of Smart Meter application

{% code title="engine.ini" %}
```text
fake MAC: 1
# name prefix of the device
name_prefix: TECH
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
# TCP address for local ETH cable connection
meter_IP: 172.116.152.61
# TCP port for local ETH cable connection
meter_SERVICE: 8000
```
{% endcode %}

Edit the file `technician-config.json` to contain the UniquID node names for the various other components of the system. You must have at least one Smart Meter entry in the list `meters`. The `name` is the name of the UniquID node for the smart meter. The IP Address does not matter as it is currently ignored by application. The `managerName` is the UniquID node name of the Capability Service node.

{% code title="technician-config.json" %}
```text
{
    "managerName": "ABCDEFGH",
    "meters": [
        {
            "name": "Smart Meter 0638-5136-3234",
            "ip": "172.1.2.3"
        },
        {
            "name": "Smart Meter 7938-9021-6577",
            "ip": "172.1.2.4"
        }
    ]
}
```
{% endcode %}

#### Configure the Ethernet Port

The Ethernet port must be configured with a static IP address. This is done by adding the following lines to the end of the configuration file `/etc/dhcpcd.conf` 

```text
interface eth0
static  ip_address=172.116.152.62/24
```

Reboot the RPi and use the `ifconfig` command to verify the Ethernet port now has this static address. The Ethernet port must be connected to another Ethernet port for the IP address to be visible.

#### Start the application

The application can be started from the command-line but the application will exit as soon as the terminal session is closed. It is useful for checking the installation to start the application is this way.

```bash
python3 internal-dev-demos/RASP-PI-3B-PLUS/technician-app/main.py
```

### Run as a Daemon

Normally, the technician application should start automatically when the RPi is powered-on. Make the following changes to the system:

```bash
mkdir -p ~/.config/lxsession/LXDE-pi
$ cp ~/internal-dev-demos/RASP-PI-3B-PLUS/technician-app/autostart ~/.config/lxsession/LXDE-pi/
$ chmod a+x ~/internal-dev-demos/RASP-PI-3B-PLUS/technician-app/technician-app.sh
```

Reboot the Raspberry Pi. The Technician Application should start automatically and display the main window of the application on the 7" display.

