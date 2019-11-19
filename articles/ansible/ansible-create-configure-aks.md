---
title: Oktatóanyag – Azure Kubernetes Service (ak) fürtök konfigurálása az Azure-ban a Ansible használatával
description: Megtudhatja, hogyan hozhat létre és felügyelhet Azure Kubernetes Service-fürtöket az Azure-ban az Ansible segítségével.
keywords: Ansible, Azure, devops, bash, cloudshellben, ötletekbõl, AK, tároló, AK, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156685"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Oktatóanyag: Azure Kubernetes Service (ak) fürtök konfigurálása az Azure-ban a Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AK konfigurálható úgy, hogy [Azure Active Directory (ad)](/azure/active-directory/) használatát használja a felhasználói hitelesítéshez. A konfigurálást követően az Azure AD-hitelesítési token használatával jelentkezhet be az AK-fürtbe. A RBAC a felhasználó identitás-vagy címtár-csoporttagság alapján lehet.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * AK-fürt konfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Felügyelt AKS-fürt létrehozása

A minta forgatókönyv létrehoz egy erőforráscsoportot és egy AK-fürtöt az erőforráscsoporthoz.

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
    aks_version: aks_version
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
      kubernetes_version: "{{aks_version}}"
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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

- A `tasks`on belüli első szakasz egy `myResourceGroup` nevű erőforráscsoportot határoz meg a `eastus` helyen belül.
- A `tasks` belüli második szakasz egy `myAKSCluster` nevű AK-fürtöt határoz meg a `myResourceGroup` erőforráscsoporthoz.
- A `your_ssh_key` helyőrzőben adja meg az RSA nyilvános kulcsát az egysoros formátumban – kezdve az "SSH-RSA" kifejezéssel (idézőjelek nélkül).
- A `aks_version` helyőrzőnél használja az az [AK Get-Versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) parancsot.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook azure_create_aks.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket mutat:

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

Az előző szakaszban lévő mintaforgatókönyv két csomópontot definiál. A csomópontok számát a `agent_pool_profiles` blokk `count` értékének módosításával állíthatja be.

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:

- A `your_ssh_key` helyőrzőben adja meg az RSA nyilvános kulcsát az egysoros formátumban – kezdve az "SSH-RSA" kifejezéssel (idézőjelek nélkül).

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook azure_configure_aks.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket mutat:

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

A minta-forgatókönyv egy AK-fürtöt töröl.

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook azure_delete_aks.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket mutat:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Alkalmazások méretezése az Azure Kubernetes Service-ben (AKS)](/azure/aks/tutorial-kubernetes-scale)