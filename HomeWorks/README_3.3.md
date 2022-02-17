1. Какой системный вызов делает команда cd? В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, это shell builtin, поэтому запустить strace непосредственно на cd не получится. Тем не менее, вы можете запустить strace на /bin/bash -c 'cd /tmp'. В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте. Вам нужно найти тот единственный, который относится именно к cd. Обратите внимание, что strace выдаёт результат своей работы в поток stderr, а не в stdout.

Ответ:
vagrant@vagrant:~$ strace /bin/bash -c 'cd /tmp' 2>&1 | grep tmp
execve("/bin/bash", ["/bin/bash", "-c", "cd /tmp"], 0x7ffcfa748180 /* 23 vars */) = 0
stat("/tmp", {st_mode=S_IFDIR|S_ISVTX|0777, st_size=4096, ...}) = 0
chdir("/tmp")                           = 0

Команда cd делает системный вызов chdir ()

2. Попробуйте использовать команду file на объекты разных типов на файловой системе. Например:
vagrant@netology1:~$ file /dev/tty
/dev/tty: character special (5/0)
vagrant@netology1:~$ file /dev/sda
/dev/sda: block special (8/0)
vagrant@netology1:~$ file /bin/bash
/bin/bash: ELF 64-bit LSB shared object, x86-64
Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.

Ответ:
Согласно документации file использует базу данных с именем magic.mgc. Таким образом выполним:

vagrant@vagrant:~$ strace file /dev/tty 2>&1 | grep magic.mgc
stat("/home/vagrant/.magic.mgc", 0x7fffdbafdd10) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3

База данных file находится в /usr/share/misc/magic.mgc.

3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).

Ответ:
Убедимся что текущее свободное место корня файловой системы не меняется во времени:
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3855188  26857644  13% /
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3855188  26857644  13% /

Запустим в фон программу dd,записывающую в в файл log поток псевдослучайных данных:
vagrant@vagrant:~/test$ dd if=/dev/urandom of=./log bs=10 &
[1] 372023

Уьедимся что log создан и размер его увеличивается:
vagrant@vagrant:~/test$ ll
total 13340
drwxrwxr-x 3 vagrant vagrant 2895872 Feb 10 15:57 ./
drwxr-xr-x 5 vagrant vagrant 2129920 Feb 10 15:53 ../
-rw-rw-r-- 1 vagrant vagrant 8622370 Feb 10 15:57 log
drwxrwxr-x 2 vagrant vagrant    4096 Feb 10 15:55 old/
vagrant@vagrant:~/test$ ll
total 20764
drwxrwxr-x 3 vagrant vagrant  2895872 Feb 10 15:57 ./
drwxr-xr-x 5 vagrant vagrant  2129920 Feb 10 15:53 ../
-rw-rw-r-- 1 vagrant vagrant 16227160 Feb 10 15:57 log
drwxrwxr-x 2 vagrant vagrant     4096 Feb 10 15:55 old/

Убедимся что текущее свободное место корня файловой системы стало уменьшаться:
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3880044  26832788  13% /

Удалим файл log:
vagrant@vagrant:~/test$ rm log
vagrant@vagrant:~/test$ ll
total 4916
drwxrwxr-x 3 vagrant vagrant 2895872 Feb 10 15:57 ./
drwxr-xr-x 5 vagrant vagrant 2129920 Feb 10 15:53 ../
drwxrwxr-x 2 vagrant vagrant    4096 Feb 10 15:55 old/

Убедимся что текущее свободное место корня файловой системы продолжает уменьшаться:
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3902880  26809952  13% /
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3913572  26799260  13% /
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3919156  26793676  13% /

