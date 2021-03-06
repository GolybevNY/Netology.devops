# Домашнее задание к занятию "3.1. Работа в терминале, лекция 1"

1. Установите средство виртуализации [Oracle VirtualBox](https://www.virtualbox.org/).
Установлено!
1. Установите средство автоматизации [Hashicorp Vagrant](https://www.vagrantup.com/).
Установлено!
1. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал. Можно предложить:
Выполнено!

1. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:

Выполнено!

	* Выполнение в этой директории `vagrant up` установит провайдер VirtualBox для Vagrant, скачает необходимый образ и запустит виртуальную машину.
Выполнено!
	* `vagrant suspend` выключит виртуальную машину с сохранением ее состояния (т.е., при следующем `vagrant up` будут запущены все процессы внутри, которые работали на момент вызова suspend), `vagrant halt` выключит виртуальную машину штатным образом.
Выполнено!
1. Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?
Выполнено!
Ресурсы по умолчанию:
RAM:1024mb
CPU:2 cpu
HDD:64gb
Video:4mb

1. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: [документация](https://www.vagrantup.com/docs/providers/virtualbox/configuration.html). Как добавить оперативной памяти или ресурсов процессора виртуальной машине?

Добавлением комманд в VagrantFile:
   config.vm.provider "virtualbox" do |vb|
     vb.memory = "2048"
     vb.cpu = "3"
   end
  

1. Команда `vagrant ssh` из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.
Выполнено!

1. Ознакомиться с разделами `man bash`, почитать о настройках самого bash:
    * какой переменной можно задать длину журнала `history`, и на какой строчке manual это описывается?
    * Ответ:
    * `Histfilesize` - The maximum number of lines contained in the history file. at line 736

    * 
    * что делает директива `ignoreboth` в bash?
    * Ответ:
    * Директива`ignoreboth` - это сокращение для директив `ignorespace` и `ignoredups`.
	`ignorespace`- команды, начинающиеся с пробелов не будут сохранены.
  	`ignoredups`- не сохраняет команды, которые уже есть в истории.


1. В каких сценариях использования применимы скобки `{}` и на какой строчке `man bash` это описано?
2. Ответ:
3. ЗАРЕЗЕРВИРОВАННЫЕ СЛОВА
Зарезервированные слова - это слова, которые имеют особое значение для оболочки. Следующие слова распознаются как зарезервированные, если они не заключены в кавычки и являются первым словом простой команды (см. ГРАММАТИКУ ОБОЛОЧКИ ниже) или третьим словом в регистре или для команды:

! в случае, если сопроцедура выполнена, если еще есть ошибка для функции, если в select, то до тех пор, пока { } время [[ ]] at line 160

1. Основываясь на предыдущем вопросе, как создать однократным вызовом `touch` 100000 файлов? 
2. Решение:
3. vagrant@vagrant:~/$touch {1..100000}
4. А получилось ли создать 300000?
5. Ответ: Нет
6. Если нет, то почему?
7. Ответ: превышено максимальное количество аргументов, которые возможно передать команде.

1. В man bash поищите по `/\[\[`. Что делает конструкция `[[ -d /tmp ]]`
2. Ответ:
3. Проверяет наличие каталога /tmp и возвращает 1 или 0 в при наличии или отсутствии каталога соответственно.
4. 
1. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:

	```bash
	bash is /tmp/new_path_directory/bash
	bash is /usr/local/bin/bash
	bash is /bin/bash
	```

	(прочие строки могут отличаться содержимым и порядком)
    В качестве ответа приведите команды, которые позволили вам добиться указанного вывода или соответствующие скриншоты.

Решение:
vagrant@vagrant:~$ mkdir /tmp/new_path_dir/
vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_dir/
vagrant@vagrant:~$ type -a bash
bash is /usr/bin/bash
bash is /bin/bash
vagrant@vagrant:~$ PATH=/tmp/new_path_dir/:$PATH
vagrant@vagrant:~$ type -a bash
bash is /tmp/new_path_dir/bash
bash is /usr/bin/bash
bash is /bin/bash

1. Чем отличается планирование команд с помощью `batch` и `at`?
Ответ:
at - выполняет команды в указанное время
batch - выполняет команды, когда позволяют уровни загрузки системы,другими словами, когда средняя загрузка падает ниже 1,5.

1. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.
2. Решено:
3. > vagrant suspend
















