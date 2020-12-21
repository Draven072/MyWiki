Установка *VirtualBox* в *Debian 9 (Stretch)*.
==============================================

**Virtualbox** - система виртуализации, на данный момент одна из наилучших  бесплатных программных продуктов виртуализации. Но в *Debian 9 (Stretch)* он по-умолчанию отсутствует в стандартных репозиториях.

Сайт проекта: https://www.virtualbox.org/

Установка из *Oracle Virtualbox third-party repository*.
--------------------------------------------------------

Установку можно произвести из *Oracle Virtualbox third-party repository*.

Для начала необходимо подключить репозиторий.

Создадим файл ``/etc/apt/source.list.d/virtualbox.list``:

.. code-block:: console

  $ sudo touch /etc/apt/source.list.d/virtualbox.list

Открываем файл в текстовом редакторе:

.. code-block:: console

   $ sudo nano /etc/apt/source.list.d/virtualbox.list

и добавляем строку:

.. code-block:: bash
   :linenos:

   deb http://download.virtualbox.org/virtualbox/debian stretch contrib

добавляем в систему *Oracle VirtualBox public key*

.. code-block:: console

   $ wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
   $ wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -

обновляем кэш *apt*

.. code-block:: console

   $ sudo apt update

Если в процессе обновления не выдало никаких сообщений об ошибках или предупреждений, то можно проверить наличие доступных пакетов *virtualbox*

.. code-block:: console

   $ sudo apt-cache search virtualbox
   virtualbox-5.0 - Oracle VM VirtualBox
   virtualbox-5.1 - Oracle VM VirtualBox
   virtualbox-5.2 - Oracle VM VirtualBox
   virtualbox-6.0 - Oracle VM VirtualBox

Теперь можно установить любую из доступных версий *virtualbox*

.. code-block:: console

  $ sudo apt install virtualbox-6.0

Установка из deb-пакета
-----------------------

Можно так же просто скачать пакет *virtualbox* с сайта и установить его вручную.

Для начала заходим на страницу загрузки https://www.virtualbox.org/wiki/Linux_Downloads и копируем ссылку на нужную версию. В нашем случае для *Debian 9*.

Загружаем пакет:

.. code-block:: console

   $ wget https://download.virtualbox.org/virtualbox/6.0.2/virtualbox-6.0_6.0.2-128162~Debian~stretch_amd64.deb

Устанавливаем пакет:

.. code-block:: console

   $ sudo dpkg -i virtualbox-6.0_6.0.2-128162~Debian~stretch_amd64.deb

На этом всё.

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
