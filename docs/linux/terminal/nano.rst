Подсветка синтаксиса в *nano*
=============================

Настройки подсветки сиснтаксиса в консольном текстовом редакторе *nano* по-умолчанию подключаются в конфигурационном файле ``/etc/nanorc``

.. code-block:: bash

   $ cat /etc/nanorc
   ...
   ## To include all existing syntax definitions, you can do:
   include "/usr/share/nano/*.nanorc"

Из этой записи видно, что конфиги подсветки синтаксиса нужно искать по пути ``/usr/share/nano/``

.. code-block:: bash

   $ ls -l /usr/share/nano
   -rw-r--r-- 1 root root  882 дек 18 01:19 asm.nanorc
   -rw-r--r-- 1 root root  555 дек 18 01:19 autoconf.nanorc
   -rw-r--r-- 1 root root 1350 дек 18 01:19 awk.nanorc
   -rw-r--r-- 1 root root  709 дек 18 01:19 changelog.nanorc
   -rw-r--r-- 1 root root  825 дек 18 01:19 cmake.nanorc
   ...

Но если открыть *nano* от юзера, то подсветка синтаксиса не работает.

Включение подсветки синтаксиса для юзера.
-----------------------------------------

Чтобы её включить необходимо создать файл ``.nanorc``

.. code-block:: bash

   $ touch ~/.nanorc

И добавить в него инклюды на файлы подсветки синтаксиса.

.. code-block:: bash

   $ find /usr/share/nano/ -iname "*.nanorc" -exec echo include {} \; >> ~/.nanorc

Как можно заметить, в списке отсутсвуют конфиги для синтаксиса *conf* и *yaml* файлов.

Добавление подсветки синтаксиса для *conf* файлов.
--------------------------------------------------

Открываем новый файла

.. code-block:: bash

   $ sudo nano /usr/share/nano/conf.nanorc

И добавляем в него следующее содержимое

.. code-block:: bash

   # config file highlighting

   syntax "conf" "(\.(conf|config|cfg|cnf|rc|lst|list|defs|ini|desktop|mime|types|preset|cache|seat|service|htaccess)$|(^|/)(\w*crontab|mirrorlist|group|hosts|passwd|rpc|netconfig|shadow|fstab|inittab|inputrc|protocols|sudoers)$|conf.d/|.config/)"

   # default text
   color magenta "^.*$"
   # special values
   icolor brightblue "(^|\s|=)(default|true|false|on|off|yes|no)(\s|$)"
   # keys
   icolor cyan "^\s*(set\s+)?[A-Z0-9_\/\.\%\@+-]+\s*([:]|\>)"
   # commands
   color blue "^\s*set\s+\<"
   # punctuation
   color blue "[.]"
   # numbers
   color red "(^|\s|[[/:|<>(){}=,]|\])[-+]?[0-9](\.?[0-9])*%?($|\>)"
   # keys
   icolor cyan "^\s*(\$if )?([A-Z0-9_\/\.\%\@+-]|\s)+="
   # punctuation
   color blue "/"
   color brightwhite "(\]|[()<>[{},;:=])"
   color brightwhite "(^|\[|\{|\:)\s*-(\s|$)"
   # section headings
   icolor brightyellow "^\s*(\[([A-Z0-9_\.-]|\s)+\])+\s*$"
   color brightcyan "^\s*((Sub)?Section\s*(=|\>)|End(Sub)?Section\s*$)"
   color brightcyan "^\s*\$(end)?if(\s|$)"
   # URLs
   icolor green "\b(([A-Z]+://|www[.])[A-Z0-9/:#?&$=_\.\-]+)(\b|$| )"
   # XML-like tags
   icolor brightcyan "</?\w+((\s*\w+\s*=)?\s*("[^"]*"|'[^']*'|!?[A-Z0-9_:/]))*(\s*/)?>"
   # strings
   color yellow "\"(\\.|[^"])*\"" "'(\\.|[^'])*'"
   # comments
   color white "#.*$"
   color blue "^\s*##.*$"
   color white "^;.*$"
   color white start="<!--" end="-->"

После сохранения файла его необходимо добавить в ``.nanorc``

.. code-block:: bash

   $ echo include "/usr/share/nano/conf.nanorc" >> ~/.nanorc

Добавление подсветки синтаксиса для *yaml* файлов.
--------------------------------------------------

Открываем новый файла

.. code-block:: bash

   $ sudo nano /usr/share/nano/yaml.nanorc

И добавляем в него следующее содержимое

.. code-block:: bash

   # Supports `YAML` files
   syntax "YAML" "\.ya?ml$"
   header "^(---|===)" "%YAML"

   ## Keys
   color magenta "^\s*[\$A-Za-z0-9_-]+\:"
   color brightmagenta "^\s*@[\$A-Za-z0-9_-]+\:"

   ## Values
   color white ":\s.+$"
   ## Booleans
   icolor brightcyan " (y|yes|n|no|true|false|on|off)$"
   ## Numbers
   color brightred " [[:digit:]]+(\.[[:digit:]]+)?"
   ## Arrays
   color red "\[" "\]" ":\s+[|>]" "^\s*- "
   ## Reserved
   color green "(^| )!!(binary|bool|float|int|map|null|omap|seq|set|str) "

   ## Comments
   color brightwhite "#.*$"

   ## Errors
   color ,red ":\w.+$"
   color ,red ":'.+$"
   color ,red ":".+$"
   color ,red "\s+$"

   ## Non closed quote
   color ,red "['\"][^['\"]]*$"

   ## Closed quotes
   color yellow "['\"].*['\"]"

   ## Equal sign
   color brightgreen ":( |$)"

После сохранения файла его необходимо добавить в ``.nanorc``

.. code-block:: bash

   $ echo include "/usr/share/nano/yaml.nanorc" >> ~/.nanorc
