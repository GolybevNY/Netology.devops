# **ДОМАШНЕЕ ЗАДАНИЕ К ЗАНЯТИЮ "3.2. РАБОТА В ТЕРМИНАЛЕ, ЛЕКЦИЯ 2"** #

1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.
Ответ:
cd - это встроенная команда shell, она изменяет текущий рабочий каталог оболочки.
«Текущий рабочий каталог» - это свойство, уникальное для каждого процесса.

Итак, если cd была программой, она работала бы следующим образом:
cd /tmp
запускается процесс cd
процесс cd изменяет каталог для процесса cd на /tmp
завершается процесс cd
При этом для shell текущий рабочий каталог не изменится.

2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.

Ответ:
Альтернатива без pipe:
    grep -c <some_string> <some_file>


3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?
Ответ:
    vagrant@vagrant:~$ pstree -p
    systemd(1)─┬─VBoxService(1020)─┬─{VBoxService}(1026)
          


4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?

Ответ:
Терминал pts/0:
vagrant@vagrant:~/test$ tty
/dev/pts/0
vagrant@vagrant:~/test$ ls ~/test1 2> /dev/pts/1

Терминал pts/1:
    vagrant@vagrant:~$ tty
    /dev/pts/1
    vagrant@vagrant:~$ ls: cannot access '/home/vagrant/test1': No such file or directory



5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.

Ответ:
    vagrant@vagrant:~/test$ ls
    grep.txt
    vagrant@vagrant:~/test$ echo ~/test > lsin.txt
    vagrant@vagrant:~/test$ ls -A <lsin.txt > lsout.txt
    vagrant@vagrant:~/test$ cat lsout.txt
    grep.txt
    lsin.txt
    lsout.txt
    vagrant@vagrant:~/test$



6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?

Ответ:
Да получится. Проверено через ssh, так как в графическом режиме терминал запускается также в качестве псевдоэмулятора терминала pts.
Терминал pts/0 (ssh):
    vagrant@vagrant:~/test$ tty
    /dev/pts/0
    vagrant@vagrant:~/test$ echo hello from pts/0 > /dev/tty2

Терминал tty2:
    vagrant@vagrant:~$ tty
    /dev/tty2
    hello from pts/0
    


7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?

Ответ:
vagrant@vagrant:~/test$ bash 5>&1 # Создает дескриптор fd5 и перенаправляет его в stdout (fd1).
vagrant@vagrant:~/test$ echo netology > /proc/$$/fd/5 # Перенаправляет stdout (fd1) echo в fd5, который направлен в fd1 (stdout).
netology



8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.

Ответ:
    vagrant@vagrant:~/test$ ls -lAh ~/test
    total 12K
    -rw-rw-r-- 1 vagrant vagrant 28 Feb  7 11:56 grep.txt
    -rw-rw-r-- 1 vagrant vagrant 19 Feb  7 16:49 lsin.txt
    -rw-rw-r-- 1 vagrant vagrant 28 Feb  7 16:50 lsout.txt
    -rw-rw-r-- 1 vagrant vagrant  0 Feb  7 17:50 test1.txt
    -rw-rw-r-- 1 vagrant vagrant  0 Feb  7 17:50 test2.txt
    -rw-rw-r-- 1 vagrant vagrant  0 Feb  7 17:50 test3.txt
    vagrant@vagrant:~/test$ ls -lAh ~/test1
    ls: cannot access '/home/vagrant/test1': No such file or directory
    vagrant@vagrant:~/test$ ls -lAh ~/test 5>&2 2>&1 1>&5 | grep  -c test
    total 12K
    -rw-rw-r-- 1 vagrant vagrant 28 Feb  7 11:56 grep.txt
    -rw-rw-r-- 1 vagrant vagrant 19 Feb  7 16:49 lsin.txt
    -rw-rw-r-- 1 vagrant vagrant 28 Feb  7 16:50 lsout.txt
    -rw-rw-r-- 1 vagrant vagrant  0 Feb  7 17:50 test1.txt
    -rw-rw-r-- 1 vagrant vagrant  0 Feb  7 17:50 test2.txt
    -rw-rw-r-- 1 vagrant vagrant  0 Feb  7 17:50 test3.txt
    0
    vagrant@vagrant:~/test$ ls -lAh ~/test1 5>&2 2>&1 1>&5 | grep  -c test
    1


9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?

Ответ:
Команда выведет переменные окружения текущего процесса оболочки bash. Этот файл содержит начальную среду, которая была установлена при запуске текущей программы.
vagrant@vagrant:~/test$ cat /proc/$$/environ | tr '\000' '\n' # Выведет переменные окружения текущего процесса bash построчно.
vagrant@vagrant:~/test$ env # Аналогично выводит переменные окружения текущего процесса bash построчно.



10. Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe.

Ответ:
/proc/<PID>/cmdline - Этот файл, доступный только для чтения, содержит полную командную строку для процесса,
если только процесс не является зомби.
proc/<PID>/exe - В Linux 2.2 и более поздних версиях этот файл представляет собой символическую ссылку,
содержащую фактический путь к выполняемой команде.
    vagrant@vagrant:~$ readlink /proc/$$/exe
    /usr/bin/bash



11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.

Ответ:
sse4_2
    vagrant@vagrant:~/test$ grep sse /proc/cpuinfo



12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:

vagrant@netology1:~$ ssh localhost 'tty'
not a tty
Почитайте, почему так происходит, и как изменить поведение.

Ответ:
Так как команды в данном случае выполняется не в интерактивном режиме, то псевдотерминал не выдается.
    vagrant@vagrant:~$ ssh localhost 'tty'
    vagrant@localhost's password:
    not a tty
Можно принудительно выделять псевдотерминал, указав ключ "-t"
    vagrant@vagrant:~$ ssh -t localhost 'tty'
    vagrant@localhost's password:
    /dev/pts/3
    Connection to localhost closed.
    


13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.

Ответ:
Для начала установим reptyr:

    vagrant@vagrant:~$ sudo apt-get install reptyr

Затем при попытке переместить процесс в другой pty возникает ошибка:
    vagrant@vagrant:~$ reptyr 1501
    Unable to attach to pid 1501: Operation not permitted
    The kernel denied permission while attaching. If your uid matches
    the target's, check the value of /proc/sys/kernel/yama/ptrace_scope.
    For more information, see /etc/sysctl.d/10-ptrace.conf

Из руководства на reptyr:
> reptyr depends on the ptrace(2) system call to attach to the  remote  program.  On  Ubuntu
>    Maverick  and  higher,  this  ability is disabled by default for security reasons. You can
>    enable it temporarily by doing
> 
>                # echo 0 /proc/sys/kernel/yama/ptrace_scope
> 
>    as root, or permanently by  editing  the  file  /etc/sysctl.d/10-ptrace.conf,  which  also
>    contains more information about this setting.

Выполним:
    vagrant@vagrant:~$ echo 0|sudo tee /proc/sys/kernel/yama/ptrace_scope

Терминал /dev/pts/0 (ssh):
    vagrant@vagrant:~$ tty
    /dev/pts/0
    vagrant@vagrant:~$ ssh localhost 'tty' &
    [1] 1433
    ssh localhost 'tty'
    
    [1]+  Stopped ssh localhost 'tty'
    vagrant@vagrant:~$ ps -aux | grep 1433
    vagrant 1433  0.2  0.6  12008  6368 pts/0T16:52   0:00 ssh localhost tty
    vagrant 1437  0.0  0.0   6300   740 pts/0S+   16:52   0:00 grep --color=auto 1433

Терминал /dev/pts/1 (ssh):
    vagrant@vagrant:~$ ps -aux | grep 1433
    vagrant 1433  0.0  0.6  12008  6368 pts/0T16:52   0:00 ssh localhost tty
    vagrant 1467  0.0  0.0   6300   664 pts/1S+   16:55   0:00 grep --color=auto 1433
    vagrant@vagrant:~$ man ps
    vagrant@vagrant:~$ reptyr 1433
    vagrant@localhost's password:

Терминал /dev/pts/0 (ssh):
    vagrant@vagrant:~$ ps -aux | grep 1433
    vagrant 1433  0.0  0.6  12008  6836 pts/2Ss+  16:52   0:00 ssh localhost tty
    vagrant 1498  2.2  0.2   2592  2036 pts/1S+   16:59   0:00 reptyr 1433
    vagrant 1501  0.0  0.0   6300   728 pts/0S+   16:59   0:00 grep --color=auto 1433

14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.
Команда tee делает вывод одновременно и в файл, указанный в качестве параметра, и в stdout. 
Так как команда запущена от sudo, то имеет права на запись в файл, получая на вход стандартный вывод команды echo.
