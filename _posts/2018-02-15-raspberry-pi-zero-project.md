---
layout: post
title:  "Raspberry Pi Zero Project"
category: IOT
tags: [raspberryPi]
---

## 기본 Node Settings

**기존 nodejs 삭제**
```bash
sudo apt-get remove --purge nodejs
sudo apt-get autoremove
```
**nodesource에서 nodejs 최신버전 다운로드**
```bash
curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -
sudo apt-get install -y nodejs
```

<!-- more -->


## Express 설치
```bash
npm install -g express
npm install -g express-generator
```

## Aircrack 설치

**apt 업그레이드**
```bash
sudo apt-get update && sudo apt-get upgrade
```
**rpi 업데이트**
```bash
sudo rpi-update
```
**라이브러리 설치**
```bash
sudo apt-get install libssl-dev and sudo apt-get install libnl-dev and sudo apt-get install sqlite
wget http://download.aircrack-ng.org/aircrack-ng-1.2-rc4.tar.gz
tar -xvf aircrack-ng-1.2-rc4.tar.gz
cd aircrack-ng-1.2-rc4/
sudo make
sudo make install
```

**Airmon test**
sudo airmon-ng


## Network Settings

raspberry pi config(locale) window
> raspi-config

show network adapters
> iwconfig

scan wifi
> sudo iwlist wlan0 scan

wifi setup file location
> /etc/wpa_supplicant/wpa_supplicant.conf



## Network WPA_SUPPLICANT.conf

```bash
network={
  ssid="my_ssid"
  psk="my_pw"
  proto=RSN
  key_mgmt=WPA_PSK
  pairwise=CCMP
  auth_log=OPEN
}
```




**raspberry pi project**
[link](http://)
