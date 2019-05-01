---
title: Oktatóanyag – a virtual machine scale sets konfigurálása az Azure-ban az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan Ansible használatával hozzon létre, és a virtual machine scale sets konfigurálása az Azure-ban
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 13829d114f38f9a4cc490f46bb8c4b560b58e774
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698838"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: A virtual machine scale sets konfigurálása az Azure-ban az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Virtuálisgép-erőforrások konfigurálása
> * Egy méretezési csoportot konfigurálása
> * Növelje a hozzá tartozó Virtuálisgép-példányok méretezési méretezése 

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Egy méretezési csoportot konfigurálása

A forgatókönyv ebben a szakaszban határozza meg azt az alábbi forrásanyagokat:

* **Erőforráscsoport** , amelybe az összes erőforrás telepítve lesz.
* A 10.0.0.0/16 címtartományban szereplő **virtuális hálózat**
* A virtuális hálózaton belüli **alhálózat**
* A **Nyilvános IP-cím**, amely lehetővé teszi az erőforrások elérését az internetről.
* **Hálózati biztonsági csoport** , amely meghatározza, hogy a hálózati forgalmat a méretezési csoportot, és
* A **terheléselosztó**, amely a terheléselosztó szabályait használó meghatározott virtuális gépek készletében osztja szét a forgalmat.
* A **virtuálisgép-méretezési csoport**, amely a létrehozott erőforrásokat használja.

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) , és mentse a `vmss-create.yml`.
* Hozzon létre egy új fájlt `vmss-create.yml` és másolja bele a következő tartalommal:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
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

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* Az a `vars` szakaszban, cserélje le a `{{ admin_password }}` helyőrzőt a saját jelszavát.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook vmss-create.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Virtuálisgép-példányok számának megtekintéséhez

A [méretezési konfigurált](#configure-a-scale-set) jelenleg két példánnyal rendelkezik. Győződjön meg arról, hogy az érték a következő lépések használhatók:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Keresse meg a méretezési csoportban konfigurált.

1. A méretezési csoport-példányok neve zárójelben jelenik meg: `Standard_DS1_v2 (2 instances)`

1. Azt is ellenőrizheti a példányok száma a [Azure Cloud Shell](https://shell.azure.com/) a következő parancs futtatásával:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Az Azure CLI-parancs futtatása a Cloud Shellben eredményei azt mutatják, hogy létezik-e most már három példányban: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Horizontális felskálázás egy méretezési csoportot

A forgatókönyv kódot ebben a szakaszban a méretezési adatait kérdezi le, és a kapacitás a két három változik.

A minta forgatókönyv beolvasásához két módja van:

* [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) , és mentse a `vmss-scale-out.yml`.
* Hozzon létre egy új fájlt `vmss-scale-out.yml` és másolja bele a következő tartalommal:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
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

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook vmss-scale-out.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
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

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Az eredmények ellenőrzése

Ellenőrizze az eredményeit a munkáját az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Keresse meg a méretezési csoportban konfigurált.

1. A méretezési csoport-példányok neve zárójelben jelenik meg: `Standard_DS1_v2 (3 instances)` 

1. A változásokat ellenőrizheti az [Azure Cloud Shell-lel](https://shell.azure.com/), ha futtatja az alábbi parancsot:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Az Azure CLI-parancs futtatása a Cloud Shellben eredményei azt mutatják, hogy létezik-e most már három példányban: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Alkalmazások telepítése a virtuális gép méretezési csoportok az Azure-ban az Ansible-lel](./ansible-deploy-app-vmss.md)