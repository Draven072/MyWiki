.. meta::
   :http-equiv=Content-Type: text/html; charset=UTF-8

Настройка DNS сервера Bind на Ubuntu 16.04
==========================================

Перед началом установки мы рекомендуем вам обновить информацию о пакетах, содержащихся в репозиториях:

.. code-block:: console

   sudo apt-get update

Установка *bind9*
-----------------
`BIND <https://wiki.debian.org/Bind9>`_ *(Berkeley Internet Name Domain)* является реализацией протокола *Domain Name System (DNS)* с открытым исходным кодом *(Open-source)*, и обеспечивает реализацию основных компонентов системы доменных имен.

Вы можете установить *Bind9* используя команду:

.. code-block:: console

   sudo apt-get install bind9 dnsutils

Настройка *Bind* для протокола *IPv4*
-------------------------------------

После установки необходимо отредактировать файл ``named.conf.local``

.. code-block:: console

   sudo nano /etc/bind/named.conf.local

В этот файл добавьте информацию о ваших зонах. В качестве примера будем использовать доменное имя *dnstest.root.lu* и подсеть 94.242.218.160/28 (94.242.218.162 основной *IP сервера*, на котором настраивается *DNS*).

.. code-block:: console

   zone "dnstest.root.lu" {
     type master;
     file "/etc/bind/zones/db.dnstest.root.lu";
   };

   zone "218.242.94.in-addr.arpa" {
     type master;
     file "/etc/bind/zones/db.94";
   };

Нажмите ``Ctrl+X`` чтобы сохранить файл и выйти из редактора.

Теперь необходимо отредактировать файл опций *Bind9*.

.. code-block:: console

   sudo nano /etc/bind/named.conf.options

Укажите ``forwarders`` - адреса вышестоящих *DNS серверов*, куда ваш *DNS сервер* будет перенаправлять запросы которые не сможет обработать самостоятельно.

.. code-block:: console

   forwarders {
      8.8.8.8; # Google Public DNS IPv4 address
      8.8.4.4; # Google Public DNS IPv4 address
   };

Далее, добавляем файл зоны прямого просмотра:

.. code-block:: console

   sudo mkdir /etc/bind/zones
   sudo nano /etc/bind/zones/db.dnstest.root.lu

Вносим в файл информацию о нашей зоне, и адреса/имена хостов, которые будут определяться нашим *DNS сервером*.

.. code-block:: console

   $TTL 604800
   @ IN SOA ns.dnstest.root.lu. admin.dnstest.root.lu. (
      2 ; Serial
      604800 ; Refresh
      86400 ; Retry
      2419200 ; Expire
      604800 ) ; Negative Cache TTL

   ;dnsserver
   @   IN NS ns.dnstest.root.lu.
   @   IN A  94.242.218.162
   ns  IN A  94.242.218.162

   ;clients
   subdomain1 IN A  94.242.218.163
   subdomain2 IN A  94.242.218.164
   subdomain3 IN A  94.242.218.165

Теперь создадим файл обратной зоны просмотра (*reverse DNS zone*).

.. code-block:: console

   sudo nano /etc/bind/zones/db.94

Добавим в файл следующую информацию:

.. code-block:: console


   $TTL 604800
   @ IN SOA ns.dnstest.root.lu. admin.dnstest.root.lu. (
       1 ; Serial
       604800 ; Refresh
       86400 ; Retry
       2419200 ; Expire
       604800 ) ; Negative Cache TTL
   ;
   @   IN NS  ns.dnstest.root.lu.
   162 IN PTR dnstest.root.lu.

   163 IN PTR subdomain1.dnstest.root.lu.
   164 IN PTR subdomain2.dnstest.root.lu.
   165 IN PTR subdomain3.dnstest.root.lu.

Теперь проверим правильность синтаксиса конфигурационных файлов *BIND*.

.. code-block:: console

   named-checkconf

Если конфигурационные файлы не содержат ошибок, вывод этой программы будет пуст.

Далее, необходимо перезапустить сервис *Bind*.

.. code-block:: console

   sudo service bind9 restart

И проверить статус *Bind* на наличие ошибок.

.. code-block:: console

   sudo service bind9 status

