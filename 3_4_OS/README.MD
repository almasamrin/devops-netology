# 1.
```
sudo vi /etc/default/MyTestService.conf
# Settings for the MyTestService
# MyTestVars= :  any extra command-line startup arguments for crond
MyTestVars="TestTestTest"
EXTRA_OPTS="-f"
```

```
sudo vi /etc/systemd/system/MyTestService.service
[Unit]
Description=example systemd service unit file.

[Service]
EnvironmentFile=-/etc/default/MyTestService.conf
ExecStart=/bin/bash /usr/sbin/example.sh $EXTRA_OPTS
ExecStop=/bin/bash /usr/sbin/exampleStop.sh

[Install]
WantedBy=multi-user.target
```

```
sudo vi /usr/sbin/example.sh
#!/bin/bash
echo "the script works" >> /tmp/diditwork.txt
$MyTestVars >> /tmp/diditwork.txt
```

```
sudo vi /usr/sbin/exampleStop.sh
#!/bin/bash
echo "the stop script works" >> /tmp/diditwork.txt
```

```
sudo chmod 555 /usr/sbin/example.sh
sudo chmod u+w /usr/sbin/example.sh
sudo chmod 555 /usr/sbin/exampleStop.sh
sudo chmod u+w /usr/sbin/exampleStop.sh
ls -lah /usr/sbin/example.sh
-rwxr-xr-x 1 root root 58 Jan 27 16:17 /usr/sbin/example.sh
ls -lah /usr/sbin/exampleStop.sh
-rwxr-xr-x 1 root root 63 Jan 31 14:41 /usr/sbin/exampleStop.sh
systemctl daemon-reload
systemctl enable MyTestService.service
reboot
```

```
cat /tmp/diditwork.txt
the script works9
TestTestTest
the stop script works
```

# 2.
Для базового мониторинга выбираем: cpu, meminfo, qdisc, (netdev, netstat, udp_queues)

# 3. 
Ознакомился. Для 1 сервера очень удобно, не когда их несколько не очень удобно. Хотя возможно есть решение для нескольких серверов.

# 4. 
Нашел через команду:
```
dmesg -k | grep VM
[159231.791521] Hardware name: VMware, Inc. VMware Virtual Platform/440BX Desktop Reference Platform, BIOS 6.00 12/12/2018
```
Получается может в сообщениях ядра (kernel) может проскакивать информация о инструменте виртуализации.

# 5.
```
sysctl -n fs.nr_open
1048576
```
Не дает достичь максимума настройка:
```
ulimit -a
open files                      (-n) 1024
```

# 6.
Из screen:
```
sleep 5m
```

Из другого терминала:
```
ps aux | grep sleep
root     120386  0.0  0.0 108056   356 pts/2    S+   15:54   0:00 sleep 5m
nsenter --target 120386 --pid --mount

ps aux
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.0 116208  2828 pts/2    S    15:49   0:00 /bin/bash
root         51  0.0  0.0 108056   356 pts/2    S+   15:54   0:00 sleep 5m
root         57  1.3  0.0 116204  2808 pts/3    S    15:59   0:00 -bash
root         76  0.0  0.0 155452  1872 pts/3    R+   15:59   0:00 ps aux
```

# 7.
Получается это процесс, который бесконечно порождает себе подобные процессы. Спасли ситуацию лимиты на количество запуска процессов.
Доступно объяснили зачем нужны эти лимиты :)
