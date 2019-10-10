---
title: Oktatóanyag – Azure-beli virtuális hálózatok társításának konfigurálása a Ansible használatával
description: Ismerje meg, hogyan csatlakoztathatók a virtuális hálózatok a virtuális hálózatokkal a Ansible használatával.
keywords: Ansible, Azure, devops, bash, ötletekbõl, hálózatkezelés, peering
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 180bdff48a2ace4dfee1d1cb10eb75a33d360f4c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241238"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Oktatóanyag: Azure-beli virtuális hálózati társítás konfigurálása a Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

A [Virtual Network (VNet)](/azure/virtual-network/virtual-network-peering-overview) -társítás lehetővé teszi két Azure-beli virtuális hálózat zökkenőmentes összekapcsolását. A társítást követően a két virtuális hálózat a kapcsolati céloknak megfelelően jelenik meg. 

A forgalom a magánhálózati IP-címeken keresztül azonos virtuális hálózatban lévő virtuális gépek között lesz átirányítva. Hasonlóképpen, a virtuális gépek közötti adatforgalom a Microsoft gerinc-infrastruktúrán keresztül irányítható. Ennek eredményeképpen a különböző virtuális hálózatokban lévő virtuális gépek kommunikálhatnak egymással.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két virtuális gép létrehozása
> * Egyenrangú a két virtuális hálózat
> * A két hálózat közötti társítás törlése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Két erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

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

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

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

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

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

## <a name="peer-the-two-virtual-networks"></a>Egyenrangú a két virtuális hálózat

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- Virtuális hálózati társak inicializálása
- Társ két korábban létrehozott virtuális hálózat

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

## <a name="delete-the-virtual-network-peering"></a>A virtuális hálózati társak törlése

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- A két korábban létrehozott virtuális hálózat közötti társítás törlése

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>A minta forgatókönyvének beolvasása

A teljes példa a következő két módon szerezhető be:

- [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) forgatókönyvet, és mentse a `vnet_peering.yml` értékre.
- Hozzon létre egy `vnet_peering.yml` nevű új fájlt, és másolja bele a következő tartalomba:

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

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Az ebben a szakaszban ismertetett példa az oktatóanyagban bemutatott különböző szolgáltatások tesztelésére szolgál.

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- A `vars` szakaszban cserélje le az `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevére.

Futtassa a forgatókönyvt a Ansible-ötletekbõl paranccsal:

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

Az ebben a szakaszban szereplő forgatókönyv-forgatókönyv a következőhöz használható:

- Törölje a korábban létrehozott két erőforrás-csoportot.

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

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- Cserélje le a `{{ resource_group_name-1 }}` helyőrzőt az elsőként létrehozott erőforráscsoport nevére.
- Cserélje le a `{{ resource_group_name-2 }}` helyőrzőt a létrehozott második erőforráscsoport nevére.
- A két megadott erőforráscsoport összes erőforrása törölve lesz.

Futtassa a forgatókönyvt a Ansible-ötletekbõl paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)