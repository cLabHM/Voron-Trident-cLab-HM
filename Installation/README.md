



## Inhaltsverzeichnis

1. [Flash Image](#flash-image)  
2. [KIAUH](#kiuah)  
3. [Flash Katapult](#flash-katapult)  
4. [Flash Klipper](#flash-klipper)  
5. [Klippain](#klippain)
6. [Update Klipper](#update-klipper) 


## Flash Image
 `Raspberry Pi OS Lite (either 32bit or 64bit)`

### Paketlisten aktualisieren und System upgraden
```shell
# 1. Paketlisten aktualisieren und System upgraden
sudo apt update && sudo apt full-upgrade -y
sudo apt autoremove -y


# 2. Zeitzone auf Europa/Berlin setzen
sudo timedatectl set-timezone Europe/Berlin

# 3. Englische Locale aktivieren
sudo sed -i 's/^# en_GB.UTF-8 UTF-8/en_GB.UTF-8 UTF-8/' /etc/locale.gen
sudo locale-gen en_GB.UTF-8
sudo update-locale LANG=en_GB.UTF-8

# 4. Hostname anpassen (hier: raspberrypi)
sudo hostnamectl set-hostname raspberrypi


# 5. Root-Dateisystem erweitern (falls noch nicht automatisch geschehen)
sudo raspi-config nonint do_expand_rootfs

echo "Erstkonfiguration abgeschlossen – Neustart empfohlen."
```
```shell
sudo reboot
```



## KIUAH
https://github.com/dw-0/kiauh/blob/master/README.md

```shell
sudo apt-get install git -y
```

```shell
cd ~ && git clone https://github.com/dw-0/kiauh.git
```


```shell
./kiauh/kiauh.sh
```
- Klipper
- Moonraker
- Fluid
- Advanced
    - G-Code Shell Command

## Flash Katapult
https://github.com/Arksine/katapult

### Prepare
```shell
git clone https://github.com/Arksine/katapult
cd katapult
make clean
make menuconfig
make
```
- `STMicroelectronics STM32`
- `STM32F446`
- Clock Reference: `12 MHz crystal`
- Application start offset: `32KiB`

### Flash
- Jumper at BOOT
- `lsusb` UUID von DFU


Ersetzte UUID mit der gefunden
```shell
sudo dfu-util -a 0 -d 0483:df11 -s 0x08000000:mass-erase:force -D ~/katapult/out/katapult.bin
```
- Remove Jumper

## Flash Klipper
https://docs.vorondesign.com/build/software/octopus_klipper.html

### Prepare
```shell
cd
sudo apt install make
cd ~/klipper
make clean
make menuconfig
make
```

- Select “Enable extra low-level configuration options”
- Set the micro-controller architecture is set to `STMicroelectronics STM32`
- Set the Processor model to `STM32F446`
- Set the Bootloader offset to`32KiB`
- Set the Clock Reference to `12 MHz crystal`


### Flash

```shell
ls /dev/serial/by-id/ 
```
```shell
sudo apt install python3-serial
python3 ~/katapult/scripts/flashtool.py -f ~/klipper/out/klipper.bin -d /dev/serial/by-id/<your serial>
```

### MCU definieren
```shell
ls /dev/serial/by-id
```
## Klippain
```shell
wget -O - https://raw.githubusercontent.com/Frix-x/klippain/main/install.sh | bash
```

## Update Klipper
https://drklipper.de/doku.php?id=klipper_faq:can:66_-_katapult-bootloader