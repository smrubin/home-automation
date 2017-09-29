Home Lab and Automation

This guide will walk through the setup and configuration for my home network and automation. A list of the hardware is in homelab.md, and the contents of this repo primarily are for a [home-assistant](https://home-assistant.io/) setup using [hass.io](https://home-assistant.io/hassio/).

### Home-Assistant

Home-assistant is an open source home automation platform, like Samsung SmartThings. There are lots of ways to install home-assistant, especially on the Raspberry Pi.

Hass.io combines home-assistant with a host controller and a supervisor layer. Not only does it make for quicker install, but it can control the OS and manage docker containers. This means that it can do things like restart the raspberry pi and manage the network. It also gives hassio the ability to have modular components called add-ons, enabling configuration to be done in an easy way.

An awesome resource for learning more about hassio is the [BRUH Automation YouTube Channel](https://www.youtube.com/channel/UCLecVrux63S6aYiErxdiy4w).

### Getting Started

1) Go here: https://home-assistant.io/hassio/installation/
2) Download the image for your hardware (e.g. Raspberry Pi 2)
3) Flash the downloaded image to an SD card using Etcher
4) Insert SD card into RPi and connect to router
5) Hassio will boot up on the pi and will automatically detect some of your connected devices
6) Access hassio user interface at: http://hassio.local:8123/

While configuring the RPi, I also gave it a static IP address. You also have the ability to enable WiFi if you want, but I won't be using that for my setup.

To give the RPi a static IP address, follow the instructions here: [https://docs.resin.io/deployment/network/2.0.0/](https://docs.resin.io/deployment/network/2.0.0/). On the SD card, add a config file (mine is called resin-eth) in the system-connections directory. My resin-eth file in this repo has been configured for a static IP address of 192.168.1.169.

### Making Configuration Easier

I've installed a couple of hassio add-ons to make configuration easier: Samba sharing, auto-update chekcer, and an ssh server.

#### Samba Sharing

Samba Sharing allows your computer to access the pi file structure as a network drive.

It can be installed via the hassio UI and then accessed under shared networks in Finder.

#### SSH

Install the hassio ssh add-on to ssh into the RPi. I am using a public/private key setup. GitHub has a guide [here](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) for generating a new ssh key and adding it to the ssh-agent.

Copy and paste the public key into the hassio ssh add-on user interface.

Run `ssh root@hassio.local`.
Run `hassio help` inside the pi.

More can be found here: https://github.com/hassio-addons/addon-ssh

You can also specify the IP address of the Rpi instead of hassio.local. To find the RPi IP address:

Run `nmap -sn 192.168.1.0/24`. The -sn option tells nmap not to run a port scan and just do host discovery.

Reference: https://www.raspberrypi.org/documentation/remote-access/ip-address.md

#### Home Assistant Version Auto-Checker

Install the "Check Home Assistant COnfiguration" add-on, which detects updates to home-assistant and allows one-click updates.

### External Network Access

This setup will include external network access via port-forwarding and securing external access via a DNS Service (DuckDNS) and SSL encryption with Let's Encrypt.

Great guide and explanation here: https://www.youtube.com/watch?v=BIvQ8x_iTNE

To setup port forwarding, access the router config.

For Edge Router X: 192.168.1.1

Firewall/NAT > Port Forwarding > Port Forwarding Rules > Add Rule

External Port 443 to Internal 8123 at the IP address of the RPi

Public IP address can be found in Edge Router X under Dashboard > Interfaces > eth0.

#### DNS Service - DuckDNS

Duck DNS is a free service which will point a DNS (sub domains of duckdns.org) to an IP of your choice. Long story short, it will translate the public IP address to a readable URL.

Go to https://www.duckdns.org/ and login.
Create a new subdomain.
Go to Install tab
Select pi
Choose subdomain
Make sure to grab the domain and token info for the hassio config.

Install DuckDNS via the hassio GUI and copy your subdomain name and token into the options.

Nice thing about DuckDNS is that it will detect if your public IP address has changed from your provider, and will update the DuckDNS configs appropriately.

#### Let's Encrypt

LetsEncrypt is a free, automated, and open certificate authority that generates an SSL certificate for our new (sub)domain. This will allow us to connect via https and use a password to access our hassio instance externally. This ssl encryption will encrypt our traffic to help prevent snooping.

Install the LetsEncrypt add-on via the hassio GUI. Follow the instructions here: https://home-assistant.io/addons/lets_encrypt/. Note that you will have to update your home-assistant configuration and you should also add a password via the api_password option.

#### NGINX SSL Proxy

This add-on will setup an nginx ssl proxy and redirect http traffic to https.

Install the nginx ssl proxy via the hassio GUI and enter the domain name into the options config.

### Home Assistant Configuration

#### Managing Secrets

Using secrets with home-assistant allows us to use a single file for all of our private information and call it from another file (typically our configuration.yaml). THis way we can share our home-assistant configuration file without sharing sensitive information.

Create a new file called secrets.yaml. Add a new line for the secret variable: `api_password: dummypassword`

Then reference that secret variable on configuration.yaml via the notation: `api_password: !secret api_password`. Restart home-assistant.

#### Snapshotting

Hassio has a feature that allows us to take snapshots of the configuration for backup and restoration at a later time.

In the UI, click the three vertical dots in the top-left corner, then click snapshosts.

Give a new snapshot a name, then hit create. It will now be listed under available snapshots for restoration. This feature can even be used for restoring between different hardware (e.g. RPi0 to RPi3).

### Up Next

MQTT Broker: Mosquito:
Protocol for communicating between IoT devices

Google-assistant:
Turn pi into a google home with speaker and microphone

Emulated Hue

Haska

Snips

Native Conversation

Gladys
