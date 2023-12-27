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

## Flash Katapult to MCU to update Fimware via canbus

Get Katapult, build Katapult, connect MCU in DFU-mode (check with dmesg), flash Katapult, stop Klipper, search for device-IDs on the canbus
```
git clone https://github.com/Arksine/katapult
cd katapult/
make menuconfig
make -j4
dmesg -HW
dfu-util -R -a 0 -s 0x08000000:mass-erase:force -D ~/katapult/out/katapult.bin
sudo systemctl stop klipper.service
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
```

## Update Firmware via canbus

build klipper, search for device-IDs on the canbus, flash Klipper to a canbus-ID
```
cd ~/klipper/
make menuconfig
make -j4
~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
~/klippy-env/bin/python3 ~/katapult/scripts/flash_can.py -i can0 -f ~/klipper/out/klipper.bin -u d9f16e998d9a
```
