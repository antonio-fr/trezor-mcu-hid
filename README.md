# TREZOR One Bootloader and Firmware HID version

Official Trezor One firmware changed USB identification and communication protocol since v1.7.
This fork of the Trezor One firmware keeps using HID usb communication. In case you build any apps on the Trezor One, this fork is 100% compatible with them. This doesn't break compatibility. So you don't need to do any modification in your apps.


## How to build the TREZOR HID bootloader and firmware

If you have Docker installed. You can follow [Docker's installation instructions](https://docs.docker.com/engine/installation/).

Clone this repository:
```sh
git clone https://github.com/antonio-fr/trezor-mcu-hid.git`
cd trezor-mcu-hid
```

Use the `build.sh` command to build the images.

* to build bootloader 1.6.0 and firmware 1.7.3:
  ```sh
  ./build.sh bl1.6.0 v1.7.3
  ```
* to build latest firmware from master:
  ```sh
  ./build.sh
  ```
* to build the emulator from master:
  ```sh
  ./build.sh EMU
  ```
* to build the emulator for version 1.7.3:
  ```sh
  ./build.sh EMU v1.7.3
  ```

Build results are stored in the `build/` directory. File `bootloader-<tag>.bin` represents
the bootloader, `trezor-<tag>.bin` is the firmware image, and `trezor-emulator-<tag>.elf`
is the emulator executable.


If you don't have docker, you need Debian 9 :

Setting up
```sh
apt-get install -y git python3-pip lib32z1 lib32ncurses5 protobuf-compiler python3-protobuf libprotobuf-dev gcc-arm-none-eabi libnewlib-arm-none-eabi
pip3 install pipenv
```

Compilation, after cloning this source repository
```sh
export LC_ALL=C.UTF-8
export LANG=C.UTF-8
script/setup
pipenv install
pipenv run ./script/cibuild
```



## What is the fingerprint of this HID version ?

Firmware fingerprints of versions earlier than 1.7 are the same as SatoshiLabs, see below.

For new versions :

1.7.3 : fa3eb33b38e56db749dbe4b6f789b5fe19b54e10b87fa28ffb4e2d5459904f12


## How to get fingerprint of firmware signed and distributed by SatoshiLabs?

1. Pick version of firmware binary listed on https://wallet.trezor.io/data/firmware/1/releases.json
2. Download it: `wget -O trezor.signed.bin https://wallet.trezor.io/data/firmware/1/trezor-1.6.1.bin`
3. Compute fingerprint: `tail -c +257 trezor.signed.bin | sha256sum`

Step 3 should produce the same sha256 fingerprint like your local build (for the same version tag). Firmware has a special header (of length 256 bytes) holding signatures themselves, which must be avoided while calculating the fingerprint, that's why tail command has to be used.

## How to install this customized built firmware?

**WARNING: This will erase the recovery seed stored on the device! You should never do this on TREZOR that contains coins!**

1. Install python-trezor: `pip install trezor` ([more info](https://github.com/trezor/python-trezor))
2. `trezorctl firmware_update -f build/trezor-TAG.bin`


