---
title: Az Ansible használatával egy egyszerű Linux virtuális gép létrehozása az Azure-ban |} A Microsoft Docs
description: Ismerje meg, az Ansible használatával hozhat létre és kezelhet az alapszintű Linux rendszerű virtuális gép az Azure-ban
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
ms.openlocfilehash: 35dfe8348718e0edf8683f7eeddf286831697d89
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931429"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Alapszintű virtuális gép létrehozása az Azure-ban, az ansible segítségével
Az Ansible segítségével automatizálhatja a telepítését és konfigurálását az erőforrásoknak a környezetben. Az Ansible segítségével kezelheti a virtuális gépek (VM) az Azure-ban, ugyanaz, mint bármely más erőforrást. Ez a cikk bemutatja, hogyan hozhat létre egy egyszerű virtuális Gépet az ansible segítségével. Azt is megtudhatja, hogyan [virtuális gép teljes környezet létrehozása az ansible segítségével](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Előfeltételek
Ansible az Azure-erőforrások kezeléséhez, a következőkre lesz szüksége:

- Az Ansible és a gazdagép a rendszerre telepített Azure Python SDK-modulokat.
    - Az Ansible telepítése [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), és [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Azure hitelesítő adatait, és Ansible azok használatára konfigurálva.
    - [Hozza létre az Azure hitelesítő adatokat, és az Ansible konfigurálása](ansible-install-configure.md#create-azure-credentials)
- Az Azure CLI 2.0.4-es vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. 
    - Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). Is használhatja a [Azure Cloud Shell](/azure/cloud-shell/quickstart) a webböngészőből.


## <a name="create-supporting-azure-resources"></a>Támogató Azure-erőforrások létrehozása
Ebben a példában egy runbookot, amely üzembe helyezi a virtuális gép egy meglévő infrastruktúra hoz létre. Először is hozzon létre erőforráscsoportot [az csoport létrehozása](/cli/azure/group#az-group-create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Virtuális hálózat létrehozása a virtuális gép [az network vnet létrehozása](/cli/azure/network/vnet#az-network-vnet-create). A következő példában létrehozunk egy nevű virtuális hálózatot *myVnet* és a egy nevű alhálózatot *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Hozzon létre, és Ansible-forgatókönyvek futtatása
Hozzon létre az Ansible-forgatókönyvek nevű *azure_create_vm.yml* , és illessze be a következő tartalmakat. Ebben a példában egy egyetlen virtuális Gépet hoz létre, és konfigurálja az SSH hitelesítő adatokat. Adja meg a saját teljes nyilvános kulcsadatokat az a *key_data* párosítsa az alábbiak szerint:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

A virtuális gép létrehozása az ansible segítségével, a következőképpen futtassa a forgatókönyvet:

```bash
ansible-playbook azure_create_vm.yml
```

A kimenet a következő példa bemutatja, hogy a virtuális gép sikeresen létrejött hasonlóan néz ki:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>További lépések
Ez a példa létrehoz egy virtuális gép egy meglévő erőforráscsoportot, és a egy már üzembe helyezte a virtuális hálózathoz. Az Ansible használatával hozhat létre a támogató erőforrások, például egy virtuális hálózatot és hálózati biztonsági csoportszabályok részletes példa: [virtuális gép teljes környezet létrehozása az ansible segítségével](ansible-create-complete-vm.md).
