Установка провайдера *VirtualBox* для *terraform*
=================================================

Перед установкой провайдера *VirtualBox* необходимо установить компилятор языка *Go*, как описано по ссылке_.

.. _ссылке: ../go/go_install

Установка производится следующей командой:

.. code-block:: console

   $ go get github.com/terra-farm/terraform-provider-virtualbox

Пример конфигурации:

.. code-block:: javascript
   :linenos:

   resource "virtualbox_vm" "node" {
       count = 2
       name = "${format("node-%02d", count.index+1)}"

       image = "~/ubuntu-15.04.tar.xz"
       cpus = 2
       memory = "512mib"

       network_adapter {
           type = "nat"
       }

       network_adapter {
           type = "bridged"
           host_interface = "en0"
       }

       optical_disks = ["./cloudinit.iso"]
   }

   output "IPAddr" {
       # Get the IPv4 address of the bridged adapter (the 2nd one) on 'node-02'
       value = "${element(virtualbox_vm.node.*.network_adapter.1.ipv4_address, 1)}"
   }

.. note:: Полезные ссылки:
   https://github.com/terra-farm/terraform-provider-virtualbox


.. image:: https://readthedocs.org/projects/mylittlewiki/badge/?version=latest
   :target: https://mylittlewiki.readthedocs.io/ru/latest/?badge=latest
   :alt: Documentation Status
