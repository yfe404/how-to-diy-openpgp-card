
# DIY OpenPGP Card

## Introduction
This repository provides instructions and resources for building your own OpenPGP card using open-source hardware and software. The OpenPGP card is a smart card integrated with many OpenPGP functions, enabling cryptographic tasks like encryption, decryption, digital signing, verification, and authentication.

### What is an OpenPGP Card?
From Wikipedia:
>In cryptography, the OpenPGP card is an ISO/IEC 7816-4, -8 compatible smart card integrated with many OpenPGP functions. It allows secure storage of secret key material and supports various cryptographic tasks. Private keys and passwords cannot be read from the card with any command or function, ensuring security.

## Purpose of this Repository
This project guides you through the steps to create your own OpenPGP card. The total cost was approximately 25 euros, but with careful sourcing from Aliexpress or eBay, the cost can potentially be reduced to under 9 euros.

## Materials Needed
- STM32 Blue Pill
  - [Amazon](https://www.amazon.fr/ARCELI-STM32F103C8T6-d%C3%A9veloppement-syst%C3%A8me-minimum/dp/B077MJKCVT/)
  - [Aliexpress](https://fr.aliexpress.com/item/1005002317649315.html)
- St-Link V2
  - [Amazon](https://www.amazon.fr/gp/product/B09HJQQ1C6/)
  - [Aliexpress](https://fr.aliexpress.com/item/1766455290.html)
- Combined STM32 Blue Pill & St-Link V2 [Aliexpress](https://fr.aliexpress.com/item/32792513237.html)

## Background Tutorial
For more context, visit [The Static Turtle Blog](https://blog.thestaticturtle.fr/lets-make-a-diy-gpg-usb-key/).

## Instructions

### Setting Up the Environment
Clone the repository and set up the environment:
```sh
git clone https://github.com/TheStaticTurtle/gnuk
cd gnuk
git pull
git submodule update --init
git submodule sync
git submodule update --remote
cd chopstx
git pull origin master
cd ..
cd src
./configure --vidpid=234b:0000 --target=TURTLE_AUTH --enable-confirm-button
make clean
make
```

### Flashing the Firmware
Connect the Blue Pill using the St-Link and run the following commands:
```sh
sudo /usr/local/bin/openocd -f /usr/local/share/openocd/scripts/interface/stlink-v2.cfg -f /usr/local/share/openocd/scripts/target/stm32f1x.cfg
```
In another terminal, execute:
```sh
# Commands to unlock and flash the STM32
echo 'stm32f1x unlock 0' > tmp.network
echo 'reset halt' >> tmp.network
echo 'stm32f1x unlock 0' >> tmp.network
echo 'reset halt' >> tmp.network
echo 'flash erase_sector 0 0 127' >> tmp.network
echo 'flash write_bank 0 ./gnuk/src/build/gnuk.bin 0' >> tmp.network
echo 'reset' >> tmp.network
echo 'exit' >> tmp.network

cat tmp.network | netcat 127.0.0.1 4444
rm tmp.network
```

### Verifying the Installation
After flashing, verify the installation with:
```sh
dmesg
gpg --card-status
```

## Troubleshooting

### Connection Failure with STLink-V2 Using OpenOCD
In case of connection failure, press and hold the reset button while OpenOCD is trying to connect. [More Info](https://stackoverflow.com/questions/66533547/why-am-i-getting-a-communication-failure-when-trying-to-connect-with-stlink-v2-u)

### Flashing STM32
Refer to this [guide](https://alexbirkett.github.io/microcontroller/2019/03/30/flash_bluepill_using_ST_link.html) for detailed instructions.

### Card Not Available Error
If encountering 'No such device' errors, try:
```sh
gpgconf --kill gpg-agent
```
[Source](https://libreddit.spike.codes/r/archlinux/comments/vuc957/anyone_suddenly_seeing_gpg_openpgp_card_not/)

### Enabling SSH Access
For SSH access configuration, refer to these resources:
- [USB Crypto Token for GPG and SSH](https://blog.danman.eu/2-usb-crypto-token-for-use-with-gpg-and-ssh/)
- [Using GPG Subkeys for SSH](https://opensource.com/article/19/4/gpg-subkeys-ssh)

