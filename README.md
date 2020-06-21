h1					ДЗ №7 Systemd
1) Написать service, который будет раз в 30 секунд мониторить лог на предмет наличия ключевого слова (файл лога и ключевое слово должны задаваться в /etc/sysconfig)
Создадим файл конфигурации для нашего сервиса. Для ключевого слова и пути к лог файлу назначим переменные ЛОГ и ВОРД. 
[vagrant@localhost ~]$ cat /etc/sysconfig/findword 
# Configuration file for my findword service
WORD="systemd"
LOG=/var/log/findword.log
Далее создадим лог файл с ключевым словом. 
[vagrant@localhost ~]$ cat /var/log/findword.log 
1       systemd         123
2       systemd         456
3       systemd         789
4       systemd         101112
5       systemd         131415
6       systemd         161718
7       systemd         192021
8       systemd         222324
9       systemd         252627
10      systemd         282930

После чего создаем два юнита и для нашенго сервиса 
[Unit]
Description=Starting findword service

[Service]
Type=oneshot
EnvironmentFile=/etc/sysconfig/findword
ExecStart=/bin/grep $WORD $LOG

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

[vagrant@localhost ~]$ sudo systemctl 


sudo systemctl enable findword.timer
sudo systemctl start findword.service
sudo systemctl start findword.timer
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
