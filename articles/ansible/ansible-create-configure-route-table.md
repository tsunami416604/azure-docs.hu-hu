---
title: Létrehozása, módosítása vagy törlése az Azure útválasztási táblázatot az Ansible-lel
description: Ismerje meg, hogyan Ansible használatával létrehozása, módosítása vagy törlése egy útválasztási táblázatot az Ansible-lel
ms.service: ansible
keywords: az ansible, azure, devops, bash, forgatókönyv, hálózatkezelés, útvonal, útválasztási táblázat
author: tomarchermsft
manager: jeconnoc
ms.author: yuwzho, kyliel
ms.topic: tutorial
ms.date: 12/17/2018
ms.openlocfilehash: ab338183590a2ff301b167d17c3300c4aaacce01
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051584"
---
# <a name="create-change-or-delete-an-azure-route-table-using-ansible"></a>Létrehozása, módosítása vagy törlése az Azure útválasztási táblázatot az Ansible-lel
Az Azure automatikusan irányítja az Azure az alhálózatok, virtuális hálózatok közötti adatforgalom és a helyszíni hálózatokkal. Az Azure alapértelmezett útválasztását módosítani szeretné, ha mégis így hozzon létre egy [útvonaltábla](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ez a cikk bemutatja, hogyan létrehozása, módosítása vagy törlése az Azure útválasztási táblázatban és csatolása az útválasztási táblázatot, valamint olyan alhálózathoz. 

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az Ansible 2.7 ebben az oktatóanyagban az alábbi minta-forgatókönyvek futtatása szükséges.

## <a name="create-a-route-table"></a>Útválasztási táblázat létrehozása
Ez a szakasz bemutatja egy minta az Ansible-forgatókönyv, amely létrehoz egy útvonaltáblát. Létrehozhat egy Azure-helyen és az előfizetés hány útvonaltábláihoz korlátozva van. További részletek: [Az Azure korlátai](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

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

Mentse a forgatókönyvet, `route_table_create.yml`. A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_table_create.yml
```

## <a name="associate-a-route-table-to-a-subnet"></a>Útválasztási táblázat társítása alhálózattal
Egy alhálózat rendelkezhet, nulla vagy egy útvonaltábla társítható. Egy útválasztási táblázatot lehet nulla vagy egynél több alhálózat van társítva. Az útvonaltáblák nem lettek társítva a virtuális hálózatok, mivel társítania kell egy útválasztási táblázatot az egyes alhálózatokon azt szeretné, hogy a társított útvonaltáblába. Minden, az alhálózatot elhagyó forgalom útválasztási táblázatokat, belül létrehozott útvonalak alapján továbbítja [alapértelmezett útvonalakat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#default), és útvonalak propagálni egy a helyszíni hálózathoz, ha a virtuális hálózathoz csatlakoztatva van egy Azure virtuális hálózati átjáró () ExpressRoute vagy VPN-, VPN gateway a BGP használatakor). Csak egy útválasztási táblázatot a megegyező Azure-helyen és előfizetésen az útvonaltáblában lévő virtuális hálózatok alhálózataihoz társíthatja.

Ez a szakasz bemutatja egy minta Ansible-forgatókönyvek, amely létrehoz egy virtuális hálózat és a egy küldés, majd hozzárendeli egy útválasztási táblázatot az alhálózathoz.

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

Mentse a forgatókönyvet, `route_table_associate.yml`. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_table_associate.yml
```

## <a name="dissociate-a-route-table-from-a-subnet"></a>Egy alhálózatról egy útválasztási táblázatot társításának megszüntetése
Egy alhálózatról egy útválasztási táblázatot, leválasztja, ha egyszerűen állítsa be a `route_table` egy alhálózat `None`. Alul látható egy minta az ansible-forgatókönyvek. 

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

Mentse a forgatókönyvet, `route_table_dissociate.yml`. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_table_dissociate.yml
```

## <a name="create-a-route"></a>Útvonal létrehozása
Ez a szakasz mutatja be egy minta az Ansible-forgatókönyv, amely alatt az útválasztási táblázatot egy útvonalat hoz létre. Azt határozza meg `virtual_network_gateway` , `next_hop_type` és `10.1.0.0/16` , `address_prefix`. Az előtag nem duplikálható az útvonaltáblában lévő egynél több útvonal ellenére, hogy az előtag lehet egy másik előtag belül. Hogyan választja ki az Azure a útvonalak és a egy részletes leírását az összes következő ugrási típusokat kapcsolatos további információkért lásd: [Útválasztás áttekintése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

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
Mentse a forgatókönyvet, `route_create.yml`. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_create.yml
```

## <a name="delete-a-route"></a>Útvonal törlése
Ez a szakasz mutatja be egy minta az Ansible-forgatókönyv, amely egy útvonal töröl egy útválasztási táblázatot.

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

Mentse a forgatókönyvet, `route_delete.yml`. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_delete.yml
```

## <a name="get-information-of-a-route-table"></a>Útválasztási táblázat adatainak
Az Ansible modul keresztül egy route_table részleteit is megtekintheti `azure_rm_routetable_facts`. A tények modul az összes útvonal csatlakozik az útvonaltábla vonatkozó információkat adja vissza.
Alul látható egy minta az ansible-forgatókönyvek. 

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

Mentse a forgatókönyvet, `route_table_facts.yml`. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_table_facts.yml
```

## <a name="delete-a-route-table"></a>Útválasztási táblázat törlése
Ha egy útválasztási táblázatot is tartozik, az esetleges olyan alhálózatokra, nem lehet törölni. [Leválasztja](#dissociate-a-route-table-from-a-subnet) egy útválasztási táblázatot az összes alhálózatot, mielőtt megkísérelné törölni.

Az útvonaltábla együtt az összes útvonal törölheti. Alul látható egy minta az ansible-forgatókönyvek. 

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

Mentse a forgatókönyvet, `route_table_delete.yml`. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook route_table_delete.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](https://docs.microsoft.com/azure/ansible/)