Использование Vault – зашифрованного хранилища
==============================================

Начиная с версии 1.5 в **Ansible** была доабвлена возможность хранения секретных данных, таких как пароли или RSA ключи, в зашифрованных файлах – *vault* (“хранилище”).

Для работы с такими хранилищами используется утилита ansible-vault_, которая принимает два основных аргумента – ``--ask-vault-pass`` или ``--vault-password-file``, которые могут быть заданы через ``ansible.cfg``.

.. _ansible-vault: https://docs.ansible.com/ansible/2.4/ansible-vault.html#ansible-vault

Располагается в ``/usr/bin/ansible-vault`` и представялет собой *Python*-скрипт.

Шифрование файлов
-----------------

Для создания хранилища – используется ``create``:

.. code-block:: console

   $ ansible-vault create file.yml
   New Vault password:
   Confirm New Vault password:

После чего открывается редактор по умолчанию, что бы в файл можно было добавить данные.

Результат:

.. code-block:: console

   $ cat file.yml
   $ ANSIBLE_VAULT;1.1;AES256
   32333262313562656230663934383566373966633138346137313937306230373633333730343839
   6330346166626235376530333662663961636365393631340a623165306231383636643066306665
   39303633623361643065636338326336333366656363313762316336623732626631643437336663
   6466336232316131390a316464383837623361333964383235616365316263336565336136663063
   6635

Что бы зашифровать уже имеющий файл – используется ``encrypt``:

.. code-block:: console

   $ echo "data" > raw_file.txt
   $ ansible-vault encrypt raw_file.txt
   New Vault password:
   Confirm New Vault password:
   Encryption successful
   $ cat raw_file.txt
   $ ANSIBLE_VAULT;1.1;AES256
   65303737323263313938656134323635313039363131626331643835393034323335343432383438
   3232643162313534643231613661623264656365663437650a353661376235346134313362653066
   64393066333937373334353361623735366334666463346633613463373532646535636566663831
   3434623235653933650a313038343833323236623061633065323266336237623461383930646662
   6538

Расшифровка файлов
------------------

Что бы зашифрованный файл снова вернуть в plaintext – используем ``decrypt``:

.. code-block:: console

   $ ansible-vault decrypt file.yml
   Vault password:
   Decryption successful
   $ cat file.yml
   test data

Редактирование файлов
---------------------

Используется ``ansible-vault edit``:

.. code-block:: console

   $ ansible-vault edit file.yml
   Vault password:

Файл расшифровывается во временный файл, открывается для редактирования, после завершения – снова шифруется и сохраняется в старом месте.

Просмотр файла
--------------

Что бы просто просмотреть содержимое хранилища – используем ``view``.

Шифруем файл опять:

.. code-block:: console

   $ ansible-vault encrypt file.yml
   New Vault password:
   Confirm New Vault password:
   Encryption successful

И проверяем содержимое:

.. code-block:: console

   $ ansible-vault view file.yml
   Vault password:
   test data

Смена пароля
------------

Для смены пароля используется ``rekey``, который можно применять к нескольким файлам сразу, если они зашифрованы одним ключём (паролем).

Создадим новый:

.. code-block:: console

   $ ansible-vault create file2.yml
   New Vault password:
   Confirm New Vault password:

И меняем пароль для обоих файлов:

.. code-block:: console

   $ ansible-vault rekey file.yml file2.yml
   Vault password:
   New Vault password:
   Confirm New Vault password:
   Rekey successful

Примеры
-------

Пример использования зашифрованного файла
+++++++++++++++++++++++++++++++++++++++++

Создаём простой плейбук ``helloworld.yml``:

.. code-block:: yaml

   ---
   - hosts: all
   tasks:
     - debug:
         msg: "hello world"

Проверяем:

.. code-block:: console

   $ ansible-playbook -i "localhost," -c local helloworld.yml
   PLAY [all] ****
   TASK [Gathering Facts] ****
   ok: [localhost]
   TASK [debug] ****
   ok: [localhost] => {
   "msg": "hello world"
   }
   PLAY RECAP ****
   localhost                  : ok=2    changed=0    unreachable=0    failed=0

ОК, теперь добавим создание файла с текстом из зашифрованного файла в Ansible.

Создаём файл ``vars/exmple_text.yml`` со строкой:

.. code-block:: yaml

   data_text: |
     some text data
     another text data

Создаём файл с паролем:

.. code-block:: console

   $ echo “MySuperPass” > ~/.ansible_pass.txt

