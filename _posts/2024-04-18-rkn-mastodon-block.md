---
layout: post
title: Проверка блокировки РКН для mastodon инстансов
---

Только mastodon инстансов забанено РКН 30 штук.

Как я это получил:

1. Скачиваем список mastodon инстансов

```bash
wget https://instances.social/list/old -O Mastodon_list.html
```
2. Превращаем html в список доменов в txt файле

```bash
grep -E '<strong><a href=\\"http' Mastodon_list.html | grep -o 'data-value="[^"]*">' | sed 's/^.*="\(.*\)">$/\1/' > Mastodon_list.txt
```

3. Скачиваем список блокировок РКН с github

```bash
wget https://github.com/zapret-info/z-i/raw/master/dump.csv
```

4. Пишем bash скрипт для проверки доменов

```bash
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
    if grep -qiF "$line" dump.csv; then
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

