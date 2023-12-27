# voron-configs

# MCU setup 
Raspberry Pi as main MCU. All 'external' boards as a sub-MCU with its own config-file. 

```cd ~/klipper
sudo cp ./scripts/klipper-mcu.service /etc/systemd/system/
sudo systemctl enable klipper-mcu.service
make menuconfig
sudo service klipper stop
make flash
sudo usermod -a -G tty pi
sudo service klipper start```

