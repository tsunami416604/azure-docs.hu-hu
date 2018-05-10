---
title: Alapszintű Linux virtuális gép létrehozása az Azure-ban Ansible használatával |} Microsoft Docs
description: Ansible létrehozása és kezelése az Azure-ban alapvető Linux virtuális gépek használata
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: a2bf047d5a08bfd3df6a6c76116d2b9b9ab81fad
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Alapszintű virtuális gép létrehozása az Azure-ban Ansible
Ansible lehetővé teszi, hogy automatizálja a központi telepítési és konfigurációs az erőforrásoknak a környezetben. Ansible segítségével kezelheti a virtuális gépek (VM), ugyanaz, mint bármely egyéb erőforrásokat az Azure-ban. Ez a cikk bemutatja, hogyan hozzon létre egy egyszerű virtuális gép Ansible. Azt is megtudhatja hogyan [hozzon létre egy teljes körű Virtuálisgép-környezetet Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Előfeltételek
Ansible az Azure-erőforrások kezeléséhez, a következőkre lesz szüksége:

- Ansible és a gazdagép a rendszerre telepített Azure Python SDK-moduljai.
    - Ansible telepíthető [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), és [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Az Azure hitelesítő adatait, és Ansible konfigurált is használhatja őket.
    - [Az Azure hitelesítő adatok létrehozása és Ansible konfigurálása](ansible-install-configure.md#create-azure-credentials)
- Az Azure CLI 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. 
    - Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). Is [felhő rendszerhéj](/azure/cloud-shell/quickstart) a böngészőből.


## <a name="create-supporting-azure-resources"></a>Hozzon létre az Azure-erőforrások támogatása
Ebben a példában hoz létre, amely egy virtuális Gépet telepít egy meglévő infrastruktúra a runbookot. Először hozza létre az erőforráscsoport [az csoport létrehozása](/cli/azure/vm#az_vm_create). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális hálózatot a virtuális gép a [az hálózati vnet létrehozása](/cli/azure/network/vnet#az_network_vnet_create). Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVnet* és nevű alhálózat *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Hozzon létre és futtasson Ansible forgatókönyv
Hozzon létre egy Ansible alkalmazástervezési nevű *azure_create_vm.yml* , majd illessze be az alábbiakat. Ez a példa hoz létre egy virtuális, és konfigurálja az SSH hitelesítő adatokat. Adja meg a saját teljes nyilvános kulcs adatai a *key_data* párosítsa a következőképpen:

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
        sku: '7.3'
        version: latest
```

Ansible a virtuális gép létrehozásához futtassa a forgatókönyv az alábbiak szerint:

```bash
ansible-playbook azure_create_vm.yml
```

A kimeneti hasonlít-e a következő példa bemutatja, hogy a virtuális gép létrehozása sikeresen megtörtént:

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
Ebben a példában egy virtuális Gépet hoz létre, egy meglévő erőforráscsoportot és egy virtuális hálózattal, már üzembe van helyezve. Részletes példa támogató erőforrások, például a virtuális hálózat és a hálózati biztonsági csoportszabályok létrehozásához Ansible használatával, lásd: [hozzon létre egy teljes körű Virtuálisgép-környezetet Ansible](ansible-create-complete-vm.md).
