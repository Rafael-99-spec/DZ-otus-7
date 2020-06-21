#					ДЗ №7 Systemd
-----------------------------------------------------------------------
## 1) Написать service, который будет раз в 30 секунд мониторить лог на предмет наличия ключевого слова (файл лога и ключевое слово должны задаваться в /etc/sysconfig)
Создадим файл конфигурации /etc/sysconfig/findword для нашего сервиса. Для ключевого слова и пути к лог файлу назначим переменные LOG и WORD. 
```
Configuration file for my findword service
WORD="systemd"
LOG=/var/log/findword.log
```
Далее создадим сам /var/log/findword.log лог файл с ключевым словом - systemd 
```
1       sysv            123
2       systemd         456
3       systemd         789
4       systemd         101112
5       systemd         131415
6       systemd         161718
7       systemd         192021
8       systemd         222324
9       systemd         252627
10      systemd         282930
11      sysv            313233
12      sysv            343536
13      sysv            373839
14      sysv            404142
15      sysv            434445
```
После чего создаем два юнита /etc/systemd/system/findword.service и /etc/systemd/system/findword.timer соответственно. 

Файлы конфигурации наших юнитов выглядят след. образом
Единственной функцией сервис юнита является поиск ключевого слова с помощью утилиты grep.
/etc/systemd/system/findword.service
```
[Unit]
Description=Starting findword service

[Service]
Type=oneshot
EnvironmentFile=/etc/sysconfig/findword
ExecStart=/bin/grep $WORD $LOG
```
Наш таймер юнит прдназначен для того чтобы запускать указанный в секции [Timer] юнит (Unit=findword.service), раз в 30 секунд.
/etc/systemd/system/findword.timer
```
[Unit]
Description=Run required service once in 30 seconds

[Timer]
OnActiveSec=0sec
OnBootSec=1min
OnCalendar=*:*:0/30
AccuracySec=1us
Unit=findword.service

[Install]
WantedBy=multi-user.target
```
Для того чтобы оба юнита приступили к выполнению своих функции необходимо их включить и при это настроить автоматическое включение после загрузки системы, с помощью нижеуказанных команд.
```
[vagrant@localhost ~]$ sudo systemctl enable findword.service
[vagrant@localhost ~]$ sudo systemctl enable findword.timer
[vagrant@localhost ~]$ sudo systemctl start findword.service
[vagrant@localhost ~]$ sudo systemctl start findword.timer
```
Далее для того чтобы убедиться, что наши юниты функционируют корректно, достаточно всего лишь посмотреть статус службы findword.service с помощью утилиты systemctl
```
[vagrant@localhost ~]$ sudo systemctl status findword
● findword.service - Starting findword service
   Loaded: loaded (/etc/systemd/system/findword.service; static; vendor preset: disabled)
   Active: inactive (dead) since Sun 2020-06-21 14:55:00 UTC; 29s ago
  Process: 2214 ExecStart=/bin/grep $WORD $LOG (code=exited, status=0/SUCCESS)
 Main PID: 2214 (code=exited, status=0/SUCCESS)

Jun 21 14:55:00 localhost.localdomain grep[2214]: 2       systemd         456
Jun 21 14:55:00 localhost.localdomain grep[2214]: 3       systemd         789
Jun 21 14:55:00 localhost.localdomain grep[2214]: 4       systemd         101112
Jun 21 14:55:00 localhost.localdomain grep[2214]: 5       systemd         131415
Jun 21 14:55:00 localhost.localdomain grep[2214]: 6       systemd         161718
Jun 21 14:55:00 localhost.localdomain grep[2214]: 7       systemd         192021
Jun 21 14:55:00 localhost.localdomain grep[2214]: 8       systemd         222324
Jun 21 14:55:00 localhost.localdomain grep[2214]: 9       systemd         252627
Jun 21 14:55:00 localhost.localdomain grep[2214]: 10      systemd         282930
Jun 21 14:55:00 localhost.localdomain systemd[1]: Started Starting findword service.
[vagrant@localhost ~]$ sudo systemctl status findword
● findword.service - Starting findword service
   Loaded: loaded (/etc/systemd/system/findword.service; static; vendor preset: disabled)
   Active: inactive (dead) since Sun 2020-06-21 14:55:30 UTC; 795ms ago
  Process: 2223 ExecStart=/bin/grep $WORD $LOG (code=exited, status=0/SUCCESS)
 Main PID: 2223 (code=exited, status=0/SUCCESS)

Jun 21 14:55:30 localhost.localdomain grep[2223]: 2       systemd         456
Jun 21 14:55:30 localhost.localdomain grep[2223]: 3       systemd         789
Jun 21 14:55:30 localhost.localdomain grep[2223]: 4       systemd         101112
Jun 21 14:55:30 localhost.localdomain grep[2223]: 5       systemd         131415
Jun 21 14:55:30 localhost.localdomain grep[2223]: 6       systemd         161718
Jun 21 14:55:30 localhost.localdomain grep[2223]: 7       systemd         192021
Jun 21 14:55:30 localhost.localdomain grep[2223]: 8       systemd         222324
Jun 21 14:55:30 localhost.localdomain grep[2223]: 9       systemd         252627
Jun 21 14:55:30 localhost.localdomain grep[2223]: 10      systemd         282930
Jun 21 14:55:30 localhost.localdomain systemd[1]: Started Starting findword service.
```
Как видим по таймингу запуска службы(29s ago при первом просмотре и 795ms ago при втором просмотре) команда, указанная в findword.service проверяет /var/log/findword.log на наличие указанного в /etc/sysconfig/findword ключевого слова systemd, и благодаря созданному нами findword.timer юниту поиск ключевого слова осуществляется раз в 30 секунд.

## 2) Из репозитория epel установить spawn-fcgi и переписать init-скрипт на unit-файл (имя service должно называться так же: spawn-fcgi)
