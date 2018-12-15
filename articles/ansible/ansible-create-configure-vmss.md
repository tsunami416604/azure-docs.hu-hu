---
title: Virtuálisgép-méretezési csoportok létrehozása az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan hozhat létre és konfigurálhat virtuálisgép-méretezési csoportokat az Azure-ban az Ansible használatával
ms.service: ansible
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: 8dacfb054a36801c076435037a530daa87c4a89c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409401"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Virtuálisgép-méretezési csoportok létrehozása az Azure-ban az Ansible használatával
Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible segítségével ugyanúgy felügyelheti a virtuálisgép-méretezési csoportokat (VMSS) az Azure-ban, ahogy azt bármely más Azure-erőforrással tenné. Ebből a cikkből megtudhatja, hogyan hozhat létre és skálázhat fel horizontálisan virtuálisgép-méretezési csoportokat az Ansible használatával. 

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.6-os verziója szükséges. 

## <a name="create-a-vmss"></a>Virtuálisgép-méretezési csoport (VMSS) létrehozása
Ez a szakasz egy Ansible-mintaforgatókönyvet mutat be, amely az alábbi erőforrásokat határozza meg:
- Az **erőforráscsoport**, amelybe az erőforrásokat telepíteni szeretnénk
- A 10.0.0.0/16 címtartományban szereplő **virtuális hálózat**
- A virtuális hálózaton belüli **alhálózat**
- A **Nyilvános IP-cím**, amely lehetővé teszi az erőforrások elérését az internetről.
- A **hálózati biztonság csoport** a virtuálisgép-méretezési csoportba irányuló és abból kiinduló forgalom szabályozásához.
- A **terheléselosztó**, amely a terheléselosztó szabályait használó meghatározott virtuális gépek készletében osztja szét a forgalmat.
- A **virtuálisgép-méretezési csoport**, amely a létrehozott erőforrásokat használja.

Adja meg az *admin_password* értékhez tartozó jelszót.

  ```yml
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

Ha szeretné létrehozni a virtuálisgép-méretezési csoportot az Ansible használatával, mentse az előző forgatókönyvet `vmss-create.yml` néven, vagy [töltse le az Ansible-mintaforgatókönyvet](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

  ```bash
  ansible-playbook vmss-create.yml
  ```

A forgatókönyv futtatása után az alábbi példához hasonló kimenetben látható, hogy a virtuálisgép-méretezési csoport sikeresen létrejött:

  ```Output
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

## <a name="scale-out-a-vmss"></a>VMSS horizontális felskálázása
A létrehozott virtuálisgép-méretezési csoport két példánnyal rendelkezik. Ha a virtuálisgép-méretezési csoporthoz lép az Azure Portalon, megjelenik a **Standard_DS1_v2 (2 példánnyal rendelkező)** csoport. Az [Azure Cloud Shellt](https://shell.azure.com/) az alábbi parancs Cloud Shell-beli futtatásával is használhatja:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

A következő kimenethez hasonló eredmények jelennek meg:

  ```bash
  {
    "capacity": 2,
  }
  ```

Most bővítsük ki a két példányt három példányra. Az Ansible-forgatókönyv alábbi kódja információt ad vissza a virtuális gép méretéről, és a kapacitást kettőről háromra módosítja. 

  ```yml
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

Ha szeretné horizontálisan felskálázni a virtuálisgép-méretezési csoportot az Ansible használatával, mentse az előző forgatókönyvet `vmss-scale-out.yml` néven, vagy [töltse le az Ansible-mintaforgatókönyvet](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml). 

Az alábbi parancs futtatja a forgatókönyvet:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Az Ansible-forgatókönyv futtatásából származó kimenetben látható, hogy a virtuálisgép-méretezési csoport horizontális felskálázása sikeres volt:

  ```Output
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

Ha a konfigurált virtuálisgép-méretezési csoporthoz lép az Azure Portalon, megjelenik a **Standard_DS1_v2 (3 példánnyal rendelkező)** csoport. A változásokat ellenőrizheti az [Azure Cloud Shell-lel](https://shell.azure.com/), ha futtatja az alábbi parancsot:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

A parancs Cloud Shell-beli futtatásának eredménye azt jelzi, hogy jelenleg három példány létezik. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Virtuális gép méretezési csoportok használata az Ansible-alkalmazások központi telepítése](https://docs.microsoft.com/azure/ansible/ansible-deploy-app-vmss)

> [Az Ansible használatával virtuálisgép-méretezési csoport automatikus méretezése](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)