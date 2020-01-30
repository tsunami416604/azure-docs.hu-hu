---
title: Oktatóanyag – szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök konfigurálása az Azure Kubernetes szolgáltatásban (ak) a Ansible használatával
description: Ismerje meg, hogyan konfigurálhatja a RBAC az Azure Kubernetes Service (ak) fürtön a Ansible használatával
keywords: Ansible, Azure, devops, bash, cloudshellben, ötletekbõl, AK, tároló, AK, kubernetes, Azure Active Directory, RBAC
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 5fac42383ee56318cc4b8f39323c02d05853dbb6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836966"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök konfigurálása az Azure Kubernetes szolgáltatásban (ak) a Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Az AK konfigurálható úgy, hogy [Azure Active Directory (ad)](/azure/active-directory/) használatát használja a felhasználói hitelesítéshez. A konfigurálást követően az Azure AD-hitelesítési token használatával jelentkezhet be az AK-fürtbe. A RBAC a felhasználó identitás-vagy címtár-csoporttagság alapján lehet.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure AD-kompatibilis AK-fürt létrehozása
> * RBAC-szerepkör konfigurálása a fürtben

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **A RedHat OpenShift-könyvtár - telepítése** `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Az Azure AD konfigurálása az AK-hitelesítéshez

Ha az Azure AD-t AK-hitelesítésre konfigurálja, két Azure AD-alkalmazás van konfigurálva. Ezt a műveletet egy Azure bérlői rendszergazdának kell elvégeznie. További információ: [Azure Active Directory integrálása az AK](/azure/aks/aad-integration#create-the-server-application)-nal. 

Az Azure-bérlői rendszergazdától szerezze be a következő értékeket:

- Kiszolgálói alkalmazás titka
- Kiszolgálói alkalmazás azonosítója
- Ügyfélalkalmazás azonosítója 
- Bérlőazonosító

Ezek az értékek szükségesek a minta forgatókönyv futtatásához.  

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ebben a szakaszban létrehoz egy AK-t az [Azure ad-alkalmazással](#configure-azure-ad-for-aks-authentication).

Íme néhány fontos megjegyzés, amelyet érdemes figyelembe venni a példa forgatókönyvének használatakor:

- A forgatókönyv betölti `ssh_key`t a `~/.ssh/id_rsa.pub`ból. Ha módosítja, használja az egysoros formátumot – az "SSH-RSA" kezdetű értékkel (idézőjelek nélkül).
- A `client_id` és `client_secret` értékek betöltődik a `~/.azure/credentials`ból, amely az alapértelmezett hitelesítőadat-fájl. Ezeket az értékeket beállíthatja az egyszerű szolgáltatásnév számára, vagy betöltheti ezeket az értékeket a környezeti változókból:

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

## <a name="get-the-azure-ad-object-id"></a>Az Azure AD-objektum AZONOSÍTÓjának beolvasása

RBAC-kötés létrehozásához először le kell kérnie az Azure AD-objektum AZONOSÍTÓját. 

1. Jelentkezzen be az [Azure portálra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. A lap tetején található Keresés mezőben adja meg a `Azure Active Directory`. 

1. Kattintson a `Enter` gombra.

1. A **kezelés** menüben válassza a **felhasználók**lehetőséget.

1. A név mezőben keresse meg a fiókját.

1. A **név** oszlopban válassza ki a fiókra mutató hivatkozást.

1. Az **identitás** szakaszban másolja ki az **objektumazonosítót**.

    ![Másolja az Azure AD-objektum AZONOSÍTÓját.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC kötés létrehozása

Ebben a szakaszban létrehoz egy szerepkör-kötést vagy egy, a fürthöz tartozó szerepkör-kötést az AK-ban. 

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

Cserélje le az `<your-aad-account>` helyőrzőt az Azure AD-bérlői [objektum azonosítójával](#get-the-azure-ad-object-id).

Mentse a következő ötletekbõl-t, amely üzembe helyezi az új szerepkört az AK-ban – `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ez a szakasz felsorolja az ebben a cikkben létrehozott feladatokat meghívó teljes minta-forgatókönyveket. 

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

A `vars` szakaszban cserélje le az alábbi helyőrzőket az Azure AD-adataira:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Futtassa a teljes forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Az eredmények ellenőrzése

Ebben a szakaszban a kubectl-t használja a cikkben létrehozott csomópontok listázásához.

Adja meg a következő parancsot egy terminál parancssorába:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

A parancs egy hitelesítési lapra irányítja. Jelentkezzen be az Azure-fiókjával.

A hitelesítés után a kubectl a következő eredményekhez hasonló módon listázza a csomópontokat:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`ként:

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ansible az Azure-on](/azure/ansible/)
