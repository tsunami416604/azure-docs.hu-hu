---
title: Rövid útmutató – Linux rendszerű virtuális gépek az Azure-ban az Ansible kezelése |} A Microsoft Docs
description: Ebből a gyorsútmutatóból megtudhatja, hogyan az Ansible segítségével Azure-beli Linuxos virtuális gépek kezelése
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: cab179980f6093bf259556fd690e55c99a817c79
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717548"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Gyors útmutató: Linux rendszerű virtuális gépek az Azure-ban az Ansible kezelése

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ebben a cikkben Ansible-forgatókönyvek elindíthatja és leállíthatja a Linux rendszerű virtuális gép használja. 

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Virtuális gép leállítása

Ebben a szakaszban használhatja az Ansible (stop) Azure-beli virtuális gép felszabadítása.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy fájlt `azure-vm-stop.yml`, és nyissa meg a szerkesztőben:

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

1. Cserélje le a `{{ resource_group_name }}` és `{{ vm_name }}` helyőrzőket a saját értékeit.

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>A virtuális gép elindítása

Ebben a szakaszban használhatja az Ansible deallocated (Leállítva) az Azure virtuális gép elindításához.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy fájlt `azure-vm-start.yml`, és nyissa meg a szerkesztőben:

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

1. Cserélje le a `{{ resource_group_name }}` és `{{ vm_name }}` helyőrzőket a saját értékeit.

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

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
> [Oktatóanyag: Az Ansible-lel az Azure dinamikus leltárak kezelése](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)