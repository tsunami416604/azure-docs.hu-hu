---
title: Oktatóanyag – szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök konfigurálása az Azure Kubernetes Service (AKS) az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan RBAC konfigurálása az Azure Kubernetes Service(AKS) fürtben az Ansible segítségével
keywords: az ansible, azure, devops, bash, cloud Shell, forgatókönyv, aks, tároló, az aks, kubernetes, az azure active directory, az rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: dbef7c2cb8de5a1b4bbb3073f694b8f77c9f441b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231297"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: Szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök konfigurálása az Azure Kubernetes Service (AKS) az Ansible-lel

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AKS használatához konfigurálhatók [Azure Active Directory (AD)](/azure/active-directory/) a felhasználók hitelesítéséhez. Miután konfigurálta az Azure AD hitelesítési jogkivonat használatával jelentkezzen be az AKS-fürtöt. Az RBAC a felhasználó identitását, vagy a directory-csoporttagság alapulhat.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Az Azure AD-kompatibilis AKS-fürt létrehozása
> * A fürt egy RBAC szerepkör konfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Telepítse az RedHat OpenShift könyvtár** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>AKS-hitelesítéshez az Azure AD konfigurálása

AKS-hitelesítéshez az Azure AD konfigurálása, ha a két Azure AD-alkalmazások úgy vannak konfigurálva. Ez a művelet egy Azure-bérlő rendszergazdája kell elvégezni. További információkért lásd: [integrálása az Azure Active Directory az aks-sel](/azure/aks/aad-integration#create-server-application). 

Az Azure-bérlő rendszergazdája kérje le a következő értékeket:

- Kiszolgáló titkos Alkalmazáskulcs
- Server app-azonosítója
- Ügyfélalkalmazás-azonosító 
- Bérlőazonosító

Ezeket az értékeket a minta forgatókönyv futtatása szükséges.  

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ebben a szakaszban egy aks-ben létrehozhat a [Azure AD-alkalmazás](#configure-azure-ad-for-aks-authentication).

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- A forgatókönyv betölti `ssh_key` a `~/.ssh/id_rsa.pub`. Ha módosít, a "ssh-rsa" kezdetű (idézőjelek nélkül) - egysoros formátumot használja.
- A `client_id` és `client_secret` értékek töltődnek be a `~/.azure/credentials`, azaz az alapértelmezett hitelesítő adatait tartalmazó fájlt. Ezeket az értékeket beállítani, ha a szolgáltatás egyszerű, vagy betölteni ezeket az értékeket a környezeti változókat:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Mentse a következő forgatókönyvet `aks-create.yml` néven:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Az Azure AD-objektum Azonosítójának lekéréséhez

Az RBAC-kötést létrehozni, szüksége lesz a lekérése az Azure AD-objektum azonosítója. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Írja be a keresőmezőbe, a lap tetején, `Azure Active Directory`. 

1. Kattintson a `Enter` gombra.

1. Az a **kezelés** menüjében válassza **felhasználók**.

1. A név mezőben keresse meg a fiók.

1. Az a **neve** oszlopban jelölje ki a hivatkozást a fiókra.

1. Az a **identitás** területén másolja a **Objektumazonosító**.

    ![Másolja ki az Azure AD-objektum azonosítója.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Az RBAC-kötés létrehozása

Ebben a szakaszban egy kötés szerepkör vagy a fürt szerepkör-kötést az aks-ben hoz létre. 

Mentse a következő forgatókönyvet `kube-role.yml` néven:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Cserélje le a `&lt;your-aad-account>` helyőrzőt az Azure AD-bérlő [Objektumazonosító](#get-the-azure-ad-object-id).

A következő forgatókönyv - AKS üzembe helyezi az új szerepkör - Mentés másként `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ez a szakasz felsorolja a teljes minta a forgatókönyvet, amely meghívja a feladatok létrehozása ebben a cikkben. 

Mentse a következő forgatókönyvet `aks-rbac.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Az a `vars` szakaszban, a helyőrzőket cserélje le az Azure AD-adatok:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

A teljes forgatókönyv használatával futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Az eredmények ellenőrzése

Ebben a szakaszban használhatja a kubectl listán a csomópontok létrehozásához a cikkben.

Adja meg a következő parancsot a terminálon a parancssorba:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

A parancs átirányítja egy hitelesítési oldalra. Jelentkezzen be az Azure-fiókjával.

A hitelesítést követően a kubectl a csomópontok hasonlóan zajlik, a következő eredményeket sorolja fel:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ansible az Azure-on](/azure/ansible/)