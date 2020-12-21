Установка консольного клиента *pgcli* в Linux
=============================================

**pgcli** - консоль для управления **Postgres**.

Умный инструмент командной строки для **Postgres** с автокомплитом и подсветкой синтаксиса написанный на **Python**.

Быстрая Установка
-----------------

Если у вас уже установлен *Python*, то ,как правило, для установки должно хватить одной команды:

.. code-block:: console

   $ pip install pgcli

Возможно потребуется ``sudo``, либо можно установить только для текущего пользователя с параметром ``--user``.

.. code-block:: console

   $ pip install --user pgcli

Подробности установки при отсутствии утилиты *pip*
--------------------------------------------------

Если же окажется что пакетный менеджер для **Python**, под названием **pip** у вас ещё не установлен ии вы получили ошибку о том, что данная команда не найдена - не беда.

Для начала проверим, действительно ли **pip** не установлен в системе. Для этого надо ввести следующую команду:

.. code-block:: console

   $ which pip

В ответ мы должны получить абсолютный путь, что-то типа:

.. code-block:: console

   /usr/bin/pip

Если же вы получите ошибку, либо команда вообще ничего не выведет, значит **pip** отсутсвует в системе.

Установить его можно простой командой.
Для систем на базе **Debian** (*Debian, Ubuntu, Mint и тд.*)

.. code-block:: console

   $ sudo apt-get install python-pip

Для систем на базе **Redhat** (*Fedora, Centos, RHEL и тд.*)

.. code-block:: console

   $ sudo yum install python-pip

**pgcli** использует *psycopg* для подключения к базе данных *postgres*. Для установки *psycopg*, вам потребуется чтобы в системе были установлены *libpq* и *python-dev*. Поскольку *psycopg* это *C* расширение для *Python*, необходимо чтобы так же был установлен компилятор *C*.

.. code-block:: console

   $ sudo apt-get install libpq-dev python-dev   # debian
   $ sudo yum install postgresql-devel python-devel  # redhat

Теперь, когда установлены все зависимости, можно приступить к установке **pgcli**.

.. code-block:: console

   $ sudo pip install pgcli

.. note:: Возможно при запуске **pgcli** будет ругаться, что пакет *psycopg2* будет переименован в релизе 2.8

Будет выдаваться ссобщение типа:

.. code-block:: console

   $ pgcli
   /home/user/.local/lib/python2.7/site-packages/psycopg2/__init__.py:144:
    UserWarning: The psycopg2 wheel package will be renamed from release 2.8;
    in order to keep installing from binary please use "pip install psycopg2-binary" instead.
    For details see:
    <http://initd.org/psycopg/docs/install.html#binary-install-from-pypi>.

В этом случае необходимо установить бинарники *psycopg2*, как это рекомендуется в сообщении:

.. code-block:: console

   $ pip install psycopg2-binary

На этом всё.

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
