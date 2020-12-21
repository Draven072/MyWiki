Интеграция non-free драйверов в *initrd.gz*
===========================================

1. Создать резервную копию ``initrd.gz``

.. code-block:: console

   $ [ -f initrd.gz.orig ] || \
     cp -p initrd.gz initrd.gz.orig

2. Скачать архив со свежимы *deb*-пакетами драйверов

.. code-block:: console

   $ [ -f firmware.cpio.gz ] || \
     wget http://cdimage.debian.org/cdimage/unofficial/non-free/firmware/stable/current/firmware.cpio.gz

3. Добавить драйвера в загрузочный образ ``initrd.gz``

.. code-block:: console

   $ cat initrd.gz.orig firmware.cpio.gz > initrd.gz
