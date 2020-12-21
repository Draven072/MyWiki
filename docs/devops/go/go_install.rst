Установка Go в debian
=====================

Установка.
++++++++++

1. Скачать архив с последней версией для Linux по ссылке_.

.. _ссылке: https://golang.org/dl/

2. Распаковать архив с в ``/usr/local`` созданием директории ``/usr/local/go``. Пример:

.. code-block:: console

   $ sudo tar -C /usr/local -xzf go1.11.5.linux-amd64.tar.gz

3. Добавить путь ``/usr/local/go/bin`` файл профиля ``~/.profile`` или в файл ``/etc/profile``. Необходимо добавить следующую строку:

.. code-block:: bash
   :linenos:

   export PATH=$PATH:/usr/local/go/bin

После этого необходимо перезайти в профиль, либо обновить переменные окружение командой:

.. code-block:: console

   $ source ~/.profile

Проверка правильности установки.
++++++++++++++++++++++++++++++++

Создадим простое приложение *Hello World*.
Создайте рабочую директорию для исходников, к примеру ``$HOME/go`` и в ней директории ``src/hello``:

.. code-block:: console

   $ mkdir $HOME/go/src/hello

Перейдите в эту директорию и создайте файл ``hello.go``:

.. code-block:: console

   $ cd $HOME/go/src/hello
   $ touch hello.go

Откройте этот файл в любом текстовом редакторе и добавьте следующий код:

.. code-block:: go
   :linenos:

   package main

   import "fmt"

   func main() {
      fmt.Printf("hello, world\n")
   }

Следующая команда соберёт исполняемый файл ``hello`` в директории с созданным файлом:

.. code-block:: console

   $ go build hello.go

Проверим создался ли исполняемый файл:

.. code-block:: console

   $ ls -lh
   итого 1,9M
   -rwxr-xr-x 1 mdima mdima 1,9M фев 17 20:23 hello
   -rw-r--r-- 1 mdima mdima   74 фев 17 20:21 hello.go

Запустим приложение. Если всё было установлено корректно и приложение собралось, то увидим следующее:

.. code-block:: console

   $ ./hello
   hello, world

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
      :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
      :alt: Documentation Status
