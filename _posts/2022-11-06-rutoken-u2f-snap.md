---
layout: post
title: Дружим Rutoken U2F с firefox в snap ubuntu
---

В век "ломаем всё и вся" U2F не роскошь, а средство выживания.
Одним из самых дешевых U2F токенов является Rutoken. На момент написания его можно купить за 1700 рублей + доставка.
И он будет в твоих руках дня за 3. 

![_config.yml]({{ site.baseurl }}/images/20221106/rutoken_u2f.jpg)

Однако, со свежим Firefox в LTS Ubuntu 22.04 он не работает.
Всё дело в том, что Firefox запихнули в snap. А snap как песочнице нужен "проброс" устройств.

Необходимо сделать:
1. Узнать информацию по токену через команду `lsusb`
```shell
Bus 001 Device 061: ID 0a89:0090 Aktiv U2F
```
2. Добавить строки в **нужные** секции (в конец файла нельзя!!!) `sudo nano /etc/udev/rules.d/70-snap.firefox.rules`
```shell
SUBSYSTEM=="hidraw", KERNEL=="hidraw*", ATTRS{idVendor}=="0a89", ATTRS{idProduct}=="0090", TAG+="snap_firefox_firefox"
SUBSYSTEM=="hidraw", KERNEL=="hidraw*", ATTRS{idVendor}=="0a89", ATTRS{idProduct}=="0090", TAG+="snap_firefox_geckodriver"
```
3. Перезагрузить правила `sudo udevadm control --reload-rules`
4. Перезапустить firefox


Всё Rutoken u2f должен заработать. Одна проблема - после обновления может затереться конфигурационный файл и придется повторять.
