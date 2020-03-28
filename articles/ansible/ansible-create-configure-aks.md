---
title: Oktatóanyag – Konfigurálja az Azure Kubernetes-szolgáltatás (AKS) fürtjeit az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan hozhat létre és felügyelhet Azure Kubernetes Service-fürtöket az Azure-ban az Ansible segítségével.
keywords: ansible, azúr, devops, bash, cloudshell, ötletekbõl, aks, konténer, aks, kubernetes
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 6672c3fac1c5d546a61622e3fd6df6c5397f87a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156685"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Oktatóanyag: Konfigurálja az Azure Kubernetes-szolgáltatás (AKS) fürtjeit az Azure-ban az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AKS beállítható úgy, hogy [az Azure Active Directory (AD)](/azure/active-directory/) felhasználói hitelesítést használjon. Konfigurálás után az Azure AD hitelesítési jogkivonatot használja az AKS-fürtbe való bejelentkezéshez. Az RBAC alapulhat a felhasználó identitás- vagy címtárcsoport-tagságán.

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

A minta forgatókönyv létrehoz egy erőforráscsoportot és egy AKS-fürtaz erőforráscsoporton belül.

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:

- Az első `tasks` szakasz a `eastus` helyen `myResourceGroup` belül megnevezett erőforráscsoportot határoz meg.
- A második `tasks` szakasz az `myResourceGroup` erőforráscsoporton `myAKSCluster` belül megnevezett AKS-fürtöt határoz meg.
- A `your_ssh_key` helyőrzőhöz adja meg az RSA nyilvános kulcsát egysoros formátumban – kezdve az "ssh-rsa" értékkel (idézőjelek nélkül).
- A `aks_version` helyőrzőhöz használja az [aks get-versions](/cli/azure/aks?view=azure-cli-latest#az-aks-get-versions) parancsot.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook azure_create_aks.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket jelenít meg:

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

Az előző szakaszban lévő mintaforgatókönyv két csomópontot definiál. A csomópontok számát a `count` `agent_pool_profiles` blokk értékének módosításával állíthatja be.

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:

- A `your_ssh_key` helyőrzőhöz adja meg az RSA nyilvános kulcsát egysoros formátumban – kezdve az "ssh-rsa" értékkel (idézőjelek nélkül).

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook azure_configure_aks.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket jelenít meg:

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

A minta forgatókönyv töröl egy AKS-fürt.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook azure_delete_aks.yml
```

A forgatókönyv futtatása a következő kimenethez hasonló eredményeket jelenít meg:

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
> [Oktatóanyag: Alkalmazások méretezése az Azure Kubernetes Service-ben (AKS)](/azure/aks/tutorial-kubernetes-scale)