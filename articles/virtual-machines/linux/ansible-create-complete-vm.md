---
title: "Ansible használatával teljes Linux virtuális gép létrehozása az Azure-ban |} Microsoft Docs"
description: "Ansible használata létrehozását és kezelését a teljes Linux virtuálisgép-környezetet az Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: f5dfecd1f5aafc1b7117433ef1bdb1805be2c57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Hozzon létre egy teljes Linux virtuális gép környezetet az Azure-ban Ansible
Ansible lehetővé teszi, hogy automatizálja a központi telepítési és konfigurációs az erőforrásoknak a környezetben. Ansible segítségével kezelheti a virtuális gépek (VM), ugyanaz, mint bármely egyéb erőforrásokat az Azure-ban. Ez a cikk bemutatja, hogyan hozzon létre egy teljes körű Linux környezetet és az azt támogató Ansible erőforrásokhoz. Azt is megtudhatja hogyan [hozzon létre egy egyszerű virtuális gép Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Előfeltételek
Ansible az Azure-erőforrások kezeléséhez, a következőkre lesz szüksége:

- Ansible és a gazdagép a rendszerre telepített Azure Python SDK-moduljai.
    - Ansible telepíthető [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73), és [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2)
- Az Azure hitelesítő adatait, és Ansible konfigurált is használhatja őket.
    - [Az Azure hitelesítő adatok létrehozása és Ansible konfigurálása](ansible-install-configure.md#create-azure-credentials)
- Az Azure CLI 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. 
    - Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). Is [felhő rendszerhéj](/azure/cloud-shell/quickstart) a böngészőből.


## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Egy Ansible forgatókönyv a következő szakasz létrehoz egy virtuális hálózatot nevű *myVnet* a a *10.0.0.0/16* címtér:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Adjon hozzá egy alhálózatot, az alábbi szakasz hoz nevű alhálózat *mySubnet* a a *myVnet* virtuális hálózat:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása
Internetes erőforrások eléréséhez hozzon létre, és egy nyilvános IP-címet rendel a virtuális Gépet. A következő szakasz egy Ansible alkalmazástervezési hoz létre egy nyilvános IP-cím nevű *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hálózati biztonsági csoportok szabályozhatja a hálózati forgalmat a virtuális Gépet mindkét. A következő szakasz egy Ansible alkalmazástervezési hoz létre a hálózati biztonsági csoport nevű *myNetworkSecurityGroup* és a szabály az SSH-forgalmat engedélyezi a 22-es TCP-portot határozza meg:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Hozzon létre a virtuális hálózati kártya
A virtuális hálózati kártya (NIC) a virtuális gép csatlakozik egy adott virtuális hálózaton, a nyilvános IP-cím és a hálózati biztonsági csoport. A következő szakasz egy Ansible alkalmazástervezési hoz létre a virtuális hálózati adapter nevű *myNIC* csatlakozik a létrehozott virtuális hálózati erőforrások:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Az utolsó lépés a virtuális gép létrehozása és használata a létrehozott összes erőforrást. A következő szakasz egy Ansible alkalmazástervezési hoz létre egy elnevezett VM *myVM* , és csatolja a virtuális hálózati adapter nevű *myNIC*. Adja meg a saját nyilvános kulcs adatai a *key_data* párosítsa a következőképpen:

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Teljes Ansible forgatókönyv
Ezek a szakaszok egyesítik, hozzon létre egy Ansible alkalmazástervezési nevű *azure_create_complete_vm.yml* , majd illessze be az alábbiakat:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible kell azokat az erőforrások telepítése egy erőforráscsoportot. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/vm#create) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *eastus* helye:

```azurecli
az group create --name myResourceGroup --location eastus
```

A teljes méretű környezet Ansible létrehozásához, futtassa a forgatókönyv az alábbiak szerint:

```bash
ansible-playbook azure_create_complete_vm.yml
```

A kimeneti hasonlít-e a következő példa bemutatja, hogy a virtuális gép létrehozása sikeresen megtörtént:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Következő lépések
Ez a példa többek között a szükséges virtuális hálózati erőforrások teljes körű Virtuálisgép-környezetet hoz létre. Például egy közvetlenül a virtuális gép létrehozása a meglévő hálózati erőforrások alapértelmezett beállításokkal, tekintse meg a [hozzon létre egy virtuális Gépet](ansible-create-vm.md).