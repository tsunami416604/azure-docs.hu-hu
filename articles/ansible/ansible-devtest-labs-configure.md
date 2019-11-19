---
title: Oktatóanyag – Labs konfigurálása Azure DevTest Labs Ansible használatával
description: Megtudhatja, hogyan konfigurálhat labort Azure DevTest Labs Ansible használatával
keywords: Ansible, Azure, devops, bash, ötletekbõl, devtest Labs
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b6981ee94c4d82997c574db037befb9782465d08
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156274"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Oktatóanyag: a laborok konfigurálása Azure DevTest Labs Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) lehetővé teszi a fejlesztők számára, hogy automatizálják a virtuálisgép-környezetek létrehozását az alkalmazásaikban. Ezeket a környezeteket az alkalmazások fejlesztéséhez, teszteléséhez és betanításához lehet konfigurálni. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Labor létrehozása
> * Tesztkörnyezet-házirendek beállítása
> * A labor-ütemtervek beállítása
> * A tesztkörnyezet virtuális hálózatának létrehozása
> * Összetevő-forrás definiálása a laborhoz
> * Virtuális gép létrehozása a laboron belül
> * A tesztkörnyezet összetevő-forrásainak és összetevőinek listázása
> * Az összetevő-források Azure Resource Manager adatainak beolvasása
> * A tesztkörnyezet létrehozása
> * A tesztkörnyezet rendszerképének létrehozása
> * A labor törlése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

A minta forgatókönyv-kódrészlet létrehoz egy Azure-erőforráscsoportot. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>A tesztkörnyezet létrehozása

A következő feladat létrehozza a minta labort.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Tesztkörnyezet-házirendek beállítása

Beállíthatja a tesztkörnyezet házirend-beállításait. A következő értékek állíthatók be:

- `user_owned_lab_vm_count` a felhasználó által birtokolható virtuális gépek száma
- `user_owned_lab_premium_vm_count` a felhasználó által birtokolható prémium szintű virtuális gépek száma
- `lab_vm_count` a labor virtuális gépek maximális száma
- `lab_premium_vm_count` a labor Premium virtuális gépek maximális száma
- `lab_vm_size` a laboratóriumi virtuális gépek megengedett mérete (i)
- `gallery_image` az engedélyezett katalógus-rendszerkép (ek)
- `user_owned_lab_vm_count_in_subnet` az alhálózaton lévő felhasználók maximális száma
- `lab_target_cost` a labor célja

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>A labor-ütemtervek beállítása

Az ebben a szakaszban szereplő minta feladat konfigurálja a labor-ütemtervet. 

A következő kódrészletben a virtuális gép indítási idejének megadásához a `lab_vms_startup` értéket kell használni. Hasonlóképpen, a `lab_vms_shutdown` érték beállítása a labor virtuális gép leállítási idejét is meghatározza.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>A tesztkörnyezet virtuális hálózatának létrehozása

A következő feladat létrehozza az alapértelmezett Tesztkörnyezet virtuális hálózatát.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Összetevő-forrás definiálása a laborhoz

Az összetevők forrása egy megfelelően strukturált GitHub-tárház, amely tartalmazza az összetevő-definíciót és a Azure Resource Manager sablonokat. Minden tesztkörnyezet előre definiált nyilvános összetevőket tartalmaz. A következő feladatok bemutatják, hogyan hozhat létre egy összetevő-forrást a laborhoz.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Virtuális gép létrehozása a laboron belül

Hozzon létre egy virtuális gépet a laboron belül.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>A tesztkörnyezet összetevő-forrásainak és összetevőinek listázása

A laborban található összes alapértelmezett és egyéni összetevő-forrás listázásához használja a következő feladatot:

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

A következő feladat felsorolja az összes összetevőt:

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Az összetevő-források Azure Resource Manager adatainak beolvasása

A `public environment repository`összes Azure Resource Manager sablonjának listázásához az előre definiált adattár sablonokkal:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

A következő feladat egy adott Azure Resource Manager sablon részleteit kérdezi le az adattárból:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>A tesztkörnyezet létrehozása

A következő feladat létrehozza a tesztkörnyezet környezetét a nyilvános környezet tárházának egyik sablonja alapján.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>A tesztkörnyezet rendszerképének létrehozása

A következő feladat létrehoz egy rendszerképet egy virtuális gépről. A rendszerkép lehetővé teszi, hogy azonos virtuális gépeket hozzon létre.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>A labor törlése

A labor törléséhez használja a következő feladatot:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>A minta forgatókönyvének beolvasása

A teljes példa a következő két módon szerezhető be:
- [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) forgatókönyvet, és mentse a `devtestlab-create.yml`ba.
- Hozzon létre egy `devtestlab-create.yml` nevű új fájlt, és másolja bele a következő tartalomba:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>A forgatókönyv futtatása

Ebben a szakaszban a forgatókönyv futtatásával tesztelheti a cikkben látható különféle funkciókat.

A forgatókönyv futtatása előtt végezze el a következő módosításokat:
- A `vars` szakaszban cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevére.
- Tárolja a GitHub-tokent egy `GITHUB_ACCESS_TOKEN`nevű környezeti változóként.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`ként:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)