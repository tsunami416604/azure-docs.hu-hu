---
title: Linux rendszerű virtuális gép felügyelete az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan felügyelhet Linux rendszerű virtuális gépeket az Azure-ban az Ansible használatával
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 09/27/2018
ms.openlocfilehash: ef19043465cb67401786cddec4972f67231995bb
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077810"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gép felügyelete az Azure-ban az Ansible használatával
Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible segítségével ugyanúgy felügyelheti az Azure-beli virtuális gépeket, ahogy azt bármely más erőforrással tenné. Ez a cikk bemutatja, hogyan használhatja az Ansible-forgatókönyveket Linux rendszerű virtuális gépek indítására és leállítására. 

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Azure-beli virtuális gép felszabadítása (leállítása) az Ansible használatával
Ez a szakasz bemutatja, hogyan szabadíthat fel (állíthat le) Azure-beli virtuális gépeket az Ansible használatával

1.  Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1.  Hozzon létre egy fájlt `azure-vm-stop.yml` néven (amely a forgatókönyvet tartalmazza majd), és nyissa meg a VI-szerkesztőben a következők szerint:

    ```azurecli-interactive
    vi azure-vm-stop.yml
    ```

1.  Az **I** billentyű lenyomásával lépjen beszúrási módba.

1.  Másolja az alábbi kódmintát a szerkesztőbe:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
          allocated: no
    ```

1.  A beszúrás módból az **Esc** billentyűvel léphet ki.

1.  Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1.  Futtassa az Ansible-mintaforgatókönyvet.

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1.  A kimenet a következő példához hasonló, amely egy virtuális gép sikeres felszabadítását (leállítását) mutatja be:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Felszabadított (leállított) Azure-beli virtuális gép indítása az Ansible használatával
Ez a szakasz bemutatja, hogy hogyan indíthat el egy felszabadított (leállított) Azure-beli virtuális gépet az Ansible használatával

1.  Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1.  Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1.  Hozzon létre egy fájlt `azure-vm-start.yml` néven (amely a forgatókönyvet tartalmazza majd), és nyissa meg a VI-szerkesztőben a következők szerint:

    ```azurecli-interactive
    vi azure-vm-start.yml
    ```

1.  Az **I** billentyű lenyomásával lépjen beszúrási módba.

1.  Másolja az alábbi kódmintát a szerkesztőbe:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
      - name: Start the virtual machine
        azure_rm_virtualmachine:
          resource_group: myResourceGroup
          name: myVM
    ```

1.  A beszúrás módból az **Esc** billentyűvel léphet ki.

1.  Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1.  Futtassa az Ansible-mintaforgatókönyvet.

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1.  A kimenet a következő példához hasonló, amely egy virtuális gép sikeres indítását mutatja be:

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
> [Az Azure-beli dinamikus leltárak kezelése az Ansible használatával](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)