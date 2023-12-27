# voron-configs

# MCU setup 
Raspberry Pi as main MCU. All 'external' boards as a sub-MCU with its own config-file. 

Set up raspberry as main MCU
```
cd ~/klipper
sudo cp ./scripts/klipper-mcu.service /etc/systemd/system/
sudo systemctl enable klipper-mcu.service
make menuconfig
sudo service klipper stop
make flash
sudo usermod -a -G tty pi
sudo service klipper start
```

# CAN-Bus

## Set up CAN-Interface with RS485 CAN BUS Shield 

Activate SPI and add device tree entry to /boot/config.txt
```
echo -e "dtparam=spi=on\ndtoverlay=mcp2515-can0,oscillator=12000000,interrupt=25,spimaxfrequency=2000000" | sudo tee -a /boot/config.txt
```


create the interface /etc/network/interfaces.d/can0
```
sudo bash -c 'echo -e "# CAN-Bus\nauto can0\niface can0 can static\n  bitrate 1000000\n  up ifconfig \$IFACE txqueuelen 1024" > /etc/network/interfaces.d/can0'

```

Install can-utils
```
sudo apt install can-utils
```