.. code-block:: console

   ● bind9.service - BIND Domain Name Server
      Loaded: loaded (/lib/systemd/system/bind9.service; enabled; vendor preset: enabled)
      Drop-In: /run/systemd/generator/bind9.service.d
               └─50-insserv.conf-$named.conf
      Active: active (running) since Sat 2016-04-23 21:00:17 CEST; 2min 52s ago

Если в результате выполнения вы увидите ошибки, ещё раз перепроверьте правильность данных в конфигурационных файлах *Bind* и в директории ``/etc/bind/zones/``

Теперь проверим работу вашего *DNS сервера*. Для начала изменим содержимое файла ``resolv.conf``.

.. code-block:: console

   sudo nano /etc/resolvconf/resolv.conf.d/head

Вставьте в файл следующие строки с данными вашего *DNS сервера* и сохраните файл.

.. code-block::  console

   search dnstest.root.lu
   nameserver 94.242.218.162

Выполните команду ``resolvconf`` чтобы сгенерировать новый файл ``resolv.conf``.

.. code-block:: console

   sudo resolvconf -u

И наконец, протестируем ваш *DNS* при помощи одной из двух приведённых ниже команд.

Резолв зоны ``dnstest.root.lu``:

.. code-block:: console

   dig @94.242.218.162 dnstest.root.lu

.. code-block:: console

   ;; ANSWER SECTION:
   dnstest.root.lu.	604800	IN	A	94.242.218.162

   ;; AUTHORITY SECTION:
   dnstest.root.lu.	604800	IN	NS	ns.dnstest.root.lu.

   ;; ADDITIONAL SECTION:
   ns.dnstest.root.lu.	604800	IN	A	94.242.218.162

Обратный резольв IPv4:

.. code-block:: console

   dig @94.242.218.162 -x 94.242.218.162

.. code-block:: console

   ;; ANSWER SECTION:
   162.218.242.94.in-addr.arpa. 604800 IN  PTR     dnstest.root.lu.

   ;; AUTHORITY SECTION:
   218.242.94.in-addr.arpa. 604800     IN  NS      ns.dnstest.root.lu.

Если вы получили подобный результат, значит ваш *DNS сервер* сконфигурирован правильно.

Настройка *Bind* для *IPv6*
---------------------------

В качестве примера к серверу прикреплена *IPv6* подсеть 2a01:608:ffff:a02b::/64. Основной адрес, настроеный на интерфейсе сервера - 2a01:608:ffff:a02b::2

Сначала в файле ``/etc/bind/named.conf.options`` подправим секцию ``forwarders``, пропишем публичный *IPv6 DNS сервер* компании **Google**.

.. code-block:: console

   sudo nano /etc/bind/named.conf.options

Файл ``/named.conf.options`` должен иметь следующий вид:

.. code-block:: console

   options {
     directory "/var/cache/bind";

     // If there is a firewall between you and nameservers you want
     // to talk to, you may need to fix the firewall to allow multiple
     // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

     // If your ISP provided one or more IP addresses for stable
     // nameservers, you probably want to use them as forwarders.
     // Uncomment the following block, and insert the addresses replacing
     // the all-0's placeholder.

     forwarders {
           8.8.8.8; # Google Public DNS IPv4 addresses
           8.8.4.4; # Google Public DNS IPv4 addresses
           2001:4860:4860::8888; # Google Public DNS IPv6 address
           2001:4860:4860::8844; # Google Public DNS IPv6 address
     };

     //========================================================================
     // If BIND logs error messages about the root key being expired,
     // you will need to update your keys.  See https://www.isc.org/bind-keys
     //========================================================================
     dnssec-validation auto;

     auth-nxdomain no;    # conform to RFC1035
     listen-on-v6 { any; };
   };

Далее, добавьте следующую информацию в файл ``/etc/bind/zones/db.dnstest.root.lu``

.. code-block:: console

   ;dnsserver
   @   IN AAAA 2a01:608:ffff:a02b::2
   ns  IN AAAA 2a01:608:ffff:a02b::2

   ;clients
   subdomain1 IN AAAA 2a01:608:ffff:a02b::3
   subdomain2 IN AAAA 2a01:608:ffff:a02b::4
   subdomain3 IN AAAA 2a01:608:ffff:a02b::5

