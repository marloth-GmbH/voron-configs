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

Activate SPI
```
raspi-config
```
3 Interface Options --> I4 SPI --> Enable (Yes) 


add to /boot/config.txt
```
dtparam=spi=on
dtoverlay=mcp2515-can0,oscillator=12000000,interrupt=25,spimaxfrequency=2000000
```


/etc/network/interfaces.d/can0
```
# CAN-Bus
auto can0
iface can0 can static
  bitrate 1000000
  up ifconfig $IFACE txqueuelen 1024
```
