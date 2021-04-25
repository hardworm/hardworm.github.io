---
layout: post
title: Фильтры tiny rss
---

Есть у нас в городе местечковый портал новостей одной местечковой редакции газеты. И, внезапно, там появляются полезные новости типа: отключение воды и отопления, фестивали, интересные мероприятия. Но как и все российские СМИ они невероятно гадят в мозг своим трешем типа: «путин педофилы расчелененка». Я не люблю, когда у меня плохое настроение, хотелось бы больше позитивной информации или хотя бы полезной. Читаю rss ленты tiny rss установленным на своем VPS.
У tinyRss есть полезная функция фильтры, их мы и настроим:

* Идем в Настройки, вкладка Метки — создаем метку «Треш» или что-то похожее
* Там же вкладка Фильтры, создаем фильтр и называем его.
* В секцию «Искать» добавляем стоп слова (внимание, заголовок бьется на слова и уже по ним ищется, сочетания слов не работают). При это можно указать разные параметры типа в какой ленте искать и по какому полю.
* В секцию «Apply actions» добавляйте действия. Рекомендую присваивать метку «Треш» и менять статус на «Как прочитанные». (метка работает всегда и на старых и на новых постах, а вот статус нет).
* Ставим галку «Включен» и «Match any rule».

Готово!
Вот вам мой список слов.

```text
[оО]граб*
[Пп]ьян*
[Сс]толкнов*
[Сс]уд
[тТ]ело
[Уу]голов*
[Уу]краи*
автомобилист
алко*
ВАЗ*
вор*
врач*
врезал*
выбросил*
депутат
драку
ДТП
журналист
задержа*
застрелил*
зек
изнасил*
обокрал
погиб
поджог*
полиц*
пострад*
протарани*
разыскивают
ранен*
Савч*
сбил
сгорел*
СИЗО
смерт*
собутыльник*
ссор*
столкнул*
уби*
угнал*
украл*
умер*
утонул*
наркот*
```

![_config.yml]({{ site.baseurl }}/images/20140809/tinyrss_filter.png)