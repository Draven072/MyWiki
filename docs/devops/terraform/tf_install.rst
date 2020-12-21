Установка *terraform* в Debian
==============================

https://www.terraform.io/

Установка *terraform* очень проста. Поскольку он распрастраняется в виде одного единственного исполняемого файла.

Для начала необходимо скачать по ссылке_ архив с последней версией *terraform* под свою операционную систему. В данном случае это будет *Linux 64-bit*.

.. _ссылке: https://www.terraform.io/downloads.html

.. code-block:: console

   $ wget https://releases.hashicorp.com/terraform/0.11.11/terraform_0.11.11_linux_amd64.zip

Распаковываем исполняемый файл в директорию ``/usr/bin/``, либо в директорию ``~/bin/`` текущего пользователя, если таковая существует.

.. code-block:: console

   $ sudo unzip terraform_0.11.11_linux_amd64.zip -d /usr/bin/

По сути, процесс установки на этом закончен. Теперь можно проверить установленную версию:

.. code-block:: console

   $ terraform -v
   Terraform v0.11.11

На этом всё.

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
