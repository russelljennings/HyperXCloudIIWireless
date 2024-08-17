# HyperXCloudIIWireless
A CLI and tray application to monitor HyperX Cloud II Wireless headset battery level.

**Hopefully I can get this to support the Cloud Core I have... - Russell**

<img src=./screenshots/tray_app.png alt="tray_app" width="400">

## Compatibility
The CLI application is compatible with both Linux and MacOS operating systems. 
However, the tray application is only functional on Linux. 
Although it was only tested on Manjaro/KDE, it should also work on other distribution and desktop environments.

Currently, only the HyperX Cloud II Wireless and HyperX Cloud Stinger 2 Wireless.
However, the HyperX Cloud II Wireless comes in two versions: one produced before HP acquired HyperX and one after.
The application has only been tested on the HyperX Cloud II Wireless with the HP vendorID.

## Prerequisites

### Hidraw

Make sure you have hidraw installed on your system.

Debian/Ubuntu:

`sudo apt install libhidapi-hidraw0`

Arch:

`sudo pacman -S hidapi`

MacOS:

`brew install hidapi`

### Other Dependencies

These dependencies are probably already installed.

Debian/Ubuntu:

`sudo apt install libdbus-1-dev libusb-1.0-0-dev libudev-dev`

Arch:

`sudo pacman -S dbus libusb`

MacOS:

`brew install libusb`

### Udev (Linux only)

Create a new file in /etc/udev/rules.d/99-hyperx-cloud-II.rules with the following content inside:

```
SUBSYSTEMS=="usb", ATTRS{idProduct}=="018b", ATTRS{idVendor}=="03f0", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idProduct}=="0696", ATTRS{idVendor}=="03f0", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idProduct}=="1718", ATTRS{idVendor}=="0951", MODE="0666"
SUBSYSTEMS=="usb", ATTRS{idProduct}=="0d93", ATTRS{idVendor}=="03f0", MODE="0666"

KERNEL=="hidraw*", ATTRS{idProduct}=="0d93", ATTRS{idVendor}=="03f0", MODE="0666"
KERNEL=="hidraw*", ATTRS{idProduct}=="018b", ATTRS{idVendor}=="03f0", MODE="0666"
KERNEL=="hidraw*", ATTRS{idProduct}=="0696", ATTRS{idVendor}=="03f0", MODE="0666"
KERNEL=="hidraw*", ATTRS{idProduct}=="1718", ATTRS{idVendor}=="0951", MODE="0666"
```

Once created, replug the wireless dongle.

## Building

To only build the cli_app on MacOS, use:
`cargo build --release --bin cli_app`

To build both applications on Linux, use:
`cargo build --release`

You can also download a compiled version from [releases](https://github.com/LennardKittner/HyperXCloudIIWireless/releases).

`cargo build --release` **will fail on MacOS** because cargo will try to build the tray application, but some dependencies are exclusive to Linux.

## Usage

`cli_app` without any arguments will print the current battery level.

`hyper_x_cloud_ii_wireless` without any arguments will start the tray application. Once it's open, hover over the headset icon in the system tray to view details like the battery level. To exit, right-click on the icon.

## Limitations
The battery level is checked regularly, but information about muting or charging the headset is only recorded when those events occur.

## Contributing / TODOs
- [ ] Menu bar app for MacOS.
- [ ] Actively configure the headset.
- [ ] Query device state instead of only relying on events.

You can contribute code or monitor packets using Wireshark from the HyperX app on Windows.

### How to use Wireshark to capture packets

This [guide](https://github.com/liquidctl/liquidctl/blob/main/docs/developer/capturing-usb-traffic.md) is very helpful.
In my case, the filter `usb.idVendor == 0x03f0 && usb.idProduct == 0x018b` only showed on request.
I then only listened to the port on which this request was sent, e.g., `(usb.src == "3.5.0") || (usb.dst =="3.5.0")`.
If you have an older headset, you may have to use a different vendor and product ID `usb.idVendor == 0x0951 && usb.idProduct == 0x1718`.
Once you have set the filters, you can perform various actions and review the packets transmitted to and from the headset.

## Other Projects

This project was inspired by [hyperx-cloud-flight](https://github.com/kondinskis/hyperx-cloud-flight).
