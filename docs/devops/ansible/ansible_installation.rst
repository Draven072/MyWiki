Установка *Ansible*
===================

*Debian*
--------


*Ubuntu*
--------

Чтобы установить последнюю версию **Ansible** для *Ubuntu*, нужно добавить PPA репозиторий в систему. Перед тем, как это сделать, сначала нужно обновить индекс пакетов и установить пакет *software-properties-common*.

.. code-block:: console

   sudo apt update
   sudo apt install software-properties-common

Затем добавить PPA репозиторий **Ansible**:

.. code-block:: console

   sudo apt-add-repository ppa:ansible/ansible

Нажать ``ENTER`` чтобы добавить PPA и затем снова обновить индекс пакетов.

.. code-block:: console

   sudo apt update

После окончания обновления можно установить **Ansible**:

.. code-block:: console

   sudo apt install ansible

*CentOS*
--------


.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
