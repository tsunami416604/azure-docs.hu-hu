---
title: Linux rendszerű virtuális gép felügyelete az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan felügyelhet Linux rendszerű virtuális gépeket az Azure-ban az Ansible használatával
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250677"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Linux rendszerű virtuális gép felügyelete az Azure-ban az Ansible használatával
Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible segítségével ugyanúgy felügyelheti az Azure-beli virtuális gépeket, ahogy azt bármely más erőforrással tenné. Ez a cikk bemutatja, hogyan használhatja az Ansible-forgatókönyveket Linux rendszerű virtuális gépek indítására és leállítására. 

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Az Azure Cloud Shell konfigurálása** vagy **az Ansible telepítése és konfigurálása Linux rendszerű virtuális gépen**

  **Az Azure Cloud Shell konfigurálása**

  1. **Az Azure Cloud Shell konfigurálása** – Ha még csak most ismerkedik az Azure Cloud Shell használatával, [a Bash az Azure Cloud Shellben való használatát ismertető rövid útmutatóból](/azure/cloud-shell/quickstart) megtudhatja, hogy indíthatja el és konfigurálhatja a Cloud Shellt. 

  1. **Linux rendszerű virtuális gép** – Ha nem rendelkezik Linux rendszerű virtuális géppel, [létrehozhat egy virtuális gépet az Ansible használatával](ansible-create-vm.md).

  **– VAGY –**

  **Az Ansible telepítése és konfigurálása Linux rendszerű virtuális gépen**

  1. **Az Ansible telepítése** – Telepítse az Ansible-t egy [támogatott Linux-platformon](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Az Ansible konfigurálása** - [Azure-beli hitelesítő adatok létrehozása és az Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Azure-beli virtuális gép felszabadítása (leállítása) az Ansible használatával
Ez a szakasz bemutatja, hogyan szabadíthat fel (állíthat le) Azure-beli virtuális gépeket az Ansible használatával

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy fájlt `azure_vm_stop.yml` néven (amely a forgatókönyvet tartalmazza majd), és nyissa meg a VI-szerkesztőben a következők szerint:

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Az **I** billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódmintát a szerkesztőbe:

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

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1. Futtassa az Ansible-mintaforgatókönyvet.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. A kimenet a következő példához hasonló, amely egy virtuális gép sikeres felszabadítását (leállítását) mutatja be:

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

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy fájlt `azure_vm_start.yml` néven (amely a forgatókönyvet tartalmazza majd), és nyissa meg a VI-szerkesztőben a következők szerint:

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Az **I** billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódmintát a szerkesztőbe:

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

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1. Futtassa az Ansible-mintaforgatókönyvet.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. A kimenet a következő példához hasonló, amely egy virtuális gép sikeres indítását mutatja be:

    A kimenet a következő példához hasonló, amely egy virtuális gép sikeres indítását mutatja be:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Az Azure-beli dinamikus leltárak kezelése az Ansible használatával](../../ansible/ansible-manage-azure-dynamic-inventories.md)