Для настройки обратного просмотра нужно вписать дополнительный диапазон адресов в ``/etc/bind/named.conf.local``. В конце файла добавьте строки, содержащие объявление диапазона (*zone*), и не забудьте что каждый шестнадцатиричный блок адреса нужно дополнять нулём (если необходимо), для того чтобы он содержал 4 символа:

.. code-block:: console

   sudo nano /etc/bind/named.conf.local

.. code-block:: console

   zone "b.2.0.a.f.f.f.f.8.0.6.0.1.0.a.2.ip6.arpa" {
	    type master;
	    notify no;
	    file "/etc/bind/zones/db.b.2.0.a.f.f.f.f.8.0.6.0.1.0.a.2.ip6.arpa";
   };

Теперь создадим новый файл обратной зоны для *IPv6*:

.. code-block:: console

   sudo nano /etc/bind/zones/db.b.2.0.a.f.f.f.f.8.0.6.0.1.0.a.2.ip6.arpa

Заполним его следующим содержимым:

.. code-block:: console

   $ORIGIN b.2.0.a.f.f.f.f.8.0.6.0.1.0.a.2.ip6.arpa.
   $TTL 1d
   @  IN  SOA  dnstest.root.lu. admin.dnstest.root.lu. (
           2014011501      ;Serial
           86400           ;Refresh
           7200            ;Retry
           2592000         ;Expire
           172800          ;Minimum TTL
   )
       IN  NS  ns.dnstest.root.lu.

   ; 2a01:608:ffff:a02b::/64
   2.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0  IN  PTR  ns.dnstest.root.lu.

   3.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0  IN  PTR  subdomain1.dnstest.root.lu.
   4.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0  IN  PTR  subdomain2.dnstest.root.lu.
   5.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0  IN  PTR  subdomain3.dnstest.root.lu.

В PTR записи необходимо указать только адрес хоста (без адреса подсети). Каждый блок адреса должен быть дополнен нулями.

.. hint:: К примеру адрес *DNS сервера* (2a01:608:ffff:a02b::2) имеет адрес подсети '2a01:608:ffff:a02b' и адрес хоста '2'. Адрес хоста преобразован в '0000:0000:0000:0002' и записан в обратном порядке, с разделителями в виде точек.

После этого не забудьте перезапустить сервис *Bind* и проверить его статус.

.. code-block:: console

   sudo service bind9 restart
   sudo service bind9 status

.. code-block:: console

   ● bind9.service - BIND Domain Name Server
      Loaded: loaded (/lib/systemd/system/bind9.service; enabled; vendor preset: enabled)
      Drop-In: /run/systemd/generator/bind9.service.d
               └─50-insserv.conf-$named.conf
      Active: active (running) since Sat 2016-04-23 23:38:16 CEST; 2min 31s ago

Итак, вывод статуса без ошибок. Теперь можно проверить правильность конфигурации *DNS/rDNS* зоны для *IPv6*.

Резолв зоны ``dnstest.root.lu``:

.. code-block:: console

   dig @94.242.218.162 dnstest.root.lu

.. code-block:: console

   ;; ANSWER SECTION:
   dnstest.root.lu.        604800  IN      A       94.242.218.162

   ;; AUTHORITY SECTION:
   dnstest.root.lu.        604800  IN      NS      ns.dnstest.root.lu.

   ;; ADDITIONAL SECTION:
   ns.dnstest.root.lu.     604800  IN      A       94.242.218.162
   ns.dnstest.root.lu.     604800  IN      AAAA    2a01:608:ffff:a02b::2

Обратный резольв *IPv6*:

.. code-block:: console

   dig @94.242.218.162 -x 2a01:608:ffff:a02b::2

.. code-block:: console

   ;; ANSWER SECTION:
   2.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.b.2.0.a.f.f.f.f.8.0.6.0.1.0.a.2.ip6.arpa. 86400 IN PTR ns.dnstest.root.lu.

   ;; AUTHORITY SECTION:
   b.2.0.a.f.f.f.f.8.0.6.0.1.0.a.2.ip6.arpa. 86400 IN NS ns.dnstest.root.lu.

Ваш *DNS сервер* теперь готов к работе!

.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
