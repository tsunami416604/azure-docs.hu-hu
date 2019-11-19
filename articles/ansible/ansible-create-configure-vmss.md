---
title: Oktatóanyag – virtuálisgép-méretezési csoportok konfigurálása az Azure-ban a Ansible használatával
description: Ismerje meg, hogyan hozhat létre és konfigurálhat virtuálisgép-méretezési csoportokat az Azure-ban a Ansible használatával
keywords: ansible, azure, devops, bash, forgatókönyv, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156548"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: virtuálisgép-méretezési csoportok konfigurálása az Azure-ban a Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Virtuális gép erőforrásainak konfigurálása
> * Méretezési csoport konfigurálása
> * Méretezési csoport méretezése a virtuálisgép-példányok növelésével 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Méretezési csoport konfigurálása

Az ebben a szakaszban szereplő forgatókönyv-kód a következő erőforrásokat határozza meg:

* Az az **erőforráscsoport** , amelyben az összes erőforrás üzembe lesz helyezve.
* A 10.0.0.0/16 címtartományban szereplő **virtuális hálózat**
* A virtuális hálózaton belüli **alhálózat**
* A **Nyilvános IP-cím**, amely lehetővé teszi az erőforrások elérését az internetről.
* **Hálózati biztonsági csoport** , amely a méretezési csoporton belüli és kívüli hálózati forgalom áramlását vezérli
* A **terheléselosztó**, amely a terheléselosztó szabályait használó meghatározott virtuális gépek készletében osztja szét a forgalmat.
* A **virtuálisgép-méretezési csoport**, amely a létrehozott erőforrásokat használja.

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) forgatókönyvet, és mentse a `vmss-create.yml`ba.
* Hozzon létre egy `vmss-create.yml` nevű új fájlt, és másolja bele a következő tartalomba:

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* A `vars` szakaszban cserélje le a `{{ admin_password }}` helyőrzőt a saját jelszavára.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook vmss-create.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

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

## <a name="view-the-number-of-vm-instances"></a>Virtuálisgép-példányok számának megtekintése

A [konfigurált méretezési csoportnak](#configure-a-scale-set) jelenleg két példánya van. A következő lépések segítségével ellenőrizheti, hogy az érték:

1. Bejelentkezés az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navigáljon a beállított méretezési csoporthoz.

1. A méretezési csoport neve a zárójelben lévő példányok száma szerint jelenik meg: `Standard_DS1_v2 (2 instances)`

1. A következő parancs futtatásával ellenőrizheti a [Azure Cloud Shell](https://shell.azure.com/) példányainak számát is:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    A Cloud Shell Azure CLI-parancs futtatásának eredményei azt mutatják, hogy már létezik három példány: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Méretezési csoport méretezése

Az ebben a szakaszban található forgatókönyv-kód beolvassa a méretezési csoport adatait, és két-három értékre módosítja a kapacitását.

A minta forgatókönyv két módon szerezhető be:

* [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) forgatókönyvet, és mentse a `vmss-scale-out.yml`ba.
* Hozzon létre egy `vmss-scale-out.yml` nevű új fájlt, és másolja bele a következő tartalomba:

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook vmss-scale-out.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

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

Ellenőrizze a munkájának eredményeit a Azure Portal használatával:

1. Bejelentkezés az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navigáljon a beállított méretezési csoporthoz.

1. A méretezési csoport neve a zárójelben lévő példányok száma szerint jelenik meg: `Standard_DS1_v2 (3 instances)` 

1. A változásokat ellenőrizheti az [Azure Cloud Shell-lel](https://shell.azure.com/), ha futtatja az alábbi parancsot:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    A Cloud Shell Azure CLI-parancs futtatásának eredményei azt mutatják, hogy már létezik három példány: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokban az Azure-ban a Ansible használatával](./ansible-deploy-app-vmss.md)