---
layout: post
title: Проверка блокировки РКН для mastodon инстансов
---

Только mastodon инстансов забанено РКН по полному соответствию домена всего 3.

Как я это получил:

1. Скачиваем список mastodon инстансов

```shell
wget https://instances.social/list/old -O Mastodon_list.html
```
2. Превращаем html в список доменов в txt файле

```shell
grep -E '<strong><a href=\\"http' Mastodon_list.html | grep -o 'data-value="[^"]*">' | sed 's/^.*="\(.*\)">$/\1/' > Mastodon_list.txt
```

3. Скачиваем список блокировок РКН с github

```shell
wget https://github.com/zapret-info/z-i/raw/master/dump.csv
```

4. Пишем bash скрипт для проверки доменов

```shell
#!/bin/bash

# Проверяем, существует ли файл Mastodon_list.txt
if [ ! -f "Mastodon_list.txt" ]; then
    echo "Файл Mastodon_list.txt не найден."
    exit 1
fi

# Проверяем, существует ли файл dump.csv
if [ ! -f "dump.csv" ]; then
    echo "Файл dump.csv не найден."
    exit 1
fi

total_lines=$(wc -l < "Mastodon_list.txt")
current_line=0

# Построчно читаем строки из Mastodon_list.txt и проверяем их наличие в dump.csv
while IFS= read -r line; do
    if grep -qiF ";$line;" dump.csv; then
        echo "$line забанен РКН" >> mastodon_rkn_ban.txt
    fi
    # Обновляем прогресс-бар
    ((current_line++))
    percentage=$((current_line * 100 / total_lines))
    progress=$(printf "%-${percentage}s" "#")
    echo -ne "[$progress] $percentage%\r"
done < "Mastodon_list.txt"
```

5. Запускаем скрипт `bash check.sh` результатом работы которого является файл `mastodon_rkn_ban.txt`

Проверяем полное вхождение по списку “Fediverse Observer”. Таких уникальных доменов 11

1. Скачиваем список

```shell
curl 'https://api.fediverse.observer/' --compressed -X POST -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:124.0) Gecko/20100101 Firefox/124.0' -H 'Accept: */*' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate, br' -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' -H 'Origin: https://fediverse.observer' -H 'DNT: 1' -H 'Connection: keep-alive' -H 'Referer: https://fediverse.observer/' -H 'Sec-Fetch-Dest: empty' -H 'Sec-Fetch-Mode: cors' -H 'Sec-Fetch-Site: same-site' --data-raw '{"query":"{nodes(softwarename:\"\" status: \"UP\"){domain uptime_alltime signup total_users countryname greenhost detectedlanguage name softwarename shortversion comment_counts local_posts monthsmonitored}}"}' > observer.json
```

2. Преобразуем его в список доменов в txt

```shell
cat observer.json | jq | grep domain | cut -d '"' -f 4 > Mastodon_list.txt
```
3. Запускаем скрипт `bash check.sh` результатом работы которого является файл `mastodon_rkn_ban.txt`