---
title: Oktatóanyag – Az Azure-útvonaltáblák konfigurálása az Ansible használatával
description: Ismerje meg, hogyan hozhat létre, kezelhet és törölhet Azure-útvonaltáblákat az Ansible használatával. Azt is megtudhatja, hogyan hozhat létre és törölhet útvonalakat.
keywords: ansible, azúr, devops, bash, ötletekbõl, hálózatépítés, útvonal, útvonal tábla
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 1f08aebe7e9dcc1c5687f50ac91c7cb8cc8a62eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659797"
---
# <a name="tutorial-configure-azure-route-tables-using-ansible"></a>Oktatóanyag: Az Azure-útvonaltáblák konfigurálása az Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

Az Azure automatikusan irányítja az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok közötti forgalmat. Ha több szabályszükséges a környezet útválasztása felett, létrehozhat egy [útvonaltáblát.](/azure/virtual-network/virtual-networks-udr-overview) 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> Útvonaltábla létrehozása Virtuális hálózat létrehozása és alhálózat Társítása útvonaltáblát egy alhálózattal Az útvonaltábla társításának leválasztása az alhálózathoz Útvonalak létrehozása és törlése Útvonaltábla törlése Útvonaltábla törlése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása

Ebben a szakaszban a forgatókönyv-kód létrehoz egy útvonaltáblát. Az útvonaltábla-korlátokról az [Azure-korlátok](/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-resource-manager-virtual-networking-limits)című témakörben talál további információt. 

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal

A forgatókönyv kód ebben a szakaszban:

* Virtuális hálózat létrehozása
* Alhálózat létrehozása a virtuális hálózaton belül
* Útvonaltábla az alhálózathoz társítja

Az útvonaltáblák nincsenek virtuális hálózatokhoz társítva. Ehelyett az útvonaltáblák egy virtuális hálózat alhálózatához vannak társítva.

A virtuális hálózat és az útvonaltábla együtt kell léteznie az Azure-hely és előfizetés.

Az alhálózatok és az útvonaltáblák egy-a-többhöz kapcsolattal rendelkeznek. Az alhálózat definiálható társított útvonaltábla vagy egy útvonaltábla nélkül. Az útvonaltáblák nem, egy vagy több alhálózathoz társíthatók. 

Az alhálózatról érkező forgalom a következők alapján történik:

- az útvonaltáblákban definiált útvonalak
- [alapértelmezett útvonalak](/azure/virtual-network/virtual-networks-udr-overview#default)
- helyszíni hálózatról propagált útvonalak

A virtuális hálózatot egy Azure virtuális hálózati átjáróhoz kell csatlakoztatni. Az átjáró lehet ExpressRoute, vagy VPN, ha a BGP-t VPN-átjáróval használja.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Útvonaltábla szétválasztása alhálózatról

Ebben a szakaszban a forgatókönyv-kód elhatárolta az útvonaltáblát egy alhálózattól.

Amikor egy útvonaltáblát elválasztott egy alhálózatról, `route_table` állítsa az alhálózatot `None`. 

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Útvonal létrehozása

A forgatókönyv-kód ebben a szakaszban egy útvonalat egy útvonaltábla. 

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:

* `virtual_network_gateway`definíciója a `next_hop_type`. Az Útvonalak Azure általi kiválasztásáról az [Útválasztás áttekintése című témakörben olvashat bővebben.](/azure/virtual-network/virtual-networks-udr-overview)
* `address_prefix`definíciója a `10.1.0.0/16`. Az előtag nem kettőzhető az útvonaltáblán belül.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Útvonal törlése

Ebben a szakaszban a forgatókönyv-kód egy útvonalat töröl egy útvonaltáblából.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-route-table-information"></a>Útvonaltábla adatainak beszereznie

Ebben a szakaszban a forgatókönyv-kód `azure_rm_routetable_facts` az Ansible modult használja az útvonaltábla adatainak lekéréséhez.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Útvonaltábla törlése

A forgatókönyv kód ebben a szakaszban egy útvonaltábla.

Az útvonaltábla törlésekor az összes útvonal is törlődik.

Az útvonaltábla nem törölhető, ha alhálózathoz van társítva. [Az útvonaltábla elválasztása az alhálózatoktól,](#dissociate-a-route-table-from-a-subnet) mielőtt megpróbálna törölni az útvonaltáblát. 

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)