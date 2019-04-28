---
title: Oktatóanyag – konfigurálja az Azure virtuális hálózati társviszony-létesítést az Ansible-lel |} A Microsoft Docs
description: 'Útmutató: virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony az Ansible használatával.'
keywords: ansible, azure, devops, bash, playbook, networking, peering
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: d3d1aab17a94e928d113246f280c3391cae85655
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767088"
---
# <a name="tutorial-configure-azure-virtual-network-peering-using-ansible"></a>Oktatóanyag: Konfigurálja az Azure virtuális hálózati társviszony-létesítést az Ansible-lel

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Virtuális hálózatok (VNet) közötti társviszony](/azure/virtual-network/virtual-network-peering-overview) lehetővé teszi, hogy zökkenőmentesen csatlakoztathatja az Azure két virtuális hálózat. A társviszonyba, a két virtuális hálózat a csatlakozás szempontjából egyetlen jelennek meg. 

Az adatforgalmat ugyanazon a virtuális hálózaton keresztül a magánhálózati IP-címek a virtuális gépek között. Hasonlóképpen virtuális hálózat társviszonyban lévő virtuális gépek közötti adatforgalmat a Microsoft gerincinfrastruktúráján halad át. Ennek eredményeképpen különböző virtuális hálózatokban lévő virtuális gépek kommunikálhatnak egymással.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két virtuális gép létrehozása
> * A két virtuális hálózat társviszonyba állítása
> * A két hálózat közötti társviszony törlése

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-two-resource-groups"></a>Két erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

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

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- Virtuális hálózat létrehozása
- Hozzon létre egy alhálózatot a virtuális hálózaton belül

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

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- Virtuális hálózat létrehozása
- Hozzon létre egy alhálózatot a virtuális hálózaton belül

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

## <a name="peer-the-two-virtual-networks"></a>A két virtuális hálózat társviszonyba állítása

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- Virtuális hálózatok közötti társviszony inicializálása
- Két korábban létrehozott virtuális hálózatok társviszonyba állítása

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

## <a name="delete-the-virtual-network-peering"></a>A virtuális hálózati társviszony törlése

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- A két korábban létrehozott virtuális hálózatok közötti társviszony törlése

```yml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>A mintául szolgáló forgatókönyvek beolvasása

A teljes minta forgatókönyv beolvasásához két módja van:

- [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) , és mentse a `vnet_peering.yml`.
- Hozzon létre egy új fájlt `vnet_peering.yml` és másolja bele a következő tartalommal:

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

Ebben a szakaszban szereplő mintakódban forgatókönyv teszteléséhez látható az oktatóanyag során különböző funkcióihoz használatos.

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- Az a `vars` szakaszban, cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevét.

Futtassa a forgatókönyvet, az ansible-forgatókönyvek paranccsal:

```bash
ansible-playbook vnet_peering.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

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

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- A két erőforrás-csoportok korábban létrehozott DELETE

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

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- Cserélje le a `{{ resource_group_name-1 }}` létrehozott helyőrzőt az első erőforráscsoport nevét.
- Cserélje le a `{{ resource_group_name-2 }}` létrehozott helyőrzőt a második erőforráscsoport nevét.
- A két megadott erőforráscsoport belül minden erőforrás törlődni fog.

Futtassa a forgatókönyvet, az ansible-forgatókönyvek paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)