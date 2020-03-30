---
title: Rövid útmutató – Linuxos virtuális gépek kezelése az Azure-ban az Ansible használatával
description: Ebben a rövid útmutatóban megtudhatja, hogyan kezelheti a Linux virtuális gépeket az Azure-ban az Ansible használatával
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239535"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Rövid útmutató: Linuxos virtuális gépek kezelése az Azure-ban az Ansible használatával

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ebben a cikkben egy Ansible forgatókönyv segítségével indítsa el és állítsa le a Linux virtuális gép. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Virtuális gép leállítása

Ebben a szakaszban az Ansible használatával felszabadíthatja (leállítja) az Azure virtuális gépet.

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre `azure-vm-stop.yml`egy nevű fájlt, és nyissa meg a szerkesztőben:

    ```bash
    code azure-vm-stop.yml
    ```

1. Másolja az alábbi kódmintát a szerkesztőbe:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Cserélje `{{ resource_group_name }}` le `{{ vm_name }}` a és a helyőrzőket az ön értékeire.

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Virtuális gép elindítása

Ebben a szakaszban az Ansible használatával indíthat el egy felszabadított (leállított) Azure virtuális gépet.

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre `azure-vm-start.yml`egy nevű fájlt, és nyissa meg a szerkesztőben:

    ```bash
    code azure-vm-start.yml
    ```

1. Másolja az alábbi kódmintát a szerkesztőbe:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Cserélje `{{ resource_group_name }}` le `{{ vm_name }}` a és a helyőrzőket az ön értékeire.

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Az Azure dinamikus készleteinek kezelése az Ansible használatával](./ansible-manage-azure-dynamic-inventories.md)