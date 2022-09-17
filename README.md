### Пишем скрипт

```
#!/bin/env bash

logfile=/var/log/access-4560-644067.log # предоставленный файл для задания

#hour=$(date +%H)  #
hour=17 	   # Указываем час вручную для примера, поскольку команда date показывает текущее время, но можно раскрыть эту строку, тогда будет работать динамически
let hour-=1        # Минусуем один час
#year=$(date +%Y)  # Так же вручную указываем год	
year=2019          #
grep "$year:$hour:" $logfile > ./time_log # создаение временого файла с выборкой по часу

echo
echo ========History by last 1 hour===========
echo "start from $hour to $(($hour + 1))"    # Вывод "start from 16 to 17"


topip=$(awk '{print $1}' ./time_log | sort | uniq -c | sort -rn | head -10)          #
topreq=$(awk -F'"' '{ print $2}' ./time_log | sort | uniq -c | sort -rn | head -10)  #  Парсим временный файл
topcode=$(awk '{ print $9 }' ./time_log | sort | uniq -c | sort -rn | head -10)	     #


echo -e "${topip} \n\n ${topreq} \n\n ${topcode}"  # Вывод топ IP, запросов, кодом за последний час

exit 0
```

#### Добавляем команду в cron:

0 */1 * * * flock -w0 /bin/bash /root/bin/parser_access.sh | mailq -s "access by hour" root@lvm.localdomain # запускаем команду раз в час с проверкой наличия запущенной копии, если ее нет, команда будет запущена и отправка на указанный адрес.
 
