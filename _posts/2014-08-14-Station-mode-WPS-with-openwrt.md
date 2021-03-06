---
published: true
layout: post
title: Station mode WPS with openwrt
category: blog
published: true
tags: openwrt, wifi, linux
---

The problem is to use an openwrt(applies otherwise too) installation to connect to an AP using wps, and to save the network details to save this setting. This does not work without modifications in Attitude Adjustment, and is not(yet) present in Bleeding Edge. Hopefully it will be supported sometime.

AP mode WPS is somewhat documented and working [Openwrt Wifi configuration](http://wiki.openwrt.org/doc/uci/wireless#wps.options).

You need `wpa_cli`, (and I have tested with full `wpa_supplicant` package) to use `wps_pbc` (WPS push button method, which is the only one I have used, but others will probably too). WPS push button is in which the user pushes a button on both the AP, and on the client, and authentication etc. takes place w/o any more input form user, and client device connetct to the access point.

What is supposed to happen in a device is that the button push somehow sends `wpa_supplicant` the `wps_pbc` command. For host mode, openwrt wiki has an example which uses hotplug mechanism. A gpio button is used with the gpio_hotplug driver, so that it generates an even on button push and hotplug program catches it, ond a user script catches this event and sends `wps_pbc` to hostapd, like `hostapd_cli -p /var/run/hostapd-phy0 wps_pbc`.

The only difference in station mode is to send `wps_pbc` command to `wpa_supplicant` instead, as in `wpa_cli -p /var/run/wpa_supplicant/ wps_pbc` (path is different depending on openwrt release, or on some other distro).

To get network SSID, key, and encryption, though, the only way I could find was to have the setting `update_config` to be present in wpa_supplicant.conf file, which is not present in the configs generated by openwrt scripts in attitude adjustement and bleeding edge as of now. In attitude adjustment, you can edit wpa_supplicant.sh which generates the .conf from your UCI settings, to add `update_config=1` in the file. In bleeding edge, i had to edit netifd.sh to do the same change.

Now when you connect to an AP succesfully, the config file will be updated with the current network parameters. You can grep these values, SSID, key can be used as it is(i think), and you may have to map the cipher to encryption which UCI config understands. 

Update: It seems that WPS is not currently working with the AP being in WPA(or psk in uci syntax). It worls
nearly every time now that I changed this to WPA2/psk2. As of now there seem to be only 2 people interested in solving this issue, me and somebody who contacted me on twitter because of a forum post of mine on forums.openwrt.org.