Шифруем файл ``vars/exmple_text.yml``:

.. code-block:: console

   $ ansible-vault encrypt vars/exmple_text.yml --vault-password-file ~/.ansible_pass.txt
   Encryption successful

Проверяем его:

.. code-block:: console

   $ head -n 2 vars/exmple_text.yml
   $ ANSIBLE_VAULT;1.1;AES256
   33303034383034313365383135323837613963646235393533643361343061306336373236663837

Обновляем наш ``helloworld.yml``, добавляем создание файла, который будет содержать текст :

.. code-block:: yaml

   ---
   - hosts: all
     tasks:
       - debug:
           msg: "hello world"
       - name: add var
         include_vars:
           file: exmple_text.yml
       - name: Add descrypted text from exmple_text.yml
         copy:
           content="{{ data_text }}"
           dest=/tmp/example_text_out.txt

Запускаем, передавая ``--vault-password-file``:

.. code-block:: console

   $ ansible-playbook -i "localhost," -c local helloworld.yml --vault-password-file ~/.ansible_pass.txt
   PLAY [all] ****
   TASK [Gathering Facts] ****
   ok: [localhost]
   TASK [debug] ****
   ok: [localhost] => {
   "msg": "hello world"
   }
   TASK [add var] ****
   ok: [localhost]
   TASK [Add descrypted text from exmple_text.yml] ****
   changed: [localhost]
   PLAY RECAP ****
   localhost                  : ok=4    changed=1    unreachable=0    failed=0

Проверяем:

.. code-block:: console

   $ cat /tmp/example_text_out.txt
   some text data
   another text data

Всё на месте.

Пример шифрования переменной
++++++++++++++++++++++++++++

Кроме файла – можно зашифровать строку, например:

.. code-block:: console

   $ ansible-vault encrypt_string
   New Vault password:
   Confirm New Vault password:
   Reading plaintext input from stdin. (ctrl-d to end input)
   This is Text String
   !vault |
   $ ANSIBLE_VAULT;1.1;AES256
   38363965343563353962666264646337613464663263663632626264373563633430323633356639
   3737333233393662336533376661333163653035333334370a373565343330633537363563656430
   37363632636664353864353532633030326231356238643634623033396539656164666437343565
   3964653033343136620a356438316635663561313665323739353766383233656261646538616165
   66313237633635646265653633323635333861636539313937343363666539366465
   Encryption successful

Либо выполнить одной командой:

.. code-block:: console

   $ ansible-vault encrypt_string "This is Text String" --name "encrypted_data_string" --vault-password-file ~/.ansible_pass.txt
   encrypted_data_string: !vault |
   $ ANSIBLE_VAULT;1.1;AES256
   36653162656434373362396464353061343337343866323436366138636266666165636163623337
   3339343833383064303036346661616235396563356362630a363430313566323537363964663636
   32303931333561633632323265346136323139616536343466663736666138333638306133653935
   3066643730653831300a663735333639343064666636336139336536353734343963313032373338
   35643639643136623163346662623763633763366462396433323133306532663038
   Encryption successful

Далее эта строка может использоваться в переменной.

Добавим новый файл ``vars/strings.yml`` с переменной ``encrypted_data_string``:

.. code-block:: yaml

  ---
  encrypted_data_string: !vault |
              $ANSIBLE_VAULT;1.1;AES256
              36653162656434373362396464353061343337343866323436366138636266666165636163623337
              3339343833383064303036346661616235396563356362630a363430313566323537363964663636
              32303931333561633632323265346136323139616536343466663736666138333638306133653935
              3066643730653831300a663735333639343064666636336139336536353734343963313032373338
              35643639643136623163346662623763633763366462396433323133306532663038

Обновим плейбук:

.. code-block:: yaml

   ---
   - hosts: all
       tasks:
         - debug:
             msg: "hello world"
         - name: add vars
           include_vars:
             file: exmple_text.yml
         - name: add another vars
           include_vars:
             file: strings.yml
         - name: Add decrypted text from exmple_text.yml
           copy:
             content="{{ data_text }}"
             dest=/tmp/example_text_out.txt
         - name: Add another decrypted text from strings.yml
           copy:
             content="{{ encrypted_data_string }}"
             dest=/tmp/another_example_text_out.txt

Запускаем выполнение:

