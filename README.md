Table of Contents
=================

   * [IoT Senors Network](#iot-senors-network)
      * [Intoduction](#intoduction)
      * [Description](#description)
      * [How to Build Project](#how-to-build-project)
         * [Block Diagram](#block-diagram)
         * [Cloud Service](#cloud-service)
         * [Web Service API](#web-service-api)
         * [IOS Application](#ios-application)
         * [mBed Sensor Pad](#mbed-sensor-pad)
         * [PocketBeagle](#pocketbeagle)
            * [Wiring Diagram](#wiring-diagram)
            * [Connecting the XBee module](#connecting-the-xbee-module)
            * [Connceting WiFi](#connceting-wifi)
            * [Connecting the PocketBeagle to Eduroam](#connecting-the-pocketbeagle-to-eduroam)
            * [Building Cloud Communication Program](building-program)
      * [Conclustion](#conclustion)
      * [Desired Improvements](#desired-improvements)
      * [Helpful Links/Thanks to](#helpful-links/thanks-to)
         * [mBED Sensors](#mbed-sensors)
         * [PocketBeagle Communication](#pocketbeagle-communication)
         * [WiFi Configuration](#wifi-configuration)
      * [Parts List](parts-list)

# IoT Senors Network
## Intoduction
IoT or Internet of Things is a major part of today's embedded systems. It relates to the conecpt of connecting devices
to the internet. One of the problems, especially on an enterprise network, is connected all these devices to wifi is not 
always plausible. Also, using a sub-1GHz rf band could consume less power. 

The first benefit is the one we shall focus on. Connecting the mbed or a small embedded device to the school's enterprise
network is not an easy task. Our IoT Sensor Network provides a solution.
## Description
The IoT Sensors Network is cloud communication done from a Sensor Pad (mbed LPC1768 and XBee with various sensor attached) 
to the BeagleBone PocketBeagle to the cloud). This cloud data is displayed on an IOS application.

The platforms used were a baremetal mbed, Debian 9.9 "stretch IoT", IOS, and the Amazon Web Services. 
The programming languges used were,
1. C++
2. Swift
## How to Build Project
### Block Diagram
![Diagram](https://github.com/AryaMirshafii/4180FinalProject/blob/master/Images/blockDiagram.png)

### Cloud Service
The API is hosted using Amazon Web Service's API gateway platform. AWS was chosen due to its ease of use, low cost, and high scalability. In order to upload the API to the AWS cloud, the [Serverless Framework](https://serverless.com/) is used to compile the source code into a .jar file and then upload it. The benefit of using serverless is that it handles deployment and scaling by itself, with minimal effort from the user. This is achieved by doing the following: (Note: this is just to upload using Serverless to AWS, and it is assumed that you already have an existing project)


1)Install Serverless using NPM:
```console
npm install -g serverless
```
2)Give serverless your AWS Access Key and Secret Token using:
```console
export AWS_ACCESS_KEY_ID=YOUR_ACCESS_KEY
export AWS_SECRET_ACCESS_KEY=YOUR_SECRET_ACCESS_KEY
```
NOTE: Your keys can be found by going into your AWS console and viewing your IAM user information. It should be noted that these keys can only be viewed once so if you already created an IAM user and you forgot your keys, you will need to regenerate new keys and write them down.

3)If the service is already created on AWS you will need to tear down the existing one by running:
```console
sls remove
```
4) To compile the source code into a .jar file using maven run:
```console
mvn clean install
```
5) Finally, to upload the newly generated .jar to AWS, run:
```console
sls deploy
```

That is all! Serverless handles all of the scaling and once the process is done, serverless will give you a list of API endpoints created which are able to be called using regular HTTP requests.

### Web Service API
### IOS Application
### mBed Sensor Pad
#### Wiring Diagram
#### Building mbed code
#### Parts List
### PocketBeagle
#### Wiring Diagram
#### Connecting the XBee module
#### Adding WiFi
#### Connecting the PocketBeagle to Eduroam
Use the Connman utility that comes with Debian.
1. sftp the eduroam.config file under the PocketBeagleCode directory to the PocketBeagle. Use Windows or Linux because the 
exposed USB network interfaces do not seem to come up with MacOS. Or you can write the code in eduroam.config with Vim or Nano
over serial console. 
On Linux:
```console
user@machine:~$ cd PocketBeagleCode 
```
```console
user@machine:~$ sftp debian@192.168.7.2
> put eduroam.config
> exit
```
Use the password temppwd
2. Connect Serially to the PocketBeagle (use screen or minicom or any other serial utility)
You have to find the device name of the PocketBeagle. On mac and linux is might begin with usbmodem on Windows it will come up
as a Comm devices
```console
user@machine:~$ screen /dev/tty.usbmodem* 115200
```
Login to the PocketBeagle using the username: debian and the password temppwd.
3. Move the eduroam.config file to the directory /var/lib/connman. You may have to do this as root.
```console
debian@machine:~$ cp eduroam.config /var/lib/connman
```
4. Edit the eduroam.config. This can only be done with root privileges. The identity is your institutional email. Passphrase is 
your instituional password. This was initially made for Georgia Tech, so the CACertificate field may have to be changed. Use the 
one that your institional uses for logging in via Linux operating systems. For example, I used the CACertificate from a Georgia 
Tech tutorial for getting on the network for Ubuntu. Contact the IT department at your school for more information. Edit the 
anonymous identity. Use eduroam@domain.edu. Domain is replaced by the domain used in your institutional email. Use Vim, Nano or 
your favorite console text editor. If you were not able to sftp in the network, you can create the file in this directory.
More information on this configuration file can be found [here](https://manpages.debian.org/testing/connman/connman-service.config.5.en.html).
```console
debian@machine:~$ cd /var/lib/connman
debian@machine:~$ sudo vim /var/lib/connman/eduroam.config
```
Now wait for the Eduroam to connect. If it doesn't you may have to restart Connman or start the PocketBeagle, using the button on
the top. Use this command to determine if you are connected. A new network interface will show up wlan0 and it will have a new 
eduroam IP Address assigned. You are now connected to the internet. 
```console
debian@machine:~$ ifconfig
```
Ping an IP Address to test the network.
```console
debian@machine:~$ ping 1.1.1.1
```
Ping google to test if DNS works. If this hanges the turn off IPv6.
```console
debian@machine:~$ ping google.com
```
If this doesn't work run these commands to turn off IPv 6
```console
debian@machine:~$ sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1
debian@machine:~$ sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1
```
if it doesn't connect to eduroam restart and run this command to diagnose what went wrong.
```console
debian@machine:~$ journalctl | grep connmand | less
```
#### Building Cloud Communication Program
## Conclustion
IoT networks work and improve a company or individual's ability capture sensor data from small embedded devices. Our IoT sensor 
network allowed us to capture sensor data and send it over Eduroam, the fastest internet network on campus. We found the other 
network GT-Other to be too slow for our application.
## Desired Improvements
1. Add security to the communication between Sensor Pad and PocketBeagle, also from PocketBeagle to the Web
2. Build a more robust application for cloud communication in C++.
3. Allow more than one sensor pad to be connected to a single PocketBeagle, requires XBee configuration.
4. Figure out how to get IPv6 hostnames resolved.
5. Add more sensors, sensor pads. 
## Helpful Links/Thanks to:
### mBED Sensors
### PocketBeagle Communication
### WiFi Configuration
## Parts List
