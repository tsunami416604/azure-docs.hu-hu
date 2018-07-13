---
title: Virtuálisgép-méretezési csoportok létrehozása az Azure-ban az Ansible-lel
description: Ismerje meg, hogyan létrehozása és konfigurálása az Azure-beli virtuális gép méretezési az Ansible használatával
ms.service: ansible
keywords: az ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoportot, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009006"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Virtuálisgép-méretezési csoportok létrehozása az Azure-ban az Ansible-lel
Az Ansible segítségével automatizálhatja a telepítését és konfigurálását az erőforrásoknak a környezetben. Az Ansible segítségével kezelheti a virtuálisgép-méretezési csoportot (VMSS) az Azure-ban, ugyanaz lenne, kezelheti a bármely más Azure-erőforrás. Ez a cikk bemutatja, hogyan hozzon létre, és a horizontális felskálázás egy virtuálisgép-méretezési csoportot az Ansible használatával. 

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.
- **Az Ansible konfigurálása** - [létrehozása Azure hitelesítő adatait, és az Ansible konfigurálása](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Az Ansible és az Azure Python SDK-modulok** 
  - [7.4 centOS](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> Az Ansible 2.6 futtassa a következő szükséges ebben az oktatóanyagban a mintául szolgáló forgatókönyvek. 

## <a name="create-a-vmss"></a>Hozzon létre egy VMSS
Ez a fejezet ismerteti egy minta az Ansible-forgatókönyv, amely meghatározza az alábbi forrásanyagokat:
- **Erőforráscsoport** , amelybe az összes erőforrás lesz telepítve
- **Virtuális hálózat** a 10.0.0.0/16 címteret a
- **Alhálózat** a virtuális hálózaton belül
- **Nyilvános IP-cím** adott wllows, hogy az erőforrások eléréséhez az interneten keresztül
- **Hálózati biztonsági csoport** , amely meghatározza, hogy a hálózati forgalmat a virtuálisgép-méretezési csoportot, és
- **Terheléselosztó** , amely elosztja a forgalmat terheléselosztó szabályait használó meghatározott virtuális gépek között
- **Virtuálisgép-méretezési csoport** , amely a létrehozott erőforrásokat használja

Adja meg a saját jelszavát a *admin_password* értéket.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Szeretne létrehozni a virtuálisgép-méretezési készlet környezetet az ansible segítségével, mint a fenti forgatókönyv mentése `vmss-create.yml`, vagy [töltse le a mintát az Ansible-forgatókönyvek](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Az Ansible-forgatókönyvek futtatásához használja a **ansible-forgatókönyvek** paranccsal a következőképpen:

  ```bash
  ansible-playbook vmss-create.yml
  ```

Az alábbi példához hasonló kimenetet azt mutatja, hogy a virtuálisgép-méretezési csoport a forgatókönyv futtatása után készlet sikeresen létrehozva:

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Horizontális felskálázás egy VMSS
A létrehozott virtuálisgép-méretezési készlet két példánnyal rendelkezik. Ha a virtuálisgép-méretezési csoportba az Azure Portalon keresse meg, láthatja, **standard_ds1_v2 méret (2 példány)**. Is használhatja a [Azure Cloud Shell](https://shell.azure.com/) belül a Cloud Shellben a következő parancs futtatásával:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

A kimenet az alábbihoz hasonló lesz:

  ```bash
  {
    "capacity": 2,
  }
  ```

Most már most skálázhatja a két példány három alkalmazáspéldányra. A következő Ansible-forgatókönyvek kódot a virtuálisgép-méretezési csoport adatait kérdezi le, és a kapacitás a két három változik. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Horizontális felskálázási a virtuális gép méretezési csoportot hozott létre, mint a fenti forgatókönyv mentése `vmss-scale-out.yml` vagy [töltse le a mintát az Ansible-forgatókönyvek](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)). 

A következő parancsot fog futni a forgatókönyvet:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Az Ansible-forgatókönyvek futtatása kimenetében látható, hogy a virtuális gép méretezési rendelkezik lett sikeresen horizontálisan felskálázott:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Ha konfigurálta az Azure Portalon, megjelenik a virtuális gép méretezési lépjen **standard_ds1_v2 méret (3 példányok)**. Azt is ellenőrizheti a módosítást a [Azure Cloud Shell](https://shell.azure.com/) a következő parancs futtatásával:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

A parancs futtatása a Cloud Shellben eredményeit jeleníti meg, hogy létezik-e most már három példányban. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Az Ansible minta forgatókönyv az vmss-hez](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)