.. code-block:: console

   $ ansible-playbook -i "localhost," -c local helloworld.yml --vault-password-file ~/.ansible_pass.txt
   PLAY [all] ****
   TASK [Gathering Facts] ****
   ok: [localhost]
   TASK [debug] ****
   ok: [localhost] => {
   "msg": "hello world"
   }
   TASK [add vars] ****
   ok: [localhost]
   TASK [add another vars] ****
   ok: [localhost]
   TASK [Add decrypted text from exmple_text.yml] ****
   ok: [localhost]
   TASK [Add another decrypted text from strings.yml] ****
   changed: [localhost]
   PLAY RECAP ****
   localhost                  : ok=6    changed=1    unreachable=0    failed=0

Проверяем:

.. code-block:: console

   $ cat /tmp/another_example_text_out.txt
   This is Text String

Или используя ``--ask-vault-pass``:

.. code-block:: console

   $ ansible-playbook -i “localhost,” -c local helloworld.yml --ask-vault-pass
   Vault password:
   …

vault-id
++++++++

В Ansible 2.4 и выше вместо ``--ask-vault-pass`` и ``--vault-password-file`` можно использовать ``--vault-id``, который позволяет использовать разные пароли для разных файлов.

Создадим новые файлы вместо ``exmple_text.yml`` и ``strings.yml``, зашифруем их разными паролями.

Файлы паролей:

.. code-block:: console

   $ echo "pass1" > pass1.txt
   $ echo "pass2" > pass2.txt

Создаём файл ``vars/data_text1.yml`` с переменной *data_text1*:

.. code-block:: console

   $ echo “data_text1: Data Text One” > vars/data_text1.yml

Шифруем его паролем из файла ``pass1.txt``:

.. code-block:: console

   $ ansible-vault --vault-id pass1.txt encrypt vars/data_text1.yml
   Encryption successful


Аналогично – второй файл:

.. code-block:: console

   $ echo “data_text2: Data Text Two” > vars/data_text2.yml

И тоже шифруем его, вторым паролем:

.. code-block:: console

   $ ansible-vault --vault-id pass2.txt encrypt vars/data_text2.yml
   Encryption successful

Обновим плейбук:

.. code-block:: yaml

   ---
   - hosts: all
     tasks:
       - debug:
           msg: "hello world"
       - name: add data_text1.yml
         include_vars:
           file: data_text1.yml
       - name: add data_text2.yml
         include_vars:
           file: data_text2.yml
       - name: Add decrypted data_text1
         copy:
           content="{{ data_text1 }}"
           dest=/tmp/data_text1_out.txt
       - name: Add decrypted data_text2
         copy:
           content="{{ data_text2 }}"
           dest=/tmp/data_text2_out.txt

И запускаем его:

.. code-block:: console

   $ ansible-playbook -i "localhost," -c local --vault-id pass1.txt --vault-id pass2.txt helloworld.yml
   PLAY [all] ****
   TASK [Gathering Facts] ****
   ok: [localhost]
   TASK [debug] ****
   ok: [localhost] => {
   "msg": "hello world"
   }
   TASK [add data_text1.yml] ****
   ok: [localhost]
   TASK [add data_text2.yml] ****
   ok: [localhost]
   TASK [Add decrypted data_text1] ****
   changed: [localhost]
   TASK [Add decrypted data_text2] ****
   changed: [localhost]
   PLAY RECAP ****
   localhost                  : ok=6    changed=2    unreachable=0    failed=0

Проверяем:

.. code-block:: console

   $ cat /tmp/data_text*
   Data Text OneData Text Two

Так же ``--vault-id`` можно использовать вместе с ``--vault-password-file``:

.. code-block:: console

   $ rm /tmp/data_text*
   $ ansible-playbook -i "localhost," -c local --vault-id pass1.txt --vault-password-file pass2.txt helloworld.yml
   ...
   localhost                  : ok=6    changed=2    unreachable=0    failed=0

Либо использовать ``--ask-vault-pass`` вместо указания файла:

.. code-block:: console

   $ rm /tmp/data_text*
   $ ansible-playbook -i "localhost," -c local --vault-id pass1.txt --ask-vault-pass helloworld.yml
   Vault password:
   ...
   localhost                  : ok=6    changed=2    unreachable=0    failed=0

Сам ``--vault-password-file`` тоже можно использовать несколько раз:

.. code-block:: console

   $ ansible-playbook -i "localhost," -c local --vault-password-file pass1.txt --vault-password-file pass2.txt helloworld.yml

Ansible попробует каждый из переданных паролей, пока один из них не сработает для зашированного файла.


.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