Определим файловый дескриптор нашего процесса:
vagrant@vagrant:~/test$ lsof -p 372023
COMMAND    PID    USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
dd      372023 vagrant  cwd    DIR  253,0  2895872 1048606 /home/vagrant/test
dd      372023 vagrant  rtd    DIR  253,0     4096       2 /
dd      372023 vagrant  txt    REG  253,0    80256 1835599 /usr/bin/dd
dd      372023 vagrant  mem    REG  253,0  3035952 1835290 /usr/lib/locale/locale-archive
dd      372023 vagrant  mem    REG  253,0  2029224 1841468 /usr/lib/x86_64-linux-gnu/libc-2.31.so
dd      372023 vagrant  mem    REG  253,0   191472 1841428 /usr/lib/x86_64-linux-gnu/ld-2.31.so
dd      372023 vagrant    0r   CHR    1,9      0t0      11 /dev/urandom
dd      372023 vagrant    1w   REG  253,0 83553500 1048609 /home/vagrant/test/log (deleted)
dd      372023 vagrant    2u   CHR  136,0      0t0       3 /dev/pts/0
dd      372023 vagrant    3r  FIFO   0,13      0t0   34117 pipe

Уменьшим размер псевдофайла стандартного вывода (stdout) процесса с помощью программы truncate: 
vagrant@vagrant:~/test$ truncate -s 0 /proc/372023/fd/1

Убедимся что текущее свободное место корня файловой системы увеличилось:
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3857580  26855252  13% /

Убедимся что текущее свободное место корня файловой системы продолжает уменьшаться, так как данные продожают поступать на stdout:
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3866296  26846536  13% /

Также можно уменьшить размер псевдофайла стандартного вывода (stdout) процесса с помощью перенаправления в него пустоты: 
vagrant@vagrant:~/test$ > /proc/372023/fd/1

Убедимся что текущее свободное место корня файловой системы увеличилось:
vagrant@vagrant:~/test$ df -T | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv ext4      32380720  3857620  26855212  13% /

4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?

Ответ:
Процесс при завершении (как нормальном, так и в результате не обрабатываемого сигнала) освобождает все свои ресурсы и становится «зомби» — пустой записью в таблице процессов, хранящей статус завершения, предназначенный для чтения родительским процессом.

Зомби-процесс существует до тех пор, пока родительский процесс не прочитает его статус с помощью системного вызова wait(), в результате чего запись в таблице процессов будет освобождена.
Зомби-процессы не занимают памяти, но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом.

При достижении лимита записей все процессы пользователя, от имени которого выполняется создающий зомби родительский процесс, не будут способны создавать новые дочерние процессы.

5. В iovisor BCC есть утилита opensnoop:
root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке.

Ответ:
vagrant@vagrant:~/test$ sudo /usr/sbin/opensnoop-bpfcc
PID    COMM               FD ERR PATH
632    snapd              10   0 /var/lib/snapd/assertions/asserts-v0/model/16/generic/generic-classic
632    snapd              11   0 /var/lib/snapd/assertions/asserts-v0/model/16/generic/generic-classic/active
632    snapd              10   0 /var/lib/snapd/assertions/asserts-v0/serial/generic/generic-classic/e58abe3f-98f3-41b7-9d4c-90b167b6f4e6
632    snapd              11   0 /var/lib/snapd/assertions/asserts-v0/serial/generic/generic-classic/e58abe3f-98f3-41b7-9d4c-90b167b6f4e6/active
632    snapd              10   0 /var/lib/snapd/assertions/asserts-v0/model/16/generic/generic-classic
632    snapd              11   0 /var/lib/snapd/assertions/asserts-v0/model/16/generic/generic-classic/active
632    snapd              10   0 /var/lib/snapd/assertions/asserts-v0/serial/generic/generic-classic/e58abe3f-98f3-41b7-9d4c-90b167b6f4e6
632    snapd              11   0 /var/lib/snapd/assertions/asserts-v0/serial/generic/generic-classic/e58abe3f-98f3-41b7-9d4c-90b167b6f4e6/active
632    snapd              10   0 /var/lib/snapd/assertions/asserts-v0/model/16/generic/generic-classic
632    snapd              11   0 /var/lib/snapd/assertions/asserts-v0/model/16/generic/generic-classic/active
1      systemd            12   0 /proc/632/cgroup
1060   vminfo              4   0 /var/run/utmp
621    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
621    dbus-daemon        19   0 /usr/share/dbus-1/system-services
621    dbus-daemon        -1   2 /lib/dbus-1/system-services
621    dbus-daemon        19   0 /var/lib/snapd/dbus-1/system-services/

6. Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.

Ответ:
vagrant@vagrant:~/test$ strace uname -a 2>&1 | grep "Linux"
uname({sysname="Linux", nodename="vagrant", ...}) = 0
uname({sysname="Linux", nodename="vagrant", ...}) = 0
uname({sysname="Linux", nodename="vagrant", ...}) = 0
write(1, "Linux vagrant 5.4.0-91-generic #"..., 106Linux vagrant 5.4.0-91-generic #102-Ubuntu SMP Fri Nov 5 16:31:28 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

Системный вызов uname()
Цитата из man:
"Part of the utsname information is also accessible via
       /proc/sys/kernel/{ostype, hostname, osrelease, version,
       domainname}."

7. Чем отличается последовательность команд через ; и через && в bash? Например:
root@netology1:~# test -d /tmp/some_dir; echo Hi
Hi
root@netology1:~# test -d /tmp/some_dir && echo Hi
root@netology1:~#

Ответ:
";" - Разделитель последовательных команд. Выполняет команды одну за другой независимо от статуса завершения предыдущей команды.
"&&" - Условный оператор. Выполнит последующую команду если первая завершилась успешно.
vagrant@vagrant:~$ ls
test
vagrant@vagrant:~$ test -d ./test; echo Hi
Hi
vagrant@vagrant:~$ test -d ./test && echo Hi
Hi
vagrant@vagrant:~$ test -d ./test1 && echo Hi
vagrant@vagrant:~$

Есть ли смысл использовать в bash &&, если применить set -e?
Ответ:
   "set -e" -  Exit immediately if a command exits with a non-zero status.
Совместное использование "&&" и "set -e" вероятно не имеет смысла.

8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?

Ответ:
 Набор данных параметров позволит улучшить отладку и повысить безопасность при выполнении скрипта и результата его выполнения.

"-e" -  Указав параметр -e скрипт немедленно завершит работу, если любая команда выйдет с ошибкой. По-умолчанию, игнорируются любые неудачи и сценарий продолжет выполнятся.
"-u" - Благодаря ему оболочка проверяет инициализацию переменных в скрипте. Если переменной не будет, скрипт немедленно завершиться. Данный параметр достаточно умен, чтобы нормально работать с переменной по-умолчанию ${MY_VAR:-$DEFAULT} и условными операторами (if, while, и др).
"-x" - Полезен при отладке. С помощью него bash печатает в стандартный вывод все команды перед их исполнением. Стоит учитывать, что все переменные будут уже доставлены, и с этим нужно быть аккуратнее, к примеру если используете пароли.
"-o pipefail" - Если нужно убедиться, что все команды в пайпах завершились успешно, нужно использовать -o pipefail.


9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).

Ответ:
vagrant@vagrant:~$ ps -ax -o stat | grep -c D
0
vagrant@vagrant:~$ ps -ax -o stat | grep -c I
46
vagrant@vagrant:~$ ps -ax -o stat | grep -c R
2
vagrant@vagrant:~$ ps -ax -o stat | grep -c S
65
vagrant@vagrant:~$ ps -ax -o stat | grep -c T
1
vagrant@vagrant:~$ ps -ax -o stat | grep -c W
0
vagrant@vagrant:~$ ps -ax -o stat | grep -c X
0
vagrant@vagrant:~$ ps -ax -o stat | grep -c Z
0

Самый часто встречающийся статус у процессов "S" - interruptible sleep (waiting for an event to complete). 

Дополнительные характеристики состояния процессов означают:

  		<    high-priority (not nice to other users)
               N    low-priority (nice to other users)
               L    has pages locked into memory (for real-time and custom IO)
               s    is a session leader
               l    is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
               +    is in the foreground process group

