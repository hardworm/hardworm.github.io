---
layout: post
title: Про патч sched_autogroup для linux
---

В месте с общей волной решил попробовать «killer feature» патч, про который  прочитал [тут](http://www.opennet.ru/opennews/art.shtml?num=28671). С новым ядром 2.6.36 возникли проблемы — ядро собралось и загрузилось, но установить nvidia драйвера не удалось.  Однако, нашел на http://pavlinux.ru/ патч переделанный под ядро 2.6.35.8 http://pavlinux.ru/krnl/sched_autogroup-2.6.35.8.patch.bz2  Поставил. Все работает. Драйвера nvidia ставятся и работают.

Впечатления не однозначные. Переключение между окнами стало моментальное. Особенно стало комфортно работать с виртуальной машиной — переключение между виртуальной машиной и программами происходит быстро. Но, те же виртуальные машины стали чуть дольше запускаться. Так же запускаются медленнее программы из-за того, что отсутствие BFS  патч.

Вообщем, тем кто работает с виртуальными машинами, компилирует в 64 патока и работает с тяжелыми приложениями  и надо часто переключатся между этими приложениями на клиент мгновенных сообщений или браузер — этот патч для вас.