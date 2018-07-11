---
title: Teljes Linux rendszerű virtuális gép létrehozása az Azure-ban az Ansible használatával |} A Microsoft Docs
description: Ismerje meg, az Ansible használatával hozhat létre és kezelhet Linuxos virtuális gép teljes környezet az Azure-ban
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 63228f8bf8729f1bf3796a77516490ae7088d5ed
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930844"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Linux rendszerű virtuális gép teljes környezet létrehozása az Azure-ban, az ansible segítségével
Az Ansible segítségével automatizálhatja a telepítését és konfigurálását az erőforrásoknak a környezetben. Az Ansible segítségével kezelheti a virtuális gépek (VM) az Azure-ban, ugyanaz, mint bármely más erőforrást. Ez a cikk bemutatja, hogyan hozhat létre teljes Linux-környezet és a támogató erőforrások az ansible segítségével. Azt is megtudhatja, hogyan [alapszintű virtuális gép létrehozása az ansible segítségével](ansible-create-vm.md).


## <a name="prerequisites"></a>Előfeltételek
Ansible az Azure-erőforrások kezeléséhez, a következőkre lesz szüksége:

- Az Ansible és a gazdagép a rendszerre telepített Azure Python SDK-modulokat.
    - Az Ansible telepítése [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), és [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Azure hitelesítő adatait, és Ansible azok használatára konfigurálva.
    - [Hozza létre az Azure hitelesítő adatokat, és az Ansible konfigurálása](ansible-install-configure.md#create-azure-credentials)
- Az Azure CLI 2.0.4-es vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. 
    - Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). Is [Cloud Shell](/azure/cloud-shell/quickstart) a böngészőben.


## <a name="create-virtual-network"></a>Virtuális hálózat létrehozása
Most minden szakasza Ansible-forgatókönyvek tekintse meg, és az egyes Azure-erőforrások létrehozásához. Lásd: a teljes forgatókönyv [a cikk ezen szakasza](#complete-ansible-playbook).

A következő szakaszt az Ansible-forgatókönyvek létrehoz egy virtuális hálózatot nevű *myVnet* a a *10.0.0.0/16* címtér:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Adjon hozzá egy alhálózatot, hogy a következő rész alhálózatot hoz létre nevű *mySubnet* a a *myVnet* virtuális hálózat:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Nyilvános IP-cím létrehozása
Erőforrások eléréséhez az interneten keresztül, hozzon létre, és egy nyilvános IP-cím hozzárendelése a virtuális gép. A következő szakaszt az Ansible-forgatókönyvek létrehoz egy nyilvános IP-címet *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hálózati biztonsági csoportokkal szabályozhatja a hálózati forgalmat a virtuális gép adataikkal. A következő szakaszt az Ansible-forgatókönyvek nevű hálózati biztonsági csoportot hoz létre *myNetworkSecurityGroup* és a egy szabályt, amely engedélyezi az SSH-forgalmat a 22-es TCP-portot határozza meg:

```yaml
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
```


## <a name="create-virtual-network-interface-card"></a>Virtuális hálózati kártya létrehozása
Egy virtuális hálózati kártya (NIC) egy adott virtuális hálózaton, a nyilvános IP-cím és a hálózati biztonsági csoportot a virtuális Géphez csatlakozik. A következő szakaszt az Ansible-forgatókönyvek létrehoz egy virtuális hálózati Adaptert *myNIC* csatlakozik a létrehozott virtuális hálózati erőforrások:

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
Az utolsó lépés, hogy hozzon létre egy virtuális Gépet, és használni létrehozott összes erőforrást. A következő szakaszt az Ansible-forgatókönyvek a nevű virtuális Gépet hoz létre *myVM* , és csatolja a virtuális hálózati Adaptert *myNIC*. Adja meg a saját teljes nyilvános kulcsadatokat az a *key_data* párosítsa az alábbiak szerint:

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
      sku: '7.5'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Végezze el az Ansible-forgatókönyvek
Ezekben a szakaszokban összegyűjthetők, hozzon létre az Ansible-forgatókönyvek nevű *azure_create_complete_vm.yml* , és illessze be a következő tartalmakat. Adja meg a saját teljes nyilvános kulcsadatokat az a *key_data* párt:

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
        sku: '7.5'
        version: latest
```

Az Ansible szüksége van egy erőforráscsoportot, amelybe be az erőforrások üzembe helyezése. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

A teljes virtuális környezet létrehozása az ansible segítségével, a következőképpen futtassa a forgatókönyvet:

```bash
ansible-playbook azure_create_complete_vm.yml
```

A kimenet a következő példa bemutatja, hogy a virtuális gép sikeresen létrejött hasonlóan néz ki:

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

## <a name="next-steps"></a>További lépések
Ez a példa létrehoz egy teljes virtuális gépből álló környezeteket, beleértve a szükséges virtuális hálózati erőforrások. Virtuális gép létrehozása az alapértelmezett beállítások a meglévő hálózati erőforrások közvetlenebb példa: [hozzon létre egy virtuális Gépet](ansible-create-vm.md).
