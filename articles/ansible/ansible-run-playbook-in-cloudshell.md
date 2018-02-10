---
title: "Futtassa a Ansible Bash Azure felhőben rendszerhéj"
description: "Megtudhatja, hogyan végezhető különböző Ansible Bash Azure Cloud rendszerhéj"
ms.service: ansible
keywords: "ansible, az azure, a devops, a bash, a cloudshell, a forgatókönyv, a bash"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 0cd0390a381e85c8f047960ce06c581a433d4a2c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Futtassa a Ansible Bash Azure felhőben rendszerhéj

Ebben az oktatóanyagban elsajátíthatja a felhő rendszerhéj Bash Ansible különböző feladatok elvégzéséhez. Ezek a feladatok közé tartozik egy virtuális géphez való kapcsolódás és Ansible playbooks létrehozása és törlése az Azure-erőforráscsoport létrehozása.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

- **Azure hitelesítő adataival** - [létrehozása Azure-felhasználó hitelesítő adatait, és Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Azure Cloud rendszerhéj konfigurálása** – Ha még nem ismeri a Azure Cloud rendszerhéj, a cikk [gyors üzembe helyezés az Azure felhőalapú rendszerhéj Bash](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart) azt ábrázolja, hogyan indítani és konfigurálni a felhő rendszerhéj. Egy külön webhelyen itt felhő rendszerhéj elindításához:

[![Indítsa el a felhő rendszerhéjat](https://shell.azure.com/images/launchcloudshell.png "indítsa el a felhő rendszerhéjat")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Csatlakoztassa a virtuális Gépet Ansible segítségével
Ansible hozott létre nevű Python-parancsfájl [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) azáltal, hogy az API-kérelmek az Azure erőforrás-kezelő létrehozó Azure-erőforrások dinamikus készlet. A következő lépések végigvezetik használatával a `azure_rm.py` parancsfájl egy Azure virtuális géphez való csatlakozáshoz:

1. Nyissa meg a felhő rendszerhéj Bash. A felhő rendszerhéj ablak bal oldalán rendszerhéj típus helyén.

1. Ha nem rendelkezik a virtuális gép használja, adja meg a következő parancsokat a felhő felület, amellyel tesztelheti a virtuális gép létrehozásához:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Használja a GNU `wget` parancs beolvasása a `azure_rm.py` parancsfájlt:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Használja a `chmod` parancs futtatásával módosíthatja a hozzáférési engedélyeket a `azure_rm.py` parancsfájl. A következő parancsot használja a `+x` paraméter lehetővé végrehajtásából (fut) a megadott fájl (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Használja a [ansible parancs](https://docs.ansible.com/ansible/2.4/ansible.html) a virtuális géphez történő csatlakozáshoz: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  A csatlakozás után eredmények hasonló, a kimenetnek kell megjelennie:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Ha ez a szakasz egy erőforráscsoport és a virtuális gép létrehozása

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Forgatókönyv futtatása a Cloud Shellben
A [ansible-forgatókönyv](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) parancs végrehajtása során Ansible playbooks, a feladatok futó célzott gazdagépe (ke). Ez a szakasz bemutatja, hogyan hozhatnak létre és két playbooks - érdekében hozzon létre egy erőforráscsoportot, és egy második, a csoport törléséhez egy futtathatnak a felhőalapú rendszerhéjának használatával. 

1. Hozzon létre egy fájlt `rg.yml` az alábbiak szerint:

  ```azurecli-interactive
  vi rg.yml
  ```

1. A következő tartalom másolása a felhő rendszerhéj ablakba (ilyenkor a VI szerkesztő példányának):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          location: eastus
  ```

1. Mentse a fájlt, és zárja be a VI szerkesztőt megadásával `:wq` gomb lenyomásával &lt;Enter >.

1. Használja a `ansible-playbook` parancsot kell futtatni a `rg.yml` forgatókönyv:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. A következő kimeneti hasonló eredményeket kell megjelennie:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. A telepítés ellenőrzése:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Most, hogy létrehozta az erőforráscsoportot, hozzon létre egy második Ansible alkalmazástervezési a csoport törléséhez:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. A következő tartalom másolása a felhő rendszerhéj ablakba (ilyenkor a VI szerkesztő példányának):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          state: absent
  ```

1. Mentse a fájlt, és zárja be a VI szerkesztőt megadásával `:wq` gomb lenyomásával &lt;Enter >.

1. Használja a `ansible-playbook` parancsot kell futtatni a `rg2.yml` forgatókönyv:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. A következő kimeneti hasonló eredményeket kell megjelennie:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Alapszintű virtuális gép létrehozása az Azure-ban Ansible](/azure/virtual-machines/linux/ansible-create-vm)
