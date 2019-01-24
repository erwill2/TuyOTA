# TuyOTA
Flashing Tuya devices with Tasmota firmware.


To perform this walk through I installed Raspbian 9 (stretch) using NOOBS v3.0.0.

The only change made to the install was enabling ssh so I could access the Pi remotely.

pi@raspberrypi:~ $ sudo apt update 
Get:1 http://raspbian.raspberrypi.org/raspbian stretch InRelease [15.0 kB]
Get:2 http://archive.raspberrypi.org/debian stretch InRelease [25.4 kB]        
Get:3 http://raspbian.raspberrypi.org/raspbian stretch/main armhf Packages [11.7 MB]
Get:4 http://archive.raspberrypi.org/debian stretch/main armhf Packages [201 kB]
Get:5 http://archive.raspberrypi.org/debian stretch/ui armhf Packages [41.3 kB]
Fetched 11.9 MB in 9s (1,323 kB/s)                                             
Reading package lists... Done
Building dependency tree       
Reading state information... Done
65 packages can be upgraded. Run 'apt list --upgradable' to see them.
Just updating the package lists.

pi@raspberrypi:~ $ sudo apt install hostapd
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libnl-route-3-200
The following NEW packages will be installed:
  hostapd libnl-route-3-200
0 upgraded, 2 newly installed, 0 to remove and 65 not upgraded.
Need to get 576 kB of archives.
After this operation, 1,565 kB of additional disk space will be used.
Do you want to continue? [Y/n] 
Get:1 http://raspbian.mirror.uk.sargasso.net/raspbian stretch/main armhf libnl-route-3-200 armhf 3.2.27-2 [113 kB]
Get:2 http://raspbian.mirror.uk.sargasso.net/raspbian stretch/main armhf hostapd armhf 2:2.4-1+deb9u2 [463 kB]
Fetched 576 kB in 0s (1,142 kB/s)
Selecting previously unselected package libnl-route-3-200:armhf.
(Reading database ... 133215 files and directories currently installed.)
Preparing to unpack .../libnl-route-3-200_3.2.27-2_armhf.deb ...
Unpacking libnl-route-3-200:armhf (3.2.27-2) ...
Selecting previously unselected package hostapd.
Preparing to unpack .../hostapd_2%3a2.4-1+deb9u2_armhf.deb ...
Unpacking hostapd (2:2.4-1+deb9u2) ...
Setting up libnl-route-3-200:armhf (3.2.27-2) ...
Processing triggers for libc-bin (2.24-11+deb9u3) ...
Processing triggers for systemd (232-25+deb9u6) ...
Processing triggers for man-db (2.7.6.1-2) ...
Setting up hostapd (2:2.4-1+deb9u2) ...
Processing triggers for systemd (232-25+deb9u6) ...
Installing hostapd which is the only non standard package that needs to be installed. Everything else should come with the basic Raspbian installation.

pi@raspberrypi:~ $ sudo sed -i '3idenyinterfaces wlan0' /etc/dhcpcd.conf
pi@raspberrypi:~ $ head -3 /etc/dhcpcd.conf
# A sample configuration for dhcpcd.
# See dhcpcd.conf(5) for details.
denyinterfaces wlan0
This was just a fancy way of adding denyinterfaces wlan0 to the dhcpcd.conf. Use your favourite editor if you wish.

The purpose of the modification is to stop wpa_supplicant from handling the wlan0 interface. If it isn't stopped then it will prevent the script from running correctly.

pi@raspberrypi:~ $ ps -fu root | grep wpa_supplicant
root       370     1  0 20:46 ?        00:00:00 wpa_supplicant -B -c/etc/wpa_supplicant/wpa_supplicant.conf -iwlan0 -Dnl80211,wext
pi@raspberrypi:~ $ sudo systemctl restart dhcpcd
pi@raspberrypi:~ $ ps -fu root | grep wpa_supplicant
The dhcpcd service needed to be restarted so that the changes made to dhcpcd.conf take effect.

If wpa_supplicant is still running then try a reboot and if that doesn't work try killing the process.

pi@raspberrypi:~ $ git clone https://github.com/SynAckFin/TuyOTA
Cloning into 'TuyOTA'...
remote: Enumerating objects: 17, done.
remote: Counting objects: 100% (17/17), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 17 (delta 5), reused 9 (delta 3), pack-reused 0
Unpacking objects: 100% (17/17), done.
pi@raspberrypi:~ $ cd TuyOTA
Get the script

pi@raspberrypi:~/TuyOTA $ sudo ./tuyota.pl -ip 192.168.31.45 -s MyHomeNet -p MySecret
Without the -ip flag the script will look for devices that never completed the install and attempt to continue.

If you leave out the -s flag then if the install succeeds you will have to connect to the device yourself to enter the SSID and password for your home network.

Stage One firmware not found, downloading it
--2019-01-15 21:01:18--  https://github.com/SynAckFin/TuyOTA/raw/master/static/image_user2-0x81000.bin
Resolving github.com (github.com)... 140.82.118.3, 140.82.118.4
Connecting to github.com (github.com)|140.82.118.3|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://raw.githubusercontent.com/SynAckFin/TuyOTA/master/static/image_user2-0x81000.bin [following]
--2019-01-15 21:01:19--  https://raw.githubusercontent.com/SynAckFin/TuyOTA/master/static/image_user2-0x81000.bin
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.16.133
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.16.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 239220 (234K) [application/octet-stream]
Saving to: ‘image_user2-0x81000.bin’

image_user2-0x81000 100%[===================>] 233.61K  --.-KB/s    in 0.07s   

2019-01-15 21:01:19 (3.23 MB/s) - ‘image_user2-0x81000.bin’ saved [239220/239220]

Stage Two firmware not found, downloading it
--2019-01-15 21:01:19--  https://github.com/SynAckFin/TuyOTA/raw/master/static/sonoff.bin
Resolving github.com (github.com)... 140.82.118.3, 140.82.118.4
Connecting to github.com (github.com)|140.82.118.3|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://raw.githubusercontent.com/SynAckFin/TuyOTA/master/static/sonoff.bin [following]
--2019-01-15 21:01:20--  https://raw.githubusercontent.com/SynAckFin/TuyOTA/master/static/sonoff.bin
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.16.133
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.16.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 482512 (471K) [application/octet-stream]
Saving to: ‘sonoff.bin’

sonoff.bin          100%[===================>] 471.20K  --.-KB/s    in 0.1s    

2019-01-15 21:01:20 (4.31 MB/s) - ‘sonoff.bin’ saved [482512/482512]
The script only loads the two firmware images if it can not find them so if you run the script twice then the download will be skipped the second time.

Getting interface into stable state
RTNETLINK answers: Cannot assign requested address
RTNETLINK answers: Cannot assign requested address
Done
This is just an attempt to get the WiFi interface into a consistent state before attempting to use it. Errors are expected here.

Using WiFi device wlan0 for Access Point
This lets you know what WiFi device it is going to use. It should automatically detect the WiFi devices on your computer and picks one. You should ensure that the named device is the same one that was added to the dhcpcd.conf file previously.

Creating Access Point config file hostapd.conf
The hostapd.conf file is only created once. This means that if you experience problems with hostapd you can modify the file and try again. As an example, the default configuration specifies WPA2 but some people have experienced problems with this so you can remove the wpa=2 and wpa_passphrase=un49fqxc lines from the hostapd.conf file and the access pint it creates will be open.

Starting Access Point with SSID ZAGDU-789
Giving Access Point IP address 10.44.57.1, pid is 1563
The access point has started

Redirecting device 192.168.31.45 to use Access Point ZAGDU-789
**** Redirect appears successful
This is an attempt to get your device to use the newly created access point instead of your home network. Errors often occur here and a second attempt will be made later.

If you have any programs or applications running that access the device in any way then you should stop them.

If the error persists then try rebooting the device just before running the script.

DHCP Discover 88:99:aa:bb:cc:dd 10.44.57.222
DHCP Request 88:99:aa:bb:cc:dd 10.44.57.222
This is the script's built in DHCP server handing out an IP address. It is a good sign and is probably your device, but could also be because something else on your network just happened to ask for an address at this time.

**** New device detected. ID: 06823735bcddc20dec14 IP:10.44.57.222
This is good news. Note that the IP address is the same as the one handed out by the DHCP server.

This means that the device is now using the script's ZAGDU-789 access point. If you suspected that your device had turned into a brick then this demonstrates that it is still in working order.

**** New device looks to be part way through upgrading
**** Forcing it to retry the upgrade
Redirecting device 10.44.57.222 to use Access Point ZAGDU-789
**** Redirect appears successful
This is just in case this is a 2nd or 3rd run of the script. This should force the device to re-connect and re-check for upgrades.

Received DNS query for mq.gw.tuyaeu.com.
Sending 10.44.57.1 as response
Received DNS query for mq.gw.tuyaeu.com.
Sending 10.44.57.1 as response
Received DNS query for mq.gw.tuyaeu.com.
Sending 10.44.57.1 as response
Received DNS query for mq.gw.tuyaeu.com.
Sending 10.44.57.1 as response
DHCP Discover 88:99:aa:bb:cc:dd 10.44.57.222
DHCP Request 88:99:aa:bb:cc:dd 10.44.57.222
Received DNS query for mq.gw.tuyaeu.com.
Sending 10.44.57.1 as response
Multiple DHCP and DNS queries are expected and show the device is working.

Accepting MQTT connection, forwarding to mq.gw.tuyaeu.com.
The device needs to able to talk to the Tuya's messaging server so the script proxies the connections to it.

Received DNS query for a.tuyaeu.com.
Sending 10.44.57.1 as response
This particular query and response sets up the http interception

Receiving www request
Fetching Request Content
URL: /gw.json?a=atop.online.debug.log
Response: HTTP/1.1 200 OK
{"result":true,"t":1547586191,"e":false,"success":true}
An example of a request and response. Note the "e":false and "success":true indicates that Tuya thinks that everything is okay. if e is ever true or success is ever false then it is unlikely the upgrade will proceed. You may have to factory reset and re-provision via the "SmartLife" app before trying again.

Receiving www request
Fetching Request Content
URL: /gw.json?a=tuya.device.upgrade.silent.get
Sent upgrade response
This is the important one. if you never see the /gw.json?a=tuya.device.upgrade.silent.get then increasing the timeout might work. Try add the flag -t 60 to the command line.

Receiving www request
Fetching Request Content
URL: /gw.json?a=s.gw.upgrade.updatestatus
Response: HTTP/1.1 200 OK
{"t":1547586211,"e":false,"success":true}
The first updatestatus request is your device telling Tuya "I'm upgrading". If you see a second updatestatus immediately after the first it is usually the device telling "Tuya" it isn't upgrading due to some problem.

Received DNS query for fakewebsite.
Sending 10.44.57.1 as response
This shows the device is going to try and get the firmware

Receiving www request
Sending firmware image_user2-0x81000.bin
With the first one the device is getting the firmware header

Receiving www request
Sending firmware image_user2-0x81000.bin
Sending bytes 239271-478490 from offset 0
This is the device getting the actual firmware

DHCP Discover 88:99:aa:bb:cc:dd 10.44.57.222
DHCP Request 88:99:aa:bb:cc:dd 10.44.57.222
The device reboots after getting the firmware and usually requests an IP

Shutting down...
This is the access point shutting down. It shuts down after 30 seconds of nothing significant happening and is normal.

Setting up IP Address 192.168.4.2 for Final Stages
Getting interface into stable state
RTNETLINK answers: Cannot assign requested address
Done
The WiFi interface is now getting set up for the final stages.

Setting up wifi scan
A WiFi access point scanner is set up to look for devices in the final stages. You can jump straight to this point by using the -b 2 flag

Setting up listener for FinalStage
Devices that have had the inital firmware installed try to fetch the next firmware from a web server on port 8080 so the script sets its own one up on that port.

***** FinalStage Detected ******
***** Connected to FinalStage ******
The scanner set up previously detect a device with an access point name of "FinalStage" so the WiFi card connected to it.

***** Receiving FinalStage Request ****
REQ: Client closed connection while receiving request: 
This can happen. The device is so impatient that if it doesn't get an instant response it abandons the request and tries again.

***** Receiving FinalStage Request ****
Sending firmware sonoff.bin
***** Tasmota Firmware sent to device ******
Your device should now have the Sonoff Tasmota firmware installed.

You may occasionally see more FinalStage mesages as the WiFi scanner might have them cached them, but it isn't anything to be concerned about.

***** Found Sonoff AP sonoff-3092 ******
***** Connected to sonoff-3092 ******
Sending config to sonoff-3092
***** Config sent *****
If you specified an SSID and password on the command line then the device gets configured with them.

***** Found Sonoff AP sonoff-3092 ******
***** Connected to sonoff-3092 ******
Failed to connect to device sonoff-3092: No route to host
Nothing to worry about, just the scanner have an out of date list.

Shutting down...
Getting interface into stable state
RTNETLINK answers: Cannot assign requested address
Done
Finished
Exiting....
Shutting down...
If everything went well you have a working device.
