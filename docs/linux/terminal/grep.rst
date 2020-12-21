Использование *grep*
====================

Поиск текста в файлах
---------------------

В первом примере мы будем искать пользователя User в файле паролей Linux. Чтобы выполнить поиск текста grep в файле /etc/passwd введите следующую команду:

.. code-block:: console

   $ grep User /etc/passwd

В результате вы получите что-то вроде этого, если, конечно, существует такой пользователь:

.. code-block:: console

   User:x:1000:1000:User,,,:/home/User:/bin/bash

А теперь не будем учитывать регистр во время поиска. Тогда комбинации ABC, abc и Abc с точки зрения программы будут одинаковы:

.. code-block:: console

   $ grep -i "user" /etc/passwd

Вывести несколько строк
-----------------------

Например, мы хотим выбрать все ошибки из лог-файла, но знаем, что в следующей строчке после ошибки может содержаться полезная информация, тогда с помощью grep отобразим несколько строк. Ошибки будем искать в Xorg.log по шаблону "EE":

.. code-block:: console

   $ grep -A4 "EE" /var/log/xorg.0.log

Выведет строку с вхождением и 4 строчки после неё:

.. code-block:: console

   $ grep -B4 "EE" /var/log/xorg.0.log

Выведет целевую строку и 4 строчки до неё:

.. code-block:: console

   $ grep -C2 "EE" /var/log/xorg.0.log

Выведет по две строки с верху и снизу от вхождения.

Регулярные выражения в *grep*
-----------------------------

Регулярные выражения grep - очень мощный инструмент в разы расширяющий возможности поиска текста в файлах. Для активации этого режима используйте опцию -e. Рассмотрим несколько примеров:

Поиск вхождения в начале строки с помощью спецсимвола "^", например, выведем все сообщения за ноябрь:

.. code-block:: console

   $ grep "^Nov 10" messages.1

   Nov 10 01:12:55 gs123 ntpd[2241]: time reset +0.177479 s
   Nov 10 01:17:17 gs123 ntpd[2241]: synchronized to LOCAL(0), stratum 10

Поиск в конце строки - спецсимвол "$":

.. code-block:: console

   $ grep "terminating.$" messages

   Jul 12 17:01:09 cloneme kernel: Kernel log daemon terminating.
   Oct 28 06:29:54 cloneme kernel: Kernel log daemon terminating.

Найдём все строки, которые содержат цифры:

.. code-block:: console

   $ grep "[0-9]" /var/log/Xorg.0.log

Рекурсивный поиск фрагмента в файлах.
-------------------------------------

.. code-block:: console

   $ grep -r "mydomain.com" /etc/apache2/

Кавычки позволяют искать полное соотвествие с заданным шаблоном.
Пример:

.. code-block:: console

   $ grep -r "zendsite" /etc/apache2/
   /etc/apache2/vhosts.d/zendsite_vhost.conf: ServerName zendsite.localhost
   /etc/apache2/vhosts.d/zendsite_vhost.conf: DocumentRoot /var/www/localhost/htdocs/zendsite
   /etc/apache2/vhosts.d/zendsite_vhost.conf: <Directory /var/www/localhost/htdocs/zendsite>

Здесь перед найденной строкой указано имя файла, в котором она была найдена.
Добавление параметра -n добавляет  к выводу номер строки в файле, где найден заданный шаблон поиска.
Вывод имени файла легко отключить с помощью опции -h:

.. code-block:: console

   $ grep -h -r "zendsite" /etc/apache2/

   ServerName zendsite.localhost
   DocumentRoot /var/www/localhost/htdocs/zendsite
   <Directory /var/www/localhost/htdocs/zendsite>

Это будет искать только те файлы, у которых есть.c или.h расширения:

.. code-block:: console

   $ grep --include=\*.{c,h} -rnw '/path/to/somewhere/' -e "pattern"

Это исключает поиск всех файлов, заканчивающихся расширением.o:

.. code-block:: console

   $ grep --exclude=*.o -rnw '/path/to/somewhere/' -e "pattern"

Для каталогов можно исключить конкретный каталог через параметр --exclude-dir. Например, это исключает dir1/, dir2/dir2 и все из них, соответствующие *.dst/:

.. code-block:: console

   $ grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/somewhere/' -e "pattern"

Поиск слов в *grep*
-------------------

Когда вы ищете строку abc, grep будет выводить также kbabc, abc123, aafrabc32 и тому подобные комбинации. Вы можете заставить утилиту искать по содержимому файлов в Linux только те строки, которые выключают искомые слова с помощью опции -w:

.. code-block:: console

   $ grep -w "abc" имя_файла

Поиск двух слов
---------------

Можно искать по содержимому файла не одно слово, а два сразу:

.. code-block:: console

   $ egrep -w 'word1|word2' /path/to/file

Количество вхождений строки
---------------------------

Утилита grep может сообщить, сколько раз определённая строка была найдена в каждом файле. Для этого используется опция -c (счетчик):

.. code-block:: console

   $ grep -c 'word' /path/to/file

Инвертированный поиск в grep
----------------------------

Команда grep Linux может быть использована для поиска строк в файле, которые не содержат указанное слово. Например, вывести только те строки, которые не содержат слово пар:

.. code-block:: console

   $ grep -v пар /path/to/file

Вывод имени файла
-----------------

Вы можете указать grep выводить только имя файла, в котором было найдено заданное слово с помощью опции -l. Например, следующая команда выведет все имена файлов, при поиске по содержимому которых было обнаружено вхождение primary:

.. code-block:: console

   $ grep -l 'primary' *.c

Цветной вывод в *grep*
----------------------

Также вы можете заставить программу выделять другим цветом вхождения в выводе:

.. code-block:: console

   $ grep --color root /etc/passwd
