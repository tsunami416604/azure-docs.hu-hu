---
title: Rövid útmutató – Linux rendszerű virtuális gépek kezelése az Azure-ban az Ansible használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a Linux rendszerű virtuális gépeket az Azure-ban a Ansible használatával
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: fb6c4c7c6f90aa4fb9ac566433c62690e51db20d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037031"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Gyors útmutató: Linux rendszerű virtuális gépek kezelése az Azure-ban a Ansible használatával

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ebben a cikkben egy Linux rendszerű virtuális gép elindítására és leállítására szolgáló Ansible használ. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Virtuális gép leállítása

Ebben a szakaszban egy Azure-beli virtuális gép felszabadítására (leállítására) vonatkozó Ansible használ.

1. Bejelentkezés az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy `azure-vm-stop.yml`nevű fájlt, és nyissa meg a szerkesztőben:

    ```azurecli-interactive
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

1. Cserélje le a `{{ resource_group_name }}`t, és `{{ vm_name }}` helyőrzőket az értékekkel.

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

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

## <a name="start-a-virtual-machine"></a>Virtuális gép indítása

Ebben a szakaszban a Ansible használatával elindít egy delefoglalt (leállított) Azure-beli virtuális gépet.

1. Bejelentkezés az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy `azure-vm-start.yml`nevű fájlt, és nyissa meg a szerkesztőben:

    ```azurecli-interactive
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

1. Cserélje le a `{{ resource_group_name }}`t, és `{{ vm_name }}` helyőrzőket az értékekkel.

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Azure dinamikus leltározások kezelése a Ansible használatával](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)