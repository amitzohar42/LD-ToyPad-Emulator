# ToyPad Emulator for Lego Dimensions

<a href="https://www.buymeacoffee.com/Berny23" title="Donate to this project using Buy Me A Coffee"><img src="https://img.shields.io/badge/buy%20me%20a%20coffee-donate-yellow.svg" alt="Buy Me A Coffee donate button" /></a>
</span>

Allows you to connect an emulated ToyPad to your PC or video-game console.

## Features

- Supports **all available characters and vehicles**
- Saves **vehicle upgrades**
- Displays the toypad's **light effects**
- Can be used from **mobile devices**
- Confirmed working on **[Cemu](https://www.youtube.com/watch?v=7CBa9u2ip-Y)**, **real Wii U**, [**RPCS3**](#rpcs3-cannot-detect-the-toy-pad), [**real PS3**](https://github.com/Berny23/LD-ToyPad-Emulator/issues/10#issuecomment-933027554) and [**real PS4**](https://www.reddit.com/r/Legodimensions/comments/pb32zg/comment/hamfj29/?utm_source=share&utm_medium=web2x&context=3)
- Can be run in a **virtual machine** on Windows, macOS and Linux
- Can be configured easily by following the instructions below

## Demo

![image](https://user-images.githubusercontent.com/59704055/140558356-6e876aac-b2fc-4f6c-83d8-78398b755a75.png)

## Videos

- First demo video on Cemu emulator: https://www.youtube.com/watch?v=7CBa9u2ip-Y

- Installation tutorial on a virtual machine: https://www.youtube.com/watch?v=5PARAnrt1jU

## Installation

**There are two options.** Please choose the installation method that suits your needs best.

<hr>

### Option 1: Virtual Machine (only for emulators)

#### Prerequisites

* Either [VMware Player](https://www.vmware.com/products/workstation-player.html) (free), [VMware Workstation Pro](https://www.vmware.com/products/workstation-pro.html) (paid) or [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) (free)
* [Debian ISO](https://www.debian.org/download)
* [VirtualHere USB Client](https://www.virtualhere.com/usb_client_software) for Windows, Linux or MacOS

#### Guide

1. Make a new virtual machine with Debian in your software of choice. You can leave the default values in VMware.

2. When first booting the Debian VM, select `Graphical install`. In the configuration, leave everything on default. Only change your language, set `debian` as hostname, don't set a root password, choose an account name and password, set partition to "yes" and `/dev/sda` for the GRUB bootloader.

3. After rebooting, log in with your password. Then click the menu on the upper left corner, search for "Terminal" and open it.

4. Run the following commands (you can copy and paste with right click):
   ```bash
   sudo apt install usbip hwdata curl python build-essential -y
   echo "usbip-core" | sudo tee -a /etc/modules
   echo "usbip-vudc" | sudo tee -a /etc/modules
   echo "vhci-hcd" | sudo tee -a /etc/modules
   
   echo "dtoverlay=dwc2" | sudo tee -a /boot/config.txt
   echo "dwc2" | sudo tee -a /etc/modules
   echo "libcomposite" | sudo tee -a /etc/modules
   echo "usb_f_rndis" | sudo tee -a /etc/modules
   
   sudo apt install -y git
   git clone https://github.com/Berny23/LD-ToyPad-Emulator.git
   cd LD-ToyPad-Emulator
   
   printf '\necho "usbip-vudc.0" > UDC\nusbipd -D --device\nsleep 2;\nusbip attach -r debian -b usbip-vudc.0\nchmod a+rw /dev/hidg0' >> usb_setup_script.sh
   sudo curl https://raw.githubusercontent.com/virtualhere/script/main/install_server | sudo sh
   
   sudo cp usb_setup_script.sh /usr/local/bin/toypad_usb_setup.sh
   sudo chmod +x /usr/local/bin/toypad_usb_setup.sh
   (sudo crontab -l 2>/dev/null; echo "@reboot sudo /usr/local/bin/toypad_usb_setup.sh") | sudo crontab -
   ```

5. Reboot you device with this command:
   ```bash
   sudo shutdown -r now
   ```

6. Log in again and run the following commands in the terminal:
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
   [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
   
   nvm install 11
   sudo setcap cap_net_bind_service=+ep `readlink -f \`which node\``
   npm install --global node-gyp@latest
   npm config set node_gyp $(npm prefix -g)/lib/node_modules/node-gyp/bin/node-gyp.js
   
   cd LD-ToyPad-Emulator
   npm install
   ```

#### Usage

1. Start the virtual machine if it's not already running. Then start the **VirtualHere USB Client** and double click on `LEGO READER V2.10`.

2. Run the emulator server with this command if you are in the correct folder (otherwise run `cd    LD-ToyPad-Emulator` first):
   ```bash
   node index.js
   ```

3. Type `http://debian` in a browser to use the emulator.

   If you want to turn it off, just press `Ctrl + C` in the terminal, then use the command `sudo shutdown now` to power off the virtual machine or just pause it from the host.
   
4. Finally, start your console emulator and the game itself (e.g. Cemu).

#

<hr>

### Option 2: Single Board Computer

#### Prerequisites
* **Raspberry Pi Zero W** ($10) or similar single board computer with Network support
  * **NOTE**: Will NOT work with Rapsberry Pi: 2, 3, 3A, 3A+, 3B, 3B+. These models lack the ability to become a usb gadget.
* **USB A to micro USB A cable** that supports data transmission (e. g. your phone's charging cable)
* 2 GB+ Micro SD card
* Internet connection on your PC and single board computer

#### Guide

1. If you're using a Raspberry Pi Zero W, flash Raspberry Pi OS Lite to your SD card using [the Raspberry Pi Imager tool](https://www.raspberrypi.org/software/) and follow [this](https://www.raspberrypi.org/documentation/configuration/wireless/headless.md) as well as [this](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) instruction for headless installation.

2. Connect your device to your PC via USB cable (don't use the port on the edge of the Pi Zero!).

4. Use SSH to run the following commands (Don't know the IP address? Try [this IP scanner](https://www.advanced-ip-scanner.com/).):
   ```bash
   echo "dtoverlay=dwc2" | sudo tee -a /boot/config.txt
   echo "dwc2" | sudo tee -a /etc/modules
   echo "libcomposite" | sudo tee -a /etc/modules
   echo "usb_f_rndis" | sudo tee -a /etc/modules
   
   sudo apt install -y git
   git clone https://github.com/Berny23/LD-ToyPad-Emulator.git
   cd LD-ToyPad-Emulator
   
   printf '\necho "$UDC" > UDC\nsleep 2;\nchmod a+rw /dev/hidg0' >> usb_setup_script.sh
   
   sudo cp usb_setup_script.sh /usr/local/bin/toypad_usb_setup.sh
   sudo chmod +x /usr/local/bin/toypad_usb_setup.sh
   (sudo crontab -l 2>/dev/null; echo "@reboot sudo /usr/local/bin/toypad_usb_setup.sh") | sudo crontab -
   ```
   
5. Reboot you device with this command:
   ```bash
   sudo shutdown -r now
   ```
   
6. Connect via SSH again and run the following commands:
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
   [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
   
   nvm install 11
   sudo setcap cap_net_bind_service=+ep `readlink -f \`which node\``
   npm install --global node-gyp@latest
   npm config set node_gyp $(npm prefix -g)/lib/node_modules/node-gyp/bin/node-gyp.js
   
   cd LD-ToyPad-Emulator
   npm install
   ```

#### Usage
1. Run the emulator server with this command if you are in the correct folder (otherwise run `cd    LD-ToyPad-Emulator` first):
   ```bash
   node index.js
   ```

2. Type **your single board computer's IP address** in a browser to use the emulator.

   If you want to turn it off, just press `Ctrl + C` in the cmd window, then use the command `sudo shutdown now` to safely power off the device.

## Update
To update this software, just pull the latest changes by running the following 3 commands:
````bash
cd LD-ToyPad-Emulator
rm package-lock.json
git pull
npm install
````

**Only use the following command if you get an error**, then run the last two lines from above again:
````bash
git reset --hard
````

## Troubleshooting

### RPCS3 cannot detect the Toy Pad
Download and run [Zadig](https://zadig.akeo.ie).

Click on `Options` and tick `List All Devices`. Select `LEGO READER V2.10` in the dropdown menu, then click on the `Replace Driver` button and on `Yes` in the dialog.

After the installation has finished, exit Zadig and restart RPCS3.

### Error: listen EADDRINUSE: address already in use :::80
Either close any other software that is using the port 80 or manually edit the last line of index.js (with `nano index.js`, edit the line, then press `Ctrl + O`, `Enter` and `Ctrl + X`).

If you did this, you may need to append your selected port to the address in the browser (like `http://debian:500` or `http://192.168.0.165:500` if your port is 500).

### VirtualHere USB Client doesn't show LEGO READER V2.10
When installing the virtual machine, you have to set the hostname to `debian`.

Alternatively, copy the following command and replace `YOUR_IP_ADDRESS` with your virtual machine's IP address (it looks like `192.168.X.X`, run `hostname -i` to show it). After you've done this, run the modified command while you're inside the `LD-ToyPad-Emulator` folder.
````bash
git reset --hard ; printf '\necho "usbip-vudc.0" > UDC\nusbipd -D --device\nsleep 2;\nusbip attach -r YOUR_IP_ADDRESS -b usbip-vudc.0\nchmod a+rw /dev/hidg0' >> usb_setup_script.sh ; sudo cp usb_setup_script.sh /usr/local/bin/toypad_usb_setup.sh
````

### I can't connect to the webpage from outside the VM (Oracle VirtualBox)
Oracle VirtualBox's default network settings do not allow it to be detected by other devices on the network. A workaround at this time is to switch to `Bridged Adapter` mode.

In VirtualBox's manager, click your image and open `Settings`. Under `Network` change `Attached to:` to `Bridged Adapter` and click `ok`.

## Acknowledgements
* **ags131** for writing one of the main NodeJS libraries I'm using: [https://www.npmjs.com/package/node-ld](https://www.npmjs.com/package/node-ld). My project would've been impossible to create without this guy's research.

* **cort1237** for implementing writing to toy tags as well as several UI updates and support for saving toy tags locally. He has made the emulator more user-friendly.


## License
[MIT](https://choosealicense.com/licenses/mit/)
