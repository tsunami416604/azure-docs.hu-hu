---
title: Oktatóanyag – Az Azure virtuális hálózati társviszony-létesítésének konfigurálása az Ansible használatával
description: Ismerje meg, hogyan használhatja az Ansible segítségével a virtuális hálózatokat a virtuális hálózati társviszony-létesítéssel.
keywords: ansible, azúr, devops, bash, ötletekbõl, hálózatépítés, peering
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 78699a005d721b46a88a26452f5db68438793d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155732"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Oktatóanyag: Konfigurálja az Azure virtuális hálózati társviszony-létesítését az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Virtuális hálózati (VNet) társviszony-létesítés](/azure/virtual-network/virtual-network-peering-overview) lehetővé teszi, hogy zökkenőmentesen csatlakozni két Azure virtuális hálózatok. A társviszony-létesítést követően a két virtuális hálózat egyként jelenik meg kapcsolódási célokra. 

A forgalom ugyanazon virtuális hálózat virtuális gépei között privát IP-címeken keresztül van irányítva. Hasonlóképpen a virtuális gépek közötti forgalom egy társviszony-létesített virtuális hálózat a Microsoft gerinchálózati infrastruktúra között. Ennek eredményeképpen a különböző virtuális hálózatokban lévő virtuális gépek kommunikálhatnak egymással.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két virtuális gép létrehozása
> * A két virtuális hálózat társviszonya
> * A két hálózat közötti társviszony-létesítés törlése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Két erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- Két erőforráscsoport létrehozása 

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## <a name="create-the-first-virtual-network"></a>Az első virtuális hálózat létrehozása

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- Virtuális hálózat létrehozása
- Alhálózat létrehozása a virtuális hálózaton belül

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## <a name="create-the-second-virtual-network"></a>A második virtuális hálózat létrehozása

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- Virtuális hálózat létrehozása
- Alhálózat létrehozása a virtuális hálózaton belül

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## <a name="peer-the-two-virtual-networks"></a>A két virtuális hálózat társviszonya

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- Virtuálishálózati társviszony-létesítés inicializálása
- Társ két korábban létrehozott virtuális hálózatok

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## <a name="delete-the-virtual-network-peering"></a>A virtuális hálózati társviszony-létesítés törlése

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- A két korábban létrehozott virtuális hálózat közötti társviszony-létesítés törlése

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>A minta forgatókönyvének beszereznie

A teljes forgatókönyv kétféleképpen szerezhető be:

- [Töltse le a forgatókönyvet,](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) és mentse el `vnet_peering.yml`.
- Hozzon létre `vnet_peering.yml` egy új fájl nevű és másolja be a következő tartalmat:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>A mintaforgatókönyv futtatása

A minta forgatókönyv kód ebben a szakaszban az oktatóanyagban látható különböző funkciók tesztelésére szolgál.

Íme néhány fontos megjegyzés, amelyet figyelembe kell venni a minta forgatókönyvével való munka során:

- A `vars` szakaszban cserélje `{{ resource_group_name }}` le a helyőrzőt az erőforráscsoport nevére.

Futtassa a forgatókönyvet az ansible-playbook paranccsal:

```bash
ansible-playbook vnet_peering.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Prepare random postfix] 
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create secondary resource group] 
changed: [localhost]

TASK [Create first virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Ceate second virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Initial vnet peering] 
changed: [localhost]

TASK [Connect vnet peering] 
changed: [localhost]

TASK [Delete vnet peering] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Az ebben a szakaszban található mintaforgatókönyv-kód a következőkre szolgál:

- A korábban létrehozott két erőforráscsoport törlése

Mentse a következő forgatókönyvet `cleanup.yml` néven:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name-1 }}"
    resource_group_secondary: "{{ resource_group_name-2 }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

Íme néhány fontos megjegyzés, amelyet figyelembe kell venni a minta forgatókönyvével való munka során:

- Cserélje `{{ resource_group_name-1 }}` le a helyőrzőt az első létrehozott erőforráscsoport nevére.
- Cserélje `{{ resource_group_name-2 }}` le a helyőrzőt a létrehozott második erőforráscsoport nevére.
- A két megadott erőforráscsoporton belüli összes erőforrás törlődik.

Futtassa a forgatókönyvet az ansible-playbook paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)