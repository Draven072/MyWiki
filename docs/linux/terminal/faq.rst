FAQ
===

Как узнать дату установки Ubuntu?
---------------------------------
Для этого нужно в терминал ввести команду:

.. code-block:: console

   ls -lct /etc | tail -1 | awk '{print $6,$7,$8}'

Пример результата выполнения:

.. code-block:: console

   Feb 28 16:35

Как распаковать и собрать заново образ загрузки initrd (initramfs)?
-------------------------------------------------------------------
Создать временную директорию, в которую распакуем initrd:

.. code-block:: console

   mkdir /tmp/initramfscd /tmp/initramfs

Распаковать:

.. code-block:: console

   gunzip -c -9 /boot/initrd.img-2.6.18-1-686 | cpio -i -d -H newc --no-absolute-filenames

Запаковать образ:

.. code-block:: console

   cd /tmp/initramfsfind . | cpio -o -H newc | gzip -9 > /boot/initrd.img-2.6.18-1-686

Как посмотреть версию SATA?
---------------------------
существует уже целых три версии интерфейса SATA и каждая версия имеет свое ограничение по скорости:

* SATA revision 1.0 – 1.5 Гбит/с – 150 Мбайт/с
* SATA revision 2.0 – 3 Гбит/с – 300 Мбайт/с
* SATA revision 3.0 – 6 Гбит/с – 600 Мбайт/с

Как же узнать предельную скорость, которую поддерживает ваша материнская плата? Пользователи Windows могут вызвать платного консультанта, который вскроет корпус, заглянет вовнутрь и прочитает соответствующие надписи на микросхемах. Пользователи Linux могут узнать версию SATA, написав в терминале всего одну строчку:

.. code-block:: console

   sudo hdparm -I /dev/sda | grep -E "Model | speed"

В результате на экране появится нечто подобное:

.. code-block:: console

   Model Number:       ST1000LM035-1RK172
       *	Gen1 signaling speed (1.5Gb/s)
       *	Gen2 signaling speed (3.0Gb/s)
       *	Gen3 signaling speed (6.0Gb/s)


.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
