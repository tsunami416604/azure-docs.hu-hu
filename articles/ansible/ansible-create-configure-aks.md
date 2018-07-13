---
title: Létrehozása és konfigurálása az Azure Kubernetes Service-fürtök az Azure-ban az Ansible-lel
description: Ismerje meg, az Ansible használatával hozhat létre és kezelhet az Azure Kubernetes Service-fürt az Azure-ban
ms.service: ansible
keywords: az ansible, azure, devops, bash, cloud Shell, forgatókönyv, az aks, tároló, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009202"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Létrehozása és konfigurálása az Azure Kubernetes Service-fürtök az Azure-ban az Ansible-lel
Az Ansible segítségével automatizálhatja a telepítését és konfigurálását az erőforrásoknak a környezetben. Az Ansible segítségével kezelheti az Azure Kubernetes Service (AKS). Ez a cikk bemutatja, hogyan Ansible használatával létrehozása és konfigurálása az Azure Kubernetes Service-fürt.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.
- **Az Ansible konfigurálása** - [létrehozása Azure hitelesítő adatait, és az Ansible konfigurálása](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Az Ansible és az Azure Python SDK-modulok** 
  - [7.4 centOS](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Az Azure szolgáltatás egyszerű** – [az egyszerű szolgáltatás létrehozása](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), vegye figyelembe a következő értékeket: **appId**, **displayName**, **jelszó** , és **bérlői**.

> [!Note]
> Az Ansible 2.6 futtassa a következő szükséges ebben az oktatóanyagban a mintául szolgáló forgatókönyvek. 

## <a name="create-a-managed-aks-cluster"></a>Egy felügyelt AKS-fürt létrehozása
A következő minta Ansible-forgatókönyvek hoz létre egy erőforráscsoportot, és a egy AKS-fürtöt az erőforráscsoportban található:

  ```yaml
  - name: Create Azure Kubernetes Service
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      location: eastus
      aks_name: myAKSCluster
      username: azureuser
      ssh_key: "your_ssh_key"
      client_id: "your_client_id"
      client_secret: "your_client_secret"
    tasks:
    - name: Create resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create a managed Azure Container Services (AKS) cluster
      azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 2
            vm_size: Standard_D2_v2
        tags:
          Environment: Production
  ```

A következő felsorolás segíti a fenti Ansible-forgatókönyvek kód ismertetik:
- Az első szakaszban belül **feladatok** nevű erőforráscsoport meghatározása **myResourceGroup** belül a **eastus** helyét. 
- A második szakasz belül **feladatok** határozza meg az AKS-fürt nevű **myAKSCluster** belül a **myResourceGroup** erőforráscsoportot. 

Az AKS-fürt létrehozásához az ansible segítségével mentse az előző példa forgatókönyv, `azure_create_aks.yml`, és a forgatókönyv futtatása a következő paranccsal:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

A kimenete a **ansible-forgatókönyvek* parancs a következő megjelenítése, hogy az AKS-fürtöt sikeresen létrejött-e hasonlóan néz ki:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>AKS-csomópontok skálázása

A minta-forgatókönyvet az előző szakaszban két csomópont határozza meg. Ha a fürtön kevesebb vagy több tárolók számítási feladatait van szüksége, bármikor módosíthatja a csomópontok számát. Ebben a szakaszban a minta forgatókönyv három két csomópontból csomópontok száma növekszik. Módosítsa úgy a csomópontok száma módosítása történik az **száma** értékét a **agent_pool_profiles** letiltása. 

Adja meg a saját `ssh_key`, `client_id`, és `client_secret` a a **service_principal** letiltása:

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"    
        location: "{{ location }}"
        resource_group: "{{ resource_group }}" 
        dns_prefix: "{{ aks_name }}" 
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Az ansible segítségével az Azure Kubernetes Service-fürtök skálázásának, mint a fenti forgatókönyv mentése *azure_configure_aks.yml*, és futtassa a forgatókönyv az alábbiak szerint:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

A következő kimenet látható, hogy az AKS-fürt létrehozása sikerült:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Oktatóanyag: Alkalmazások skálázása az Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)