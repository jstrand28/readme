**Activity 05 - FreeRange**

**Team members:** **

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**


<!-- markdown-toc end -->

# Introduction

<mark>Reformat section to make sense with rest of document

 In this activity we will work on making our robot much more advanced, in this activity we will use RaspberryPi to take care of all hight level tasks like image processing, video processing, network communication and finally motion planning.Where as Romi 32U4 board can deal with low-level tasks that the pi is incapable of such as motor control and sensing. 
## Raspberry Pi 3 Model B

The Raspberry Pi 3 Model B is a single-board computer developed by the Raspberry Pi Foundation. This board consists of a 1.2Ghz 64-bit quad-core ARM processor and an 802.11n Wireless LAN, Bluetooth 4.1, and Bluetooth Low Energy. Like the previous version (the Pi 2) it consists of 1 GB of RAM, 4 USB ports, and full HDMI support

A powerful feature of the Raspberry Pi is the row of GPIO (general-purpose input/output) pins along the extreme right edge of the board. Like every Raspberry Pi chipset, it consists of a 40-pin GPIO. A standard interface for connecting a single-board computer or microprocessor to other devices is through General-Purpose Input/Output (GPIO) pins. GPIO pins do not have a specific function and can be customized using the software.
## I2C Pins

I2C is used by the Raspberry Pi board to communicate with devices that are compatible with Inter-Integrated Circuit (a low-speed two-wire serial communication protocol). This communication standard requires master-slave roles between both devices. I2C has two connections: SDA (Serial Data) and SCL (Serial Clock). They work by sending data to and using the SDA connection, and the speed of data transfer is controlled via the SCL pin.

Data: (GPIO2), Clock (GPIO3)
EEPROM Data: (GPIO0), EEPROM Clock (GPIO1)

## Problem Statement
The purpose of this activity is to Mount RaspberryPi on Pololu 32u4 board along with setting up all the software required to work smoothly for all upcoming activities.
# Setup
For this activity, you will be building a robot with the same chassis found in [the previous activities](00-GettingStarted.md). For this robot, you will need:

*  Romi 32U4 chassis and control board
*  Raspberry Pi 3 Model B (you can use any other model if you want)
*  A microSD card to flash linux on Raspberry Pi
*  Micro-USB power supply
*  Keyboard, Mouse, HDMI cable and a display to get done with initial software set up
*  <mark>include link to picture of map</mark>
* 3D printed bag: A small bag similiar to a soup can with handle or a small 3D printed bag

<mark>include pictures of need parts</mark>

Refer to [Getting Started](0-GettingStarted.md) for steps to build robot chassis and wiring connections.
<mark>SM: Add wiring diagram for the connections need for just this activity.</mark>

The software required for this will just be Arduino IDE, VScode with the PlatformIO extension. You will also need a micro-USB cable to connect the Romi 32U4 control board to your computer. Refer to [Getting Started](0-GettingStarted.md).

## Initializing the Raspberry Pi

<mark>FINISH SECTION

Before we place the Raspberry Pi on the Romi, we will need to initialize the Pi first. Download the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) from the Raspberry Pi website to flash the Pi with the latest Raspberry Pi OS. Once installed, connect the SD card to your laptop and open the Imager. For the operating system, select Raspberry Pi OS (other) and select the 32-bit Raspberry Pi OS Full, as it comes with a desktop environment. For storage, select the SD card. Make sure your selections match those in **Figure 0**. Then, press write and wait for the OS to be flashed onto the SD card. This process may take several minutes depending on your unit.

Alternitely, we could use [NOOBS](http://downloads.raspberrypi.org/NOOBS/images/NOOBS-2022-04-07/) (New Out Of Box Software) to facilitate the flashing process and allow for more control over the Raspberry Pi's OS. Download the file using the link and simply drag the downloaded folder onto the SD from your laptop.

<p align="center">
  <img src="images/romi-RPi/raspberry-pi-imager.jpeg" width="500" alt="image"/>
  <p align="center"><strong>Figure 0.</strong>
</p>

Once the OS is flashed, connect the Raspberry Pi to a display using an HDMI connecting and a micro-USB power supply to turn on the Pi. The Raspberry Pi will boot up into its setup interface. You will be prompted for your location and time zone, fill them in accordingly. Then, create a username and password. The default is set to `pi`. Finally, you will be asked If you want to connect to the internet. If you would like to connect to a WPA2 Enterprise network (found on school campuses), skip this step as it will be discussed later on. There is no need to select a netwrok at this time so this step can be skipped entirely. The Pi will then reboot and open up into its desktop environment.


### Updating Configuration Settings

In order to control the Raspberry Pi remotely and give it control over the Romi, we'll need to update some configuration files. To access these options within the Pi, begin by opening up a new terminal and type the following command:

```bash
sudo raspi-config
```

The `sudo` command allows for admin authorization over files that a "normal" user would not have access to. Essentially, it gives you the ability to run commands as the "superuser". `raspi-config` is a configuration tool for the Raspberry Pi software that allows you to configure various settings on the Pi. **Figure 0** show the user interface of the raspi-config tool.

<p align="center">
  <img src="images/romi-RPi/sudo-raspi-config.png" width="500" alt="image"/>
  <p align="center"><strong>Figure 0.</strong>
</p>

With the raspi-config tool opened, use the arrows to navigate to the Interface Options and press Enter. In here, we want to enable four options: Camera, I2C, SSH, and VNC. Go through one by one and enable each of the four options. Once enabled, exit the tool and you will be asked if you want to reboot, press YES. 

When back into the desktop environment, open a new terminal again. Now, we'll want to increase the I2C baud rate to 400kHz for faster communication between the channels. By default, this is set to only 100kHz. We can change this using the command:

```bash
sudo nano /boot/config.txt
```

The `nano` command opens up the nano terminal-based text editor. We need this as we are going to be editing the text within the config.txt file. At the very bottom of the file, type the following line:

```bash
dtparam=i2c_arm_baudrate=400000
```

Exit the text editor using ^X (CTRL+X) and press Y to save the file.To make sure all the changes have been made to the configuration settings, reboot the Raspberry Pi using the `sudo reboot` command into the terminal.

To make sure these changes have been made, head to the menu at the top left corner of the screen and got to Preferences and Raspberry Pi Configuration, as shown in **Figure 0**. Under Interfaces, the selected options of SSH, VNC, and I2C should all be turned on. If so, we have succesfully allow for communication through these <mark>channels</mark>.

### Connecting the Raspberry Pi to a WPA2 Enterprise Network and Setting a Static IP Address

The Raspberry Pi 3 Model B has the ability to connect to the internet via Wi-Fi. This is useful as we won't be able to connect the Pi to a display once it is mounted on the Romi. With an internet connection, we can control the Raspberry Pi via SSH or VNC. These two option will be elaborated on later.

A WPA2 Enterprise network is one that requires a username and password. This is the case for the BUSecure network at Bradley University and most univeristy campuses. We could connect the Pi to a network that doesn't require a username and password, but the connection may disappear if not regularly checked. Therefore, we will create a config file that will make sure the Pi always has a secure internet connection.

Open up the terminal on the Raspberry Pi and write the following command:

```bash
sudo nano /etc/network/interfaces
```
Within the file, at the bottom, write the following command:

```bash
auto lo
iface lo inet loopback
allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

Once you have written the previous commands, use ^X (CTRL+X) to exit the file and the Y to save the file. Next, we'll want to edit the wpa_supplicant.conf file. Using the following command, open the file in the terminal text editor.

```bash
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

Copy and paste the following script into the *.conf* file. Be sure to fill in your username and password, and change the name of your network if it is not BUSecure.

```bash
network={
      ssid="BUsecure"
      priority=1
      proto=RSN
      key_mgmt=WPA-EAP
      pairwise=CCMP
      auth_alg=OPEN
      eap=PEAP
      identity="your username"
      password="your password"
      phase1="peaplabel=0"
      phase2="auth=MSCHAPV2"
}
```

Once paste into the file, CRTL+X to exit the file an then press Y to save the file. Then using the command `sudo reboot` reboot the Raspberry Pi to finalize the changes. Once loaded back in, you should be connect to the WPA-Enterprise network.

Next, we'll want to create a static IP address to ease the remote communition of the Raspberry Pi. Over time, the IP adress will change and we want to connect to the Pi without using a display to check the new IP adress everytime. The Rasberry Pi assigns a new IP adress everytime it is turned on using a DHCP (Dyanmic Host Configuration Protocol) configuration. 

Before we start, we need to collect some information about the current connection made to the network. Type thefollowing command in a terminal to determine your current IP address:

```bash
hostname -I
```

Next, use the following command to determine the router's IP.

```bash
ip r | grep default
```

The resulting output from the previous command will provide the router's IP as the first IP in the output.

```cpp
default via "router_ip" dev wlan0 src "current_ip" metric 303
```

Next, use the `sudo nano` command to open the *resolv.conf*.

```bash
sudo nano /etc/resolv.conf
```

Within the file, the DNS IP address can be found right after the word `nameserver`. With these addresses collected, use the following command to open the *dhcpcd.conf* file:

```bash
sudo nano /etc/dhcpcd.conf
```

Copy and paste the following lines into the file. The first line specifies the type of connection you have. In our case, we have a wireless connection, therefore, `wlan0` is specified as the interface. In place of the `"static_ip"`, specify an IP address of your choosing. Make sure this chosen IP address does not match any that exist on the network. For example, if your previous IP address is 192.168.24.137, your static IP address should be something like 192.168.53.95. <mark>It is completely arbitrary.</mark> Once you have specified the static IP address, replace the `"router_ip"` and `"dns_ip"` with the appropriate addresses.

```bash
interface wlan0
static ip_address="static_ip"/24
static routers="router_ip"
static domain_name_servers="dns_ip"
```

Finally, use ^X (CTRL+X) to exit the file and Y to save. Run the `sudo reboot` command to reboot the Raspberry Pi.


### Updating the Raspberry Pi

With a secure internet connection made, it is time to update the Pi. Open a new terminal and write the following lines:

```bash
sudo apt-get update
sudo apt-get upgrade
```

Depending on the OS installed, this process may take anywhere from a couple of seconds to an hour. Once the connection is completed, reboot the Pi.

## Initializing the Romi 32U4

To get started, we must first set the Romi 32U4 control board to allow the Raspberry Pi to control it. The Pololu team have already create an I2C slave library for the Romi 32U4, which can be found [here](https://github.com/pololu/pololu-rpi-slave-arduino-library). For convenience, this library has been uploaded to this repository, along with some modification. Following the steps the Pololu team have provided on their [blog](https://www.pololu.com/blog/663/building-a-raspberry-pi-robot-with-the-romi-chassis), the foundation of this robot can be used and upgraded.

Download the folder [Romi-RPi-I2C-slave](code/romiRPi/Romi-RPi-I2C-slave), which should contain a *platform.ini* file. Refer to [Software Setup](00-GettingStarted.md#software-setup) in the Getting Started file to open the folder properly in VSCode. The code begins by `#include` three libraries: `servo32u4.h`, `Romi32U4`, and `PololuRPiSlave.h`. The `servo32u4.h` library will allow us to control a servo motor connected to Pin 5 on the Romi 32U4 control board. The `Romi32U4.h` allows us to control the motors, buzzer, built-in LEDs, and buttons on the control board.  The `PololuRPIiSlave.h` library specifies the I2C connection from the Romi to the Raspberry Pi.

```cpp
#include <servo32u4.h>
#include <Romi32U4.h>
#include <PololuRPiSlave.h>
```

At the start of the code, a data structure, or `struct`, called `Data` is specifed with several variables. A data structure is essentially a group of elements organized together under one name. This data structure will help us "pack" data into a several bytes and send it to and from the Raspberry Pi via I2C communication. Packing the data as such will allow us to use a python `struct` module to properly and reliably unpack the data. The placement of the data in the struct must be noted as this is the order in which they will be unpacked in. This issue will become more prevalent once we begin programming the Raspberry Pi.

Firstly, we specify the LEDs as bools and label them by their colors. A Boolean variable is useful for object like LEDs and buttons as they only have two states (on or off). Based on the definition of a Boolean variable, a `bool` has a specified width of 1 byte or 8 bits, same goes for a `char`. As specified in the `stdint.h` library, integers ar 2 bytes, or 16 bits. Every address corresponds to 1 byte. The data will be sent as one large 64 byte struct. When unpacked by the Raspberry Pi, each variable will correspond to an "address" or two, depending on the variable type. For example, the `leftMotor` will corresspond to addresses 6 and 7. It has two addresses as it is a `int16_t`, meaning it is a 2 byte signed integer. Commented by the variables is their corresponding addresses.

```cpp
struct Data // Comments signify the address of each variable when unpacked by the Pi
{
  bool yellow, green, red;              // 0, 1, 2
  bool buttonA, buttonB, buttonC;       // 3, 4, 5

  int16_t leftMotor, rightMotor;        // 6-7, 8-9
  uint16_t batteryMillivolts;           // 10-11
  uint16_t analog[6];                   // 12-23
  uint16_t setServo;                    // 24-25

  bool playNotes;                       // 26
  char notes[14];                       // 27-40

  int16_t leftEncoder, rightEncoder;    // 41-42, 43-44
};
```

After the specification of the struct, we want to declare all the objects we will be using. The main object to declare is the slave from the PololuRPiSlave library. In this library, we are provided a template of how the data will be sent. By declaring the slave to have a buffer type of `struct Data` and a delay of 5 ms, the Romi board will be able to send and receive data from the Raspberry Pi. Next, we'll want to declare all objects used by the robot. These objects include:
  * On-board Buzzer
  * Motors
  * All three on-board buttons
  * Motor encoders
  * Servo Motor

```cpp
PololuRPiSlave<struct Data,5> slave;
PololuBuzzer buzzer;
Romi32U4Motors motors;
Romi32U4ButtonA buttonA;
Romi32U4ButtonB buttonB;
Romi32U4ButtonC buttonC;
Romi32U4Encoders encoders;
Servo32U4Pin5 servo;
```

With the preliminary declarations made, the setup loop is ready for completion. As commonly done in setup loops, we have to initialize some of our objects. At first, the Raspberry Pi will need an address on the Romi's I2C bridge to look to for data. To help with debugging, set pin 4 to `HIGH` and play some sound off the buzzer to let us know if the Romi is on. The buzzer sound may not seem important for debugging, but once we start controlling the Romi with the Pi, you'll notice that the Romi may reset completely. This will be discussed in later activities. <mark>explain the buzzer and pin 4 settings</mark> Finally, declare the servo object and `setMinMaxMicroseconds`, as done in previous activities.

```cpp
void setup()
{
  // Set up the slave at I2C address 20.
  slave.init(20);
  digitalWrite(4,HIGH);

  // Play startup sound.
  buzzer.play("v10>>g16>>>c16");

  servo.attach();
  servo.setMinMaxMicroseconds(1350, 2500);
  servo.writeMicroseconds(2000);
}
```

At the start of each loop, we want to `updateBuffer()` to make sure the data sent and received continuously. The data structure created earlier needs data. Let's start with data that will be read by the Pi. As we previously specified the slave object, the function `slave.buffer.buttonA` specifies `buttonA` in the buffer which is our data `struct`. This method will be used to specify which data object we want to write to or ream from. For example, `buttonA` is set equal to `buttonA.isPressed()`. Both `buttonA` arguments are not the same. `buttonA.isPressed()` refers to the object on the Romi not within the data `struct`. This may be confusing to a human, but the Romi can differentiate between both based on the context in which it is used and declared. The same process is repeaated for `buttonB` and `buttonC`. 

Another feature that will be helpful is the battery voltage. The Raspberry Pi will require much more power than the Romi can provide. Either six AA-batteries can be used or a 2-cell 7.4V LiPo battery. The power consumption will be discussed later. <mark>discuss power consumption</mark> Next, we want to repeat the same process for the analog pins. We could do them one by one for all six analog pins, but that isn't very efficient. A for loop can faciliate this process for us.

```cpp
void loop()
{
  // Call updateBuffer() before using the buffer, to get the latest
  // data including recent master writes.
  slave.updateBuffer();

  // Write various values into the data structure.
  slave.buffer.buttonA = buttonA.isPressed();
  slave.buffer.buttonB = buttonB.isPressed();
  slave.buffer.buttonC = buttonC.isPressed();

  // Change this to readBatteryMillivoltsLV() for the LV model.
  slave.buffer.batteryMillivolts = readBatteryMillivolts();

  for(uint8_t i=0; i<6; i++)
  {
    slave.buffer.analog[i] = analogRead(i);
  }
```

As for the data being written by the Pi, we will be taaking data from the `struct` and using them as the arguments to several functions.

```cpp
  // READING the buffer is allowed before or after finalizeWrites().
  ledYellow(slave.buffer.yellow);
  ledGreen(slave.buffer.green);
  ledRed(slave.buffer.red);
  motors.setSpeeds(slave.buffer.leftMotor, slave.buffer.rightMotor);
  servo.writeMicroseconds(slave.buffer.setServo);
```

```cpp
  // Playing music involves both reading and writing, since we only
  // want to do it once.
  static bool startedPlaying = false;
  
  if(slave.buffer.playNotes && !startedPlaying)
  {
    buzzer.play(slave.buffer.notes);
    startedPlaying = true;
  }
  else if (startedPlaying && !buzzer.isPlaying())
  {
    slave.buffer.playNotes = false;
    startedPlaying = false;
  }

  slave.buffer.leftEncoder = encoders.getCountsLeft();
  slave.buffer.rightEncoder = encoders.getCountsRight();

  // When you are done WRITING, call finalizeWrites() to make modified
  // data available to I2C master.
  slave.finalizeWrites();
}
```

## Place the pi on the romi

> use male to female gpio header pins
>>Placement of camera
>>>camera mount
>>>>pi standoffs
>>>>>servo motor breadboard attachement
>>>>>>lipo battery

### Power Consumption


## VScode Working Environment

VScode offers a great UI for programming. In the [Getting Started](00-GettingStarted.md) file, code was uploaded to the Romi 32U4 control board via VScode. Similarly, we can use the VScode to control the Raspberry Pi via SSH. To start, assuming you already have VScode downloaded (if not, see [Software Setup](00-GettingStarted.md#software-setup)), we will need an extension to allow for remote SSH connections to be established. Head to the extesions tab and search for "SSH". Install the extension created by Microsoft called "Remote - SSH", shown in **Figure 0**. Restart your app.

<p align="center">
  <img src="images/romi-RPi/ssh-extension.png" width="500" alt="image"/>
  <p align="center"><strong>Figure 0.</strong>
</p>

Upon reboot, a new feature should show up at the bottom left corner of the screen. This will allow you to open a remote window. We'll need to create a config file for a new SSH host, being your Raspberry Pi. Press Enter on "Connect to Host..." and select "+ Add New SSH Host...", shown in **Figure 0**. When prompted, write the following line in the text box, filling in your user and static IP address you created earlier: <mark> include figures</mark>

```shell
ssh <user>@<static_IP>
```

<p align="center">
  <img src="images/romi-RPi/ssh-new-host.png" width="500" alt="image"/>
  <p align="center"><strong>Figure 0.</strong>
</p>

By default, your user should be pi, unless you deliberately changed it. <mark>Have to try this feature on different laptop to make sure no problem occurs with establishing SSH connection and if you there are more steps necessary for people on different operating systems</mark>. Once the host is configure, repeat the process of opening up a remote window and "Connect to Host". Instead of adding a new host, simply select the IP address of your Pi. Give the system time to initialize and establish the connection. Once it is done, you will be prompted for the password and the location you which to open up in your workspace. Simply, enter your password and leave the location set to the default (/home/pi). Your workspace should look like **Figure 0**.

<p align="center">
  <img src="images/romi-RPi/ssh-connection.png" width="500" alt="image"/>
  <p align="center"><strong>Figure 0.</strong>
</p>

You should be set and connected to the Pi via SSH. This does not show the desktop environment on the Raspberry Pi, but we do not need it. However, you would rather work on the Pi's desktop environment, you can do so by establishing a VNC connection. Luckily, we already allowed for a VNC connection to be made to the Pi previously. <mark>talk about vnc connection</mark>


# Controlling Romi 32U4 with Raspberry Pi

Adapted from [Pololu RPi](https://www.pololu.com/blog/663/building-a-raspberry-pi-robot-with-the-romi-chassis).

Since we have the I2C code uploaded onto the Raspberry Pi, we need code for the Raspberry Pi. Unlike the Romi code, the code for the Pi will be written in Python. Luckily, the Pololu team has a repository including python scripts for the Pi. However, we have made some adjustements to the files to fit our needs. You can find the code [here](code/romiRPi/RPi-scripts). 

Setup your SSH connection with the Raspberry Pi through VScode or a terminal of your choice. Once connected, open up the terminal. If you're on VScode, got to View > Terminal in the menu. To start, type `cd` to make sure your in the main directory. After that, you'll want to download this repository's as a zip file onto your Pi. Type the following command into the terminal:

```shell
wget https://github.com/suruz-miah/AMAR-MiahHenderson/archive/refs/heads/main.zip
```

<mark> will they need to clone the whole repository or will there be a seperate repository we can place the RPi code in?</mark>

This process may take a few minutes. Once the download is finished, type the following command into th eterminal to run the executable shell script:

```shell
sh ~/AMAR-MiahHenderson/code/romiRPi/RPI-scripts/requirements.sh
```

This shell script will update the Pi, install several library dependencies, create a virtual environment, delete the repository to free up space on the Pi, and setup the work environment where all the programming will be done. You will only need to run this once. If you were to shutdown the Pi and come back later, simply run the following command in your terminal to eneter the workspace again:

```shell
source ~/pi/setup.sh
```

The `requirements.sh` script will take anywhere from 15 minutes to an hour depending on your internet connectivity. Once complete, we can begin controlling the Romi using the Pi. As it stands, you should be in the `pi` directory and the `romi-env` virtual environment. Your terminal should read something like:

```shell
(romi-env) pi@raspberrypi:~/pi $ 
```

Now, let's run a python script to turn on and off the LEDs every second. Type the following command in your terminal:

```shell
python3 blink.py
```

The `python3` command just means run the following script in python. Unlike the Romi code written in C++, the Raspberry Pi will run python scripts. You should see the 
Type the following command into your terminal:

```shell
python3 server.py
```

Once you run the command, VScode will notify you that there is a port open. See **Figure 0** for the notification. Alternitively, you can open your browser and type the Pi's IP address followed by a colon and the port address, for example, `192.168.88.88:5000`. This server will be expanded upon in further activities. As you can see, the server gives you the option to control the buzzer output, the motors, and the LEDs, as well as see the output from the Romi's buttons, analog pins, battery charge, and the motor encoders.






>>>>> OUTLINE

* download repository using wget and unzip
* run the requirement.sh shell script to download alll library dependencies and create virtual environment, which will delete repository so its not taking up any unnecessary storage
* Setup server to automatically run when pi is turned on
* reboot pi
* begin working on server an modifications
