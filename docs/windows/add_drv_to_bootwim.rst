Добавление драйверов в boot.wim
===============================

Сначала необходимо открыть *cmd* (командную строку Windows) от имени *Администратора*.

Получение информации о содержимом образа ``boot.wim``:

.. code-block:: powershell

   C:\Users\Administrator> dism /get-imageinfo /imagefile:C:\boot.wim

   Deployment Image Servicing and Management tool
   Version: 6.3.9600.16384

   Details for image : C:\boot.wim

   Index : 1
   Name : Microsoft Windows PE (x64)
   Description : Microsoft Windows PE (x64)
   Size : 1,321,549,982 bytes

   Index : 2
   Name : Microsoft Windows Setup
   Description : Microsoft Windows Setup
   Size : 1,417,514,940 bytes

   The operation completed successfully.

Смонтировать образ в директорию ``C:\mnt``:

.. code-block:: powershell

   C:\Users\Administrator> dism /mount-image /imagefile:C:\boot.wim /index:1 /mountdir:C:\mnt

   Deployment Image Servicing and Management tool
   Version: 6.3.9600.16384

   Mounting image
   [==========================100.0%==========================]
   The operation completed successfully.

Добавление в распакованный образ драйверов из директории ``C:\drivers``:

.. code-block:: powershell

   dism /image:C:\mnt /add-driver /driver:C:\drivers /recurse /forceunsigned

   Deployment Image Servicing and Management tool
   Version: 6.3.9600.16384

   Image Version: 6.3.9600.16384

   Searching for driver packages to install...
   Found 2 driver package(s) to install.
   Installing 1 of 2 - C:\drivers\3wManage.inf: The driver package was successfully installed.
   Installing 2 of 2 - C:\drivers\oemsetup.inf: The driver package was successfully installed.
   The operation completed successfully.

* ``/forceunsigned`` - добавление неподписанных драйверов
* ``/recurse`` - рекурсивное добавление всех драйверов в директории

Если же необходимо добавить конкретный драйвер, то нужно указать точно ``inf-файл``.
Пример:

.. code-block:: powershell

   dism /image:C:\mnt /add-driver /driver:C:\drivers\oemsetup.inf /forceunsigned

Проверка списка добавленных драйверов:

.. code-block:: powershell

   C:\Users\Administrator> dism /image:C:\mnt /get-drivers

   Deployment Image Servicing and Management tool
   Version: 6.3.9600.16384

   Image Version: 6.3.9600.16384

   Obtaining list of 3rd party drivers from the driver store...

   Driver packages listing:

   Published Name : oem0.inf
   Original File Name : 3wmanage.inf
   Inbox : No
   Class Name : System
   Provider Name : AMCC
   Date : 3/24/2009
   Version : 3.0.4.70

   Published Name : oem1.inf
   Original File Name : oemsetup.inf
   Inbox : No
   Class Name : SCSIAdapter
   Provider Name : AMCC
   Date : 3/24/2009
   Version : 3.0.4.70

   The operation completed successfully.

Сохранение и размонтирование образа:

.. code-block:: powershell

   C:\Users\Administrator> dism /unmount-image /mountdir:C:\mnt /commit

   Deployment Image Servicing and Management tool
   Version: 6.3.9600.16384

   Saving image
   [==========================100.0%==========================]
   Unmounting image
   [==========================100.0%==========================]
   The operation completed successfully.

Остаётся только заменить загрузочный образ на WDS на созданный.

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
