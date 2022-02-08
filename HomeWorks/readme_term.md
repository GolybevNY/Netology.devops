какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?
Histfilesize at line 736

что делает директива ignoreboth в bash?

 HISTCONTROL
              A  colon-separated list of values controlling how commands are saved on the history list.  If the list of values includes ignores‐
              pace, lines which begin with a space character are not saved in the history list.  A value of ignoredups causes lines matching the
              previous  history entry to not be saved.  A value of **ignoreboth** is shorthand for ignorespace and ignoredups.  A value of erasedups
              causes all previous lines matching the current line to be removed from the history list before that line is saved.  Any value  not
              in  the above list is ignored.  If HISTCONTROL is unset, or does not include a valid value, all lines read by the shell parser are
              saved on the history list, subject to the value of HISTIGNORE.  The second and subsequent lines of a multi-line  compound  command
              are not tested, and are added to the history regardless of the value of HISTCONTROL.



В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано
RESERVED WORDS
       Reserved  words are words that have a special meaning to the shell.  The following words are recognized as reserved when unquoted and ei‐
       ther the first word of a simple command (see SHELL GRAMMAR below) or the third word of a case or for command:

       ! case  coproc  do done elif else esac fi for function if in select then until while { } time [[ ]] at line 160


С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?
vagrant@vagrant:~/$touch {1..100000}
vagrant@vagrant:~/$ getconf ARG_MAX
2097152

В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]
 [[ expression ]]
              Return  a  status of 0 or 1 depending on the evaluation of the conditional expression expression.  Expressions are composed of the
              primaries described below under CONDITIONAL EXPRESSIONS.  Word splitting and pathname expansion are not performed on the words be‐
              tween  the  [[ and ]]; tilde expansion, parameter and variable expansion, arithmetic expansion, command substitution, process sub‐
              stitution, and quote removal are performed.  Conditional operators such as -f must be unquoted to be recognized as primaries.
 CONDITIONAL EXPRESSIONS
       Conditional  expressions  are  used  by  the  [[ compound command and the test and [ builtin commands to test file attributes and perform
       string and arithmetic comparisons.  The test abd [ commands determine their behavior based on the number of arguments; see  the  descrip‐
       tions of those commands for any other command-specific actions.
-d file
              True if file exists and is a directory
Проверяет наличие каталога /tmp и возвращает 1 или 0 в при наличии или отсутсвии каталога соответсвенно.


Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:

bash is /tmp/new_path_directory/bash
bash is /usr/local/bin/bash
bash is /bin/bash
(прочие строки могут отличаться содержимым и порядком) В качестве ответа приведите команды, которые позволили вам добиться указанного вывода или соответствующие скриншоты.
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

Чем отличается планирование команд с помощью batch и at?
 	at -  выполняет команды в указанное время.

	batch - выполнение команд, когда позволяют уровни загрузки системы; другими словами, когда средняя нагрузка падает ниже 1,5.

Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.
vagrant suspend




