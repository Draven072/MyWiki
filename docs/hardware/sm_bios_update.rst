.. meta::
   :http-equiv=Content-Type: text/html; charset=UTF-8

Обновление BIOS на серверах Supermicro
======================================

.. note:: Самый простой способ обновить прошивку *BIOS* на серверах *Supermicro*, сделать это через веб-интерфейс IPMI сервера. Но для этого нужно приобрести ключ. Описанный ниже способ расскаждет как обновить *BIOS* не имея ключа.

Скачиваем архив с обновлением *BIOS* для нужной материнской платы с сайта Supermicro_.

.. _Supermicro: https://www.supermicro.com/

Можно воспользоваться поиском, или посмотреть на странице_.

.. _странице: https://www.supermicro.com/support/resources/bios_ipmi.php?vendor=1

Скачиваем образ с сайта `Hiren Boot CD`_.

.. _`Hiren Boot CD`: https://www.hirensbootcd.org/old-versions/

Распаковываем архив с обновлением прошивки в одтельную папку.

Открываем образ *Hiren Boot CD* в любом редакторе iso-образов и добавляем в корень диска папку с файлами для обновления *BIOS*. Сохраняем образ.

Монтируем образ на виртуальный привод *IPMI* и загружаемся *DOS* согласно инструкции_.

.. _инструкции: https://theitguy02.wordpress.com/2016/04/24/hirens-boot-cd-unable-to-create-ram-disk/

Копируем файлы все из директории содержащей новую прошивку *BIOS* в корень RAM-диска *R*. Обычно директория с файлами находится на диске ``С:\``.

К примеру, директория называется *bios*:

.. code-block:: bat

   R:\$> C:
   C:\$> CD bios\
   C:\$> COPY *.* R:\*.*
   C:\$> R:

Запускаем обновление прошивки. Обычно это производится скриптом ``FLASH.BAT``, но более точную информацию по процедуре прошивки можно прочесть в файле ``Readme.txt`` входящего в состав файлов архива обновлений.

Пример обновления:

.. code-block:: bat

   R:\$> FLASH.BAT X11SSE8.516

Ожидаем окончания процесса обновления прошивки.

.. attention:: После обновления прошивки, сервер необходимо выключить по питанию минимум на 10 секунд.

После включения зайти в *BIOS* и восстановить необходимые настройки.

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
