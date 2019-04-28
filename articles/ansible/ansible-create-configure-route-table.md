---
title: Oktatóanyag – konfigurálása az Azure útválasztási táblázatban az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása, módosítása és törlése az Azure útválasztási táblázatban az Ansible-lel
keywords: az ansible, azure, devops, bash, forgatókönyv, hálózatkezelés, útvonal, útválasztási táblázat
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 3d20a7bb98ba266850baa0512f5b767f8b649767
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764488"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Oktatóanyag: Konfigurálja az Azure útválasztási táblázatban az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Az Azure automatikusan irányítja az Azure az alhálózatok, virtuális hálózatok közötti adatforgalom és a helyszíni hálózatokkal. Ha a környezet útválasztás feletti ellenőrzés van szüksége, létrehozhat egy [útvonaltábla](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Hozzon létre egy útválasztási táblázatot egy virtuális hálózat és alhálózat útválasztási táblázat társítása alhálózattal Disassociate egy alhálózatról hozzon létre egy útválasztási táblázatot, és törölje a létrehozás átirányítja a lekérdezést egy útválasztási táblázatot egy útvonaltábla törlése

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

A forgatókönyv kód ebben a szakaszban létrehoz egy útvonaltáblát. Útvonaltábla korlátok kapcsolatos tudnivalókat lásd: [Azure korlátai](/azure/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

Mentse a következő forgatókönyvet `route_table_create.yml` néven:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Ebben a szakaszban a forgatókönyv kódot:

* Létrehoz egy virtuális hálózatot
* Létrehoz egy alhálózatot a virtuális hálózaton belül
* Hozzárendeli egy útválasztási táblázatot az alhálózathoz

Az útvonaltáblák nem társított virtuális hálózatokhoz. Inkább útvonaltáblák társítva, egy virtuális hálózat alhálózatához.

A virtuális hálózat és az útválasztási táblázat kell törölnie a megegyező Azure-helyen és az előfizetés.

Alhálózatok és az útválasztási táblázatokat egy-a-többhöz kapcsolattal rendelkeznek. Egy alhálózaton nincs társítva útvonaltábla vagy egy útválasztási táblázatot lehet definiálni. Útvonaltáblák társítható nincs, egy vagy több alhálózatot. 

Az alhálózatról adatforgalmat alapján:

- útvonaltáblák belül-útvonalak
- [Az alapértelmezett útvonalak](/azure/virtual-network/virtual-networks-udr-overview#default)
- útvonalak propagálni egy helyszíni hálózat

A virtuális hálózat kapcsolódnia kell egy Azure virtuális hálózati átjárót. Az átjáró lehet az ExpressRoute vagy VPN VPN gateway a BGP használatakor.

Mentse a következő forgatókönyvet `route_table_associate.yml` néven:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create virtual network
      azure_rm_virtualnetwork:
        name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
        - 10.1.0.0/16
        - 172.100.0.0/16
        dns_servers:
        - 127.0.0.1
        - 127.0.0.3
    - name: Create a subnet with route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
        route_table: "{ route_table_name }"
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Egy alhálózatról egy útválasztási táblázatot társításának megszüntetése

A forgatókönyv kód ebben a szakaszban egy útválasztási táblázatot egy alhálózatról dissociates.

Amikor leválasztása folyamatban van egy alhálózatról egy útválasztási táblázatot, állítsa be a `route_table` , az alhálózat `None`. 

Mentse a következő forgatókönyvet `route_table_dissociate.yml` néven:

```yml
- hosts: localhost
  vars:
    subnet_name: mySubnet
    virtual_network_name: myVirtualNetwork 
    resource_group: myResourceGroup
  tasks:
    - name: Dissociate a route table
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ virtual_network_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: "10.1.0.0/24"
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Útvonal létrehozása

A forgatókönyv kód ebben a szakaszban egy útvonalat egy útválasztási táblázatot belül. 

Mentse a következő forgatókönyvet `route_create.yml` néven:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        next_hop_type: virtual_network_gateway
        address_prefix: "10.1.0.0/16"
        route_table_name: "{{ route_table_name }}"
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

* `virtual_network_gateway` típusúként van definiálva `next_hop_type`. Hogyan választja ki a Azure útvonalakkal kapcsolatos további információkért lásd: [Útválasztás áttekintése](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` típusúként van definiálva `10.1.0.0/16`. Az előtag nem duplikálható az útvonal a táblán belül.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Útvonal törlése

Ebben a szakaszban a forgatókönyv kód töröl egy útválasztási táblázatot egy útvonalat.

Mentse a következő forgatókönyvet `route_delete.yml` néven:

```yml
- hosts: localhost
  vars:
    route_name: myRoute
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Remove route
      azure_rm_route:
        name: "{{ route_name }}"
        resource_group: "{{ resource_group }}"
        route_table_name: "{{ route_table_name }}"
        state: absent
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Útválasztási tábla adatainak beolvasása

A forgatókönyv kód ebben a szakaszban az Ansible-modult használja `azure_rm_routetable_facts` útválasztási táblázat információk lekéréséhez.

Mentse a következő forgatókönyvet `route_table_facts.yml` néven:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks: 
    - name: Get route table information
      azure_rm_routetable_facts:
         resource_group: "{{ resource_group }}"
         name: "{{ route_table_name }}"
      register: query
    
    - debug:
         var: query.route_tables[0]
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Útválasztási táblázat törlése

A forgatókönyv kód ebben a szakaszban egy útválasztási táblázatot.

A törölt egy útválasztási táblázatot az összes saját útvonalakat is törlődik.

Útválasztási táblázat nem törölhető, ha egy alhálózathoz kapcsolódik. [Leválasztja az útvonaltáblát az alhálózatok](#dissociate-a-route-table-from-a-subnet) az útvonaltábla törlése megkísérlése előtt. 

Mentse a következő forgatókönyvet `route_table_delete.yml` néven:

```yml
- hosts: localhost
  vars:
    route_table_name: myRouteTable
    resource_group: myResourceGroup
  tasks:
    - name: Create a route table
      azure_rm_routetable:
        name: "{{ route_table_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)