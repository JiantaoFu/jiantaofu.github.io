# Basic setup

You can use [Raspberry Pi 4](https://www.amazon.com/GeeekPi-Raspberry-4GB-Starter-Kit/dp/B0B3M2HKN6/ref=sr_1_12_sspa?crid=2L2JEUJ1460WB&dib=eyJ2IjoiMSJ9.dm2FB5zZz0j71xQemzz3eGcfMj6yAgNj0EPv-ZREy2PWNx49GJkdHYAzErmjO1rWARxfo12XUNdoTscBLFfbLbRIYyt2O44eh6kBKePKgZHoWzqG1x61SEy_n7vicrrWGgnVkLoWBoS1rY0LU453Xc8SXZtyYgzT9ZGvLWKovR1k4qtC_FNH2zz6wdvKDwm1cEi8riFowfxHNBwFO9r6uRsuyK5wCfuxhzNEeM3-ugw.eOUzPFP7-jr6HburUXKHBVMMswaal6qpIhVdoWuOVjI&dib_tag=se&keywords=raspberry+pi+starter+kit&qid=1726551003&sprefix=raspberry+pi+starter+ki%2Caps%2C176&sr=8-12-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9tdGY&psc=1) (or Raspberry Pi 3B) as a network emulator. Raspberry Pi can be used to set up a bridged network, either using ethernet or WiFi.

You can [download](https://www.dropbox.com/scl/fi/e02utgcmfw5opfymdq9h7/image_2023-05-10-piem-lite.zip?rlkey=l8b32tgl41zoqa6nupxq2y41r&st=ad66ldbu&dl=0) the prebuild image, and then download [Etcher](https://etcher.io/) and install the image on the micro sdcard.

Once done, connect keyboard monitor, and ethernet, boot and login with username “pi” and password “raspberry”, then execute the following command to enable WiFi network:

```
rfkill unblock all
```

Then reboot, login again and make sure the network is working:

using ping command to make sure the internet access is working

Use cell phone to scan the wifi network and find ssid “piem”, and login with password “piemulator”, and make sure the internet access is working

If not working, try run `sudo systemctl restart networking.service` and wait a few minutes

Then you can test network impairment:

Run speed test on Raspberry Pi

```
sudo apt install speedtest-cli
speedtest-cli
Retrieving speedtest.net configuration...
Testing from Comcast Cable (98.37.129.143)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Jefferson Union High School District (Daly City, CA) [62.00 km]: 16.523 ms
Testing download speed................................................................................
Download: 483.86 Mbit/s
Testing upload speed......................................................................................................
Upload: 112.79 Mbit/s
```

2. Run speed test on cell phone connected with WiFi “piem”. (The speed test may not be as good as you run it on Raspberry Pi, most likely due to interference, default the channel is set to 1)

3. Test rate limit, set downlink bandwidth to 3mbps, “192.168.1.185” is the IP address of the cellphone

```
sudo emulator.py add -b 3000 -f 192.168.1.185 -c downlink
```

4. Then run speed test on cell phone again to validate the settings

5. Remove the rate limit and test it again

```
sudo emulator.py remove -f 192.168.1.185 -c downlink
```

6. Run sudo emulator.py -h for help

You can refer https://github.com/cellsim/piem for more details

# Performance tune

Modify “/etc/hostapd/hostapd.conf”, change/add the following settings:

1. Use 5GHz network
2. Search for the channel with the least interferences
3. Enable 802.11n support
4. Enable 802.11ac support
5. Enable QoS support
6. Enable HT40 with short guard intervals

```
hw_mode=a
channel=0
ieee80211n=1
ieee80211ac=1
wmm_enabled=1
ht_capab=[HT40+][SHORT-GI-40]
```

Then restart hostapd:

```
sudo systemctl restart networking.service
```

There are chances that channel = 0 doesn't work (at least for me), and you won't be able to find "piem" network anymore. You can enable logging by adding the following line in "/etc/default/hostapd":

```
DAEMON_OPTS="-dd -t -f /tmp/hostapd.log"
```

Restart hostapd and make sure it runs successfully by checking systemctl status hostapd.service, otherwise check error log.

If channel auto selection doesn’t work, change that to channel = 36 or other Non-DFS Channels like: 40, 44, 48, 149, 153, 157, 161, 165. Use the following command on Raspberry Pi to check the channel in use nearby:

```
sudo iwlist wlan0 scan | grep "Channel"
```

After making those changes, you should be able to see the performance boost (my test shows 3x improvement, around 60mbps ~ 80mbps).