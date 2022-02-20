# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

1. На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

    * поместите его в автозагрузку,
    
    * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
    * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
 
Ответ:
Скачаем архив node_exporter, разархивируем его и скопируем node_exporter в каталог  /usr/local/bin/.
Создадим Unit-file /etc/systemd/system/node_exporter.service:

[Unit]

Description=Node Exporter

[Service]

ExecStart=/usr/local/bin/node_exporter $EXTRA_OPTS

EnvironmentFile=/etc/default/node_exporter

Restart=on-failure

[Install]

WantedBy=multi-user.target

Systemd будет подгружать переменные окружения при старте node_exporter из файла /etc/default/node_exporter, а параметры запуска искать в переменной EXTRA_OPTS.

Поместим в автозагрузку:
vagrant@vagrant:~$ systemctl enable node_exporter.service

Сервис после перезагрузки стартует автоматически, его можно остановить и запустить командами.

 

2. Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

Ответ:
CPU:
node_cpu_seconds_total{cpu="0",mode="idle"} 1786.44
node_cpu_seconds_total{cpu="0",mode="iowait"} 1.9
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 0
node_cpu_seconds_total{cpu="0",mode="softirq"} 1.55
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 20.36
node_cpu_seconds_total{cpu="0",mode="user"} 20.49
node_cpu_seconds_total{cpu="1",mode="idle"} 1786.18
node_cpu_seconds_total{cpu="1",mode="iowait"} 1.83
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 0
node_cpu_seconds_total{cpu="1",mode="softirq"} 1.96
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 21.94
node_cpu_seconds_total{cpu="1",mode="user"} 17.95

Memory:
node_memory_Active_anon_bytes 6.6142208e+07
node_memory_Active_bytes 2.28990976e+08
node_memory_Active_file_bytes 1.62848768e+08
node_memory_Buffers_bytes 4.9205248e+07
node_memory_Cached_bytes 5.28838656e+08

Disk:
node_disk_io_now{device="dm-0"} 0
node_disk_io_now{device="sda"} 0
node_disk_io_time_seconds_total{device="dm-0"} 25.948
node_disk_io_time_seconds_total{device="sda"} 26.168
node_disk_writes_completed_total{device="dm-0"} 2895
node_disk_writes_completed_total{device="sda"} 2408

Net:
node_network_transmit_packets_total{device="eth0"} 2254
node_network_transmit_packets_total{device="lo"} 310
node_network_up{device="eth0"} 1
node_network_up{device="lo"} 0
node_network_transmit_errs_total{device="eth0"} 0
node_network_transmit_errs_total{device="lo"} 0

3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:

    ```bash
    config.vm.network "forwarded_port", guest: 19999, host: 19999
    ```

    После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.

Ответ:

![](../../Education/Netology/DEVOPS/Netdata.png)
Все работает.

4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?

Ответ:
Да, знает.
Узнать тип системы можно с помощью утилиты dmesg.
dmesg используется для проверки кольцевого буфера ядра или управления им.
Чтобы проверить, является ли ваша система Linux физической или виртуальной, просто запустите:
$ sudo dmesg | grep "Hypervisor detected"
Если ваша система физическая, вы не увидите никаких выходных данных.
Если ваша система – виртуальная машина, вы увидите результат, подобный приведенному ниже.
[ 0.000000] Hypervisor detected: KVM

vagrant@vagrant:~$ dmesg
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.000000] Hypervisor detected: KVM



5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?

Ответ:
Это обозначает максимальное количество дескрипторов файлов, которые может выделить процесс. Значение по умолчанию равно 1024*1024 (1048576), чего должно быть достаточно для большинства машин. Фактический лимит зависит от лимита ресурсов RLIMIT_NOFILE.
Утилита ulimit возвращает два вида ограничений - hard и soft. Ограничение soft вы можете менять в любую сторону, пока оно не превышает hard. Ограничение hard можно менять только в меньшую сторону от имени обычного пользователя. От имени суперпользователя можно менять оба вида ограничений так, как нужно. По умолчанию отображаются soft-ограничения:

vagrant@vagrant:~$ ulimit -n
1024
Чтобы вывести hard, используйте опцию -H:
ulimit -nH
vagrant@vagrant:~$ ulimit -Hn
1048576

Но поскольку hard-ограничение составляет 1048576, то установить лимит больше этого значения мы не можем. Чтобы изменить настройки ограничений для пользователя на постоянной основе, нужно настроить файл /etc/security/limits.conf


6. Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.

Ответ:

vagrant@vagrant:$ sudo -i

root@vagrant:# unshare -f --pid --mount-proc sleep 10

^Z

[1]+  Stopped                 unshare -f --pid --mount-proc sleep 10m


root@vagrant:# ps -aux | grep sleep

root        1846  0.0  0.0   5480   580 pts/1    T    15:15   0:00 unshare -f --pid --mount-proc sleep 10m

root        1847  0.0  0.0   5476   592 pts/1    S    15:15   0:00 sleep 10m

root        1855  0.0  0.0   6432   660 pts/1    S+   15:15   0:00 grep --color=auto sleep


root@vagrant:# nsenter -t 1847 -p -m

root@vagrant:/# ps -aux

USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND

root           1  0.0  0.0   5476   592 pts/1    S    15:15   0:00 sleep 10m

root           2  0.1  0.3   7236  3988 pts/1    S    15:17   0:00 -bash

root          13  0.0  0.3   9084  3644 pts/1    R+   15:17   0:00 ps -aux



7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?

Ответ:

:(){ :|:& };: - Логическая бомба (известная также как fork bomb), забивающая память системы, что в итоге приводит к её зависанию.
Чтобы лучше понять, как она действует, давайте её немного преобразуем:

fu() {
  fu | fu &
}
fu
Она оперирует определением функции с именем ‘:‘, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет.

dmesg сообщает:
 
[ 4059.244572] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-5.scope

vagrant@vagrant:$ systemctl status user-1000.slice

● user-1000.slice - User Slice of UID 1000

     Loaded: loaded
     
    Drop-In: /usr/lib/systemd/system/user-.slice.d
    
             └─10-defaults.conf
             
     Active: active since Thu 2022-02-17 14:20:15 UTC; 1h 58min ago
     
       Docs: man:user@.service(5)
       
      Tasks: 13 (limit: 2356)
      

vagrant@vagrant:$ systemctl show --property DefaultTasksMax

DefaultTasksMax=1071

vagrant@vagrant:$ cat /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.max

2356

cgroups – это механизм ядра, позволяющий ограничивать использование, вести учет и изолировать потребление системных ресурсов (ЦП, память, дисковый ввод/вывод, сеть и т. п.) на уровне коллекций процессов
Три cgroups, которые по умолчанию есть в системе – System, User и Machine. Каждая из этих групп называется «слайс» (slice – сектор). Каждый слайс может иметь дочерние секторы-слайсы. Каждый из трех слайсов верхнего уровня предназначен для своего типа рабочих нагрузок, которым нарезаются дочерние сектора в рамках родительского слайса:

System – демоны и сервисы.
User – пользовательские сеансы. Каждый пользователь получает свой дочерний слайс, причем все сеансы с одинаковым UID «живут» в одном и том же слайсе, чтобы особо ушлые умники не могли получить ресурсов больше положенного.
Machine – виртуальные машины, типа KVM-гостей.

Контроллер номера процесса используется для того, чтобы позволить иерархии cgroup останавливать выполнение любых новых задач fork()’d или clone()’d после достижения определенного предела.

Поскольку тривиально достичь предела задачи, не нарушая никаких установленных ограничений kmemcg, PIDS являются фундаментальным ресурсом. Таким образом, исчерпание PID должно быть предотвращено в рамках иерархии cgroup, позволяя ограничивать количество задач в cgroup ресурсами.

Изменить количество процессов можно так:

vagrant@vagrant:$ cat /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.max

2356

vagrant@vagrant:/$ echo 2355 | sudo tee /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.max

2355

vagrant@vagrant:/$ cat /sys/fs/cgroup/pids/user.slice/user-1000.slice/pids.max

2355
