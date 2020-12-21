*Vagrant*: Основные команды.
============================

Посмотреть версию установленного *vagrant*:

.. code-block:: console

   $ vagrant --version
   Vagrant 2.2.3

или:

.. code-block:: console

   $ vagrant -v
   Vagrant 2.2.3

Так же можно посмотреть установленную и доступную для обновления версию:

.. code-block:: console

   $ vagrant version
   Installed Version: 2.2.3
   Latest Version: 2.2.3

   You're running an up-to-date version of Vagrant!

Инициализация *vagrant* в текущей директории:

.. code-block:: console

   $ vagrant init
   A `Vagrantfile` has been placed in this directory. You are now ready to
   `vagrant up` your first virtual environment! Please read the comments in
   the Vagrantfile as well as documentation on `vagrantup.com` for more
   information on using Vagrant.

Эта команда создаёт в текущей директории конфигурационный файл ``Vagrantfile`` с дэфолтными настройками.

Перед первым запуском необходимо открыть этот файл и заменить значение переменной в строке ``config.vm.box = "base"`` с *base* на версию образа, который вы желаете запустить. К примеру:

.. code-block:: bash
   :linenos:

   config.vm.box = "debian/stretch64"

После сохранения запускаем бокс:

.. code-block:: console

   $ vagrant up
   Bringing machine 'default' up with 'virtualbox' provider...
   ==> default: Importing base box 'debian/stretch64'...
   Progress: 90%
   ...
   VirtualBox Guest Additions: Starting.
   Unmounting Virtualbox Guest Additions ISO from: /mnt
   ==> default: Checking for guest additions in VM...
   ==> default: Installing rsync to the VM...
   ==> default: Rsyncing folder: /mnt/data/vagrant/test/ => /vagrant

   ==> default: Machine 'default' has a post `vagrant up` message. This is a message
   ==> default: from the creator of the Vagrantfile, and not from Vagrant itself:
   ==> default:
   ==> default: Vanilla Debian box. See https://app.vagrantup.com/debian for help and bug reports

Эта программ скачивает темплейт образа, если он ещё не был скачан вами ранее, создаёт виртуальный бокс и запускает его.

Информация об уже загруженных темплейтах можно посмотреть так:

.. code-block:: console

   $ vagrant box list
   centos/7         (virtualbox, 1811.02)
   debian/stretch64 (virtualbox, 9.6.0)
   ubuntu/bionic64  (virtualbox, 20181219.0.0)



vagrant up
vagrant ssh
vagrant destroy
vagrant halt
vagrant suspend
vagrant resume
vagrant reload

vagrant status

.. code-block:: console

   $ vagrant halt
   ==> default: Attempting graceful shutdown of VM...

Установка гостевых дополнений

.. code-block:: console

   $ vagrant vbguest
   [default] GuestAdditions 6.0.2 running --- OK.

Получить состояние бокса

.. code-block:: console

   $ vagrant status
   Current machine states:

   default                   running (virtualbox)

   The VM is running. To stop this VM, you can run `vagrant halt` to shut it
   down forcefully, or you can run `vagrant suspend` to simply suspend the
   virtual machine. In either case, to restart it again, simply run `vagrant
   up`.

Получение информации обо всех боксах созданных *Vagrant*.

.. code-block:: console

   $ vagrant global-status
   id       name    provider   state    directory
   -------------------------------------------------------------------------
   0398ff9  omv5box virtualbox poweroff /mnt/mdima/vagrant/OpenMediaVault
   b0b5f1f  default virtualbox running  /mnt/data/vagrant/debian_vb_test

   The above shows information about all known Vagrant environments on this
   machine. This data is cached and may not be completely up-to-date (use
   "vagrant global-status --prune" to prune invalid entries). To interact with
   any of the machines, you can go to that directory and run Vagrant, or you
   can use the ID directly with Vagrant commands from any directory. For
   example: "vagrant destroy 1a2b3c4d"

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
         :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
         :alt: Documentation Status
