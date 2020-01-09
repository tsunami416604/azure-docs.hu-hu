---
title: Oktatóanyag – Azure Route-táblák konfigurálása a Ansible használatával
description: Ismerje meg, hogyan hozhat létre, kezelhet és törölhet Azure Route-táblákat a Ansible használatával. Azt is megtudhatja, hogyan hozhat létre és törölhet útvonalakat.
keywords: Ansible, Azure, devops, bash, ötletekbõl, hálózatkezelés, útvonal, útválasztási táblázat
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659797"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Oktatóanyag: az Azure Route-táblázatok konfigurálása a Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Az Azure automatikusan irányítja a forgalmat az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között. Ha nagyobb mértékű vezérlésre van szüksége a környezete útválasztásához, létrehozhat egy [útválasztási táblázatot](/azure/virtual-network/virtual-networks-udr-overview). 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Útválasztási táblázat létrehozása virtuális hálózat és alhálózat létrehozása útválasztási táblázat alhálózattal való hozzárendelésével az útválasztási táblázatot egy alhálózatból hozzon létre, és törölje az útvonalakat az útválasztási táblázat törléséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Az ebben a szakaszban található forgatókönyv-kód egy útválasztási táblázatot hoz létre. Az útvonal-táblázat korlátaival kapcsolatos információkért lásd: [Azure-korlátok](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits). 

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

Az ebben a szakaszban található forgatókönyv-kód:

* Létrehoz egy virtuális hálózatot
* Létrehoz egy alhálózatot a virtuális hálózaton belül.
* Útválasztási táblázat társítása az alhálózathoz

Az útválasztási táblák nincsenek virtuális hálózatokhoz társítva. Ehelyett az útválasztási táblák egy virtuális hálózat alhálózatához vannak társítva.

A virtuális hálózat és az útválasztási tábla ugyanazon az Azure-helyen és-előfizetésen belül kell, hogy legyen.

Az alhálózatok és az útválasztási táblák egy-a-többhöz kapcsolattal rendelkeznek. Az alhálózatok társított útválasztási táblázat vagy egy útválasztási tábla nélkül is meghatározhatók. Az útválasztási táblák a none, egy vagy több alhálózathoz is társíthatók. 

Az alhálózatról érkező forgalom irányítása a következő alapján történik:

- útválasztási táblákban definiált útvonalak
- [alapértelmezett útvonalak](/azure/virtual-network/virtual-networks-udr-overview#default)
- helyszíni hálózatról propagált útvonalak

A virtuális hálózatnak csatlakoztatva kell lennie egy Azure-beli virtuális hálózati átjáróhoz. Az átjáró lehet ExpressRoute vagy VPN, ha a BGP-t VPN-átjáróval használja.

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Útválasztási táblázat leválasztása egy alhálózatból

Az ebben a szakaszban található forgatókönyv-kód leválasztja az alhálózatból származó útválasztási táblázatot.

Ha egy alhálózatból kiválaszt egy útválasztási táblázatot, állítsa `None`re az alhálózat `route_table`. 

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Útvonal létrehozása

Az ebben a szakaszban szereplő forgatókönyv-kód az útválasztási táblázaton belüli útvonal. 

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

* `virtual_network_gateway` `next_hop_type`ként van definiálva. További információ arról, hogy az Azure hogyan választja ki az útvonalakat: az [Útválasztás áttekintése](/azure/virtual-network/virtual-networks-udr-overview).
* `address_prefix` `10.1.0.0/16`ként van definiálva. Az előtag nem duplikálható az útválasztási táblán belül.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Útvonal törlése

Az ebben a szakaszban szereplő forgatókönyv-kód törli az útvonalat egy útválasztási táblából.

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Útválasztási táblázat adatainak beolvasása

Az ebben a szakaszban található forgatókönyv-kód a Ansible modult használja `azure_rm_routetable_facts` az útválasztási tábla adatainak lekéréséhez.

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Útválasztási táblázat törlése

Az ebben a szakaszban egy útválasztási táblázatban szereplő forgatókönyv-kód.

Egy útválasztási tábla törlésekor az összes útvonala is törlődik.

Egy útválasztási tábla nem törölhető, ha egy alhálózathoz van társítva. Az útválasztási táblázat törlésének megkísérlése előtt [minden alhálózatból el kell választani az útválasztási táblázatot](#dissociate-a-route-table-from-a-subnet) . 

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)