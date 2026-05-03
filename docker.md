# How to setup MeshcoretoMQTT using docker 
These instructions are tested on Ubuntu Server.

## 1. Create base folder and subfolders for configuration

```bash
# Start with making required folders (mostly inside /opt):
mkdir -p meshcoretomqtt/{mctomqtt-config,mctomqtt-config/config.d} 

# goto this folder and initiate compose.yml:
cd meshcoretomqtt
touch compose.yml # handy if you use VSC
# btw change this is you work with docker-compose.yml

```

## 2. Create 'compose.yml' file

```bash
nano compose.yml
# nano docker-compose.yml # if you use this version
```

```yml
services:
  mctomqtt:
    image: ghcr.io/cisien/meshcoretomqtt:latest
    container_name: mctomqtt
    restart: unless-stopped
    volumes:
    - ./mctomqtt-config:/etc/mctomqtt:ro
    devices:
      # Mount your device with latest repeater observer software:
      - /dev/ttyACM0 # easy method check if it refers to correct device
```
<details>
<summary>Click here to avoid swapping USB-ports</summary>

if you have multiple USB or ACM device installed on your system it is possible they swap on reboot. To avoid this behaviour you can change the reference to your devices.

```bash
# run following command
ls -la /dev/serial/by-id
```
the output will look like (in my situation 1 old Heltec V2 and 2 RAK's are connected)
```
drwxr-xr-x 2 root root 60 May  1 10:49 .
drwxr-xr-x 4 root root 80 May  1 10:49 ..
lrwxrwxrwx 1 root root 13 May  1 10:49 usb-RAKwireless_WisCore_RAK4631_Board_619E6DDF4F95D2BC-if00 -> ../../ttyACM0
lrwxrwxrwx 1 root root 13 Apr 19 17:29 usb-RAKwireless_WisCore_RAK4631_Board_F58BA22A157F29A0-if00 -> ../../ttyACM1
lrwxrwxrwx 1 root root 13 Apr  2 12:32 usb-Silicon_Labs_CP2102_USB_to_UART_Bridge_Controller_0001-if00-port0 -> ../../ttyUSB0
```
find the relevant device and replace in your compose file:

```yml
    devices:
    - /dev/ttyACM0 
# with
    devices:
    - /dev/serial/by-id/usb-RAKwireless_WisCore_RAK4631_Board_619E6DDF4F95D2BC-if00
# it is also possible to give it a more distinct name
    - /dev/serial/by-id/usb-RAKwireless_WisCore_RAK4631_Board_619E6DDF4F95D2BC-if00:/dev/ttyMC_RP_RAK
```
Do not forget to change this in your user TOML file.
</details>

## 3. Copy the latest config.toml to folder: 'mctomqtt-config'

```bash
wget -O mctomqtt-config/config.toml https://raw.githubusercontent.com/Cisien/meshcoretomqtt/refs/heads/main/config.toml.example
```

## 4. Create user TOML-files in folder: 'mctomqtt-config/config.d'

An user toml file needs to create with your specific settings and local MQTT-servers, you may separate this in two files. 


[99-user.toml](https://github.com/WJ4IoT/Meshcore-Observer-Installation/blob/main/99-user.toml)

```bash
# copy an 99-user.toml to folder: mctomqtt-config/config.d (use your own settings)
wget -O mctomqtt-config/config.d/99-user.toml https://raw.githubusercontent.com/WJ4IoT/Meshcore-Observer-Installation/refs/heads/main/99-user.toml
```

## 5. There are some preset TOML files external MQTT servers to copy to folder: 'mctomqtt-config/config.d'

```bash
# copy your 99-user.toml to folder: mctomqtt-config/config.d
wget -O mctomqtt-config/config.d/letsmesh.toml https://raw.githubusercontent.com/Cisien/meshcoretomqtt/refs/heads/main/presets/letsmesh.toml
```
The maximun number of MQTT servers is 4 (?)
