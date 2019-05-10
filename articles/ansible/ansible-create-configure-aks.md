---
title: Oktatóanyag – Azure Kubernetes Service (AKS)-fürtök konfigurálása az Azure-ban az Ansible-lel |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és felügyelhet Azure Kubernetes Service-fürtöket az Azure-ban az Ansible segítségével.
keywords: az ansible, azure, devops, bash, cloud Shell, forgatókönyv, az aks, tároló, az aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 1467afce60038e086daace72947c1ab21569865a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231324"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Oktatóanyag: Az Azure Kubernetes Service (AKS)-fürtök konfigurálása az Azure-ban az Ansible-lel

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AKS használatához konfigurálhatók [Azure Active Directory (AD)](/azure/active-directory/) a felhasználók hitelesítéséhez. Miután konfigurálta az Azure AD hitelesítési jogkivonat használatával jelentkezzen be az AKS-fürtöt. Az RBAC a felhasználó identitását, vagy a directory-csoporttagság alapulhat.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * AKS-fürt konfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Felügyelt AKS-fürt létrehozása

A minta forgatókönyv létrehoz egy erőforráscsoportot és a egy AKS-fürtöt az erőforráscsoporton belül.

Mentse a következő forgatókönyvet `azure_create_aks.yml` néven:

```yml
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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

- Az első szakaszban belül `tasks` nevű erőforráscsoport meghatározása `myResourceGroup` belül a `eastus` helyét.
- A második szakasz belül `tasks` határozza meg az AKS-fürt nevű `myAKSCluster` belül a `myResourceGroup` erőforráscsoportot.
- Az a `your_ssh_key` helyőrző, adja meg a nyilvános RSA-kulcsot az egysoros formátumban – "ssh-rsa" kezdetű (idézőjelek nélkül).

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook azure_create_aks.yml
```

A forgatókönyv futtatása eredmények jelennek meg a következő kimenet hasonlít:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>AKS-csomópontok skálázása

Az előző szakaszban lévő mintaforgatókönyv két csomópontot definiál. Állítsa be a csomópontok számát a módosításával a `count` értékét a `agent_pool_profiles` letiltása.

Mentse a következő forgatókönyvet `azure_configure_aks.yml` néven:

```yml
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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:

- Az a `your_ssh_key` helyőrző, adja meg a nyilvános RSA-kulcsot az egysoros formátumban – "ssh-rsa" kezdetű (idézőjelek nélkül).

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook azure_configure_aks.yml
```

A forgatókönyv futtatása eredmények jelennek meg a következő kimenet hasonlít:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Felügyelt AKS-fürt törlése

A minta forgatókönyv törlése egy AKS-fürtöt.

Mentse a következő forgatókönyvet `azure_delete_aks.yml` néven:


```yml
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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook azure_delete_aks.yml
```

A forgatókönyv futtatása eredmények jelennek meg a következő kimenet hasonlít:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Alkalmazások skálázása az Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)