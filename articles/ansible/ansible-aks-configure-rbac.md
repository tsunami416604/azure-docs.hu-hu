---
title: Oktatóanyag – Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök konfigurálása az Azure Kubernetes-szolgáltatásban (AKS) az Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja az RBAC az RBAC-ot az Azure Kubernetes-fürt (AKS) fürtjében
keywords: ansible, azúr, devops, bash, cloudshell, ötletekbõl, aks, konténer, aks, kubernetes, azúrkék active directory, rbac
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76836966"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: Szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök konfigurálása az Azure Kubernetes-szolgáltatásban (AKS) az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AKS beállítható úgy, hogy [az Azure Active Directory (AD)](/azure/active-directory/) felhasználói hitelesítést használjon. Konfigurálás után az Azure AD hitelesítési jogkivonatot használja az AKS-fürtbe való bejelentkezéshez. Az RBAC alapulhat a felhasználó identitás- vagy címtárcsoport-tagságán.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure AD-kompatibilis AKS-fürt létrehozása
> * RBAC szerepkör konfigurálása a fürtben

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **A RedHat OpenShift könyvtár telepítése** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Az Azure AD konfigurálása AKS-hitelesítéshez

Az Azure AD AKS-hitelesítéshez történő konfigurálásakor két Azure AD-alkalmazás van konfigurálva. Ezt a műveletet egy Azure-bérlői rendszergazdának kell végrehajtania. További információ: [Az Azure Active Directory integrálása az AKS-sel](/azure/aks/aad-integration#create-the-server-application)című témakörben talál. 

Az Azure-bérlői rendszergazdától a következő értékeket szerezheti be:

- Kiszolgálóalkalmazás titkosítata
- Kiszolgálóalkalmazás azonosítója
- Ügyfélalkalmazás azonosítója 
- Bérlőazonosító

Ezek az értékek szükségesek a minta forgatókönyv futtatásához.  

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ebben a szakaszban létrehoz egy AKS-t az [Azure AD-alkalmazással.](#configure-azure-ad-for-aks-authentication)

Íme néhány fontos megjegyzés, amelyet figyelembe kell venni a minta forgatókönyvével való munka során:

- A forgatókönyv `ssh_key` betöltődik `~/.ssh/id_rsa.pub`. Ha módosítja, használja az egysoros formátumot - kezdve az "ssh-rsa" (idézőjelek nélkül).
- A `client_id` `client_secret` és az `~/.azure/credentials`értékek betöltődnek a programból, amely az alapértelmezett hitelesítő adatok fájlja. Ezeket az értékeket beállíthatja a szolgáltatásnévhez, vagy betöltheti ezeket az értékeket a környezeti változókból:

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
  azure_rm_aksversion_facts:
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

## <a name="get-the-azure-ad-object-id"></a>Az Azure AD-objektum azonosítójának beszereznie

RBAC-kötés létrehozásához először be kell szereznie az Azure AD-objektum azonosítót. 

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. A lap tetején lévő keresőmezőbe írja `Azure Active Directory`be a mezőbe a . 

1. Kattintson a `Enter` gombra.

1. A **Kezelés** menüben válassza a **Felhasználók**lehetőséget.

1. A név mezőben keresse meg a fiókját.

1. A **Név** oszlopban jelölje ki a fiókjához mutató hivatkozást.

1. Az **Identitás** szakaszban másolja az **objektumazonosítót**.

    ![Másolja az Azure AD-objektum azonosítójának másolása.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC-kötés létrehozása

Ebben a szakaszban hozzon létre egy szerepkör-kötés vagy fürtszerepkör-kötés az AKS-ben. 

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

Cserélje `<your-aad-account>` le a helyőrzőt az Azure AD-bérlői [objektumazonosítóra.](#get-the-azure-ad-object-id)

Mentse a következő forgatókönyv - hogy telepíti az új `aks-kube-deploy.yml`szerepkör t AKS - mint:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>A mintaforgatókönyv futtatása

Ez a szakasz a jelen cikkben létrehozott feladatokat meghívja teljes mintaforgatókönyvet sorolja fel. 

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

A `vars` szakaszban cserélje le a következő helyőrzőket az Azure AD-adatokkal:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Futtassa a teljes `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Az eredmények ellenőrzése

Ebben a szakaszban kubectl használja a cikkben létrehozott csomópontok listáját.

Írja be a következő parancsot a terminálsorra:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

A parancs egy hitelesítési oldalra irányítja. Jelentkezzen be Azure-fiókjával.

A hitelesítést követően a kubectl a csomópontokat a következő eredményekhez hasonlóan sorolja fel:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő `cleanup.yml`kódot:

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ansible az Azure-on](/azure/ansible/)
