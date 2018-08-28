---
title: Azure Kubernetes Service-fürtök létrehozása és konfigurálása az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan hozhat létre és felügyelhet Azure Kubernetes Service-fürtöket az Azure-ban az Ansible segítségével.
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, forgatókönyv, aks, tároló, Kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/21/2018
ms.openlocfilehash: de692b29902145e44a055680d662c16ed90c56c2
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617175"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Azure Kubernetes Service-fürtök létrehozása és konfigurálása az Azure-ban az Ansible használatával
Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible-lel felügyelheti az Azure Kubernetes Service-t (AKS-t). Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat Azure Kubernetes Service-fürtöket az Ansible használatával.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- **Azure-szolgáltatásnév** – A [szolgáltatásnév létrehozásakor](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal) jegyezze fel a következő értékeket: **appId**, **displayName**, **password** és **tenant**.

- **Az Azure Cloud Shell konfigurálása** vagy **az Ansible telepítése és konfigurálása Linux rendszerű virtuális gépen**

  **Az Azure Cloud Shell konfigurálása**

  1. **Az Azure Cloud Shell konfigurálása** – Ha még csak most ismerkedik az Azure Cloud Shell használatával, [a Bash az Azure Cloud Shellben való használatát ismertető rövid útmutatóból](/azure/cloud-shell/quickstart) megtudhatja, hogy indíthatja el és konfigurálhatja a Cloud Shellt. 

  **– VAGY –**

  **Az Ansible telepítése és konfigurálása Linux rendszerű virtuális gépen**

  1. **Az Ansible telepítése** – Telepítse az Ansible-t egy [támogatott Linux-platformon](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Az Ansible konfigurálása** - [Azure-beli hitelesítő adatok létrehozása és az Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.6-os verziója szükséges. 

## <a name="create-a-managed-aks-cluster"></a>Felügyelt AKS-fürt létrehozása
A következő Ansible-mintaforgatókönyv egy erőforráscsoportot és abban egy AKS-fürtöt hoz létre:

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

Az alábbiak segítenek az előző Ansible-forgatókönyvkód megértésében:
- A **tasks** első szakasza egy **myResourceGroup** nevű erőforráscsoportot definiál az **eastus** helyen. 
- A **tasks** második szakasza egy **myAKSCluster** nevű AKS-fürtöt definiál a **myResourceGroup** erőforráscsoportban. 

Az AKS-fürt Ansible-lel való létrehozásához mentse az előző mintaforgatókönyvet `azure_create_aks.yml` néven, majd futtassa azt a következő paranccsal:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Az **ansible-playbook* parancs kimenete a következőhöz hasonló, és azt mutatja, hogy az AKS-fürt sikeresen létrejött:

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

Az előző szakaszban lévő mintaforgatókönyv két csomópontot definiál. Ha ennél több vagy kevesebb tárolókhoz kapcsolódó számítási feladatra van szükség a fürtön, a csomópontok számát könnyedén módosíthatja. Az ebben a szakaszban szereplő mintaforgatókönyv kettőről háromra növeli a csomópontok számát. A csomópontok számának módosításához módosítsa a **count** értéket az **agent_pool_profiles** blokkban. 

Adjon meg saját `ssh_key`, `client_id` és `client_secret` értékeket a **service_principal** blokkban:

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

Az Azure Kubernetes Service-fürt Ansible használatával való skálázásához mentse az előző forgatókönyvet *azure_configure_aks.yml* néven, majd futtassa az alábbiak szerint:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Az alábbi kimenet azt mutatja, hogy az AKS-fürt sikeresen létrejött:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Felügyelt AKS-fürt törlése

A következő Ansible-mintaforgatókönyv részlet az AKS-fürtök törlését mutatja be:

  ```yaml
  - name: Delete a managed Azure Container Services (AKS) cluster
    hosts: localhost
    connection: local
    vars:
      resource_group: myResourceGroup
      aks_name: myAKSCluster
    tasks:
    - name: 
      azure_rm_aks:
        name: "{{ aks_name }}"
        resource_group: "{{ resource_group }}"
        state: absent
   ```

Az Azure Kubernetes Service-fürt az Ansible használatával való törléséhez mentse az előző forgatókönyvet *azure_delete_aks.yml* néven, majd futtassa az alábbiak szerint:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

Az alábbi kimenet azt mutatja, hogy az AKS-fürt sikeresen törölve lett:
  ```bash
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
  
## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Oktatóanyag: Alkalmazások méretezése az Azure Kubernetes Service-ben (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
