---
title: Oktatóanyag – Laborok konfigurálása az Azure DevTest Labs ben az Ansible használatával
description: Ismerje meg, hogyan konfigurálhat tesztkörnyezetet az Azure DevTest Labs-ben az Ansible használatával
keywords: ansible, azúr, devops, bash, ötletekbõl, devtest labs
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: b6981ee94c4d82997c574db037befb9782465d08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156274"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Oktatóanyag: Laborok konfigurálása az Azure DevTest Labs-ben az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Az Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) lehetővé teszi a fejlesztők számára, hogy automatizálják az alkalmazások virtuálisgép-környezetek létrehozását. Ezek a környezetek konfigurálhatók az alkalmazások fejlesztéséhez, teszteléséhez és képzéséhez. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Labor létrehozása
> * A tesztkörnyezet-házirendek beállítása
> * A labor ütemezésének beállítása
> * A tesztkörnyezet virtuális hálózatának létrehozása
> * Műtermékforrás definiálása a laborszámára
> * Virtuális gép létrehozása a tesztkörnyezetben
> * A labor műtárgyforrásainak és műtárgy-összetevőinek listázása
> * Az Azure Resource Manager adatainak beszereznie a műtermék-forrásokról
> * A laborkörnyezet létrehozása
> * A laborkép létrehozása
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

## <a name="create-the-lab"></a>A labor létrehozása

A következő feladat létrehozza a mintalabor.

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

## <a name="set-the-lab-policies"></a>A tesztkörnyezet-házirendek beállítása

Beállíthatja a laborházirend-beállításokat. A következő értékek állíthatók be:

- `user_owned_lab_vm_count`a felhasználó tulajdonában lévő virtuális gépek száma
- `user_owned_lab_premium_vm_count`a prémium szintű virtuális gépek száma, amelyet a felhasználó birtokolhat
- `lab_vm_count`a laboros virtuális gépek maximális száma
- `lab_premium_vm_count`A labor prémium szintű virtuális gépek maximális száma
- `lab_vm_size`az engedélyezett tesztkörnyezet virtuális gépmérete(i)
- `gallery_image`az engedélyezett galériakép(ek)
- `user_owned_lab_vm_count_in_subnet`A felhasználó virtuális gépeinek maximális száma egy alhálózatban
- `lab_target_cost`a cél költsége a labor

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

## <a name="set-the-lab-schedules"></a>A labor ütemezésének beállítása

Ebben a szakaszban a mintafeladat konfigurálja a labor ütemezését. 

A következő kódrészletben az `lab_vms_startup` érték a virtuális gép indítási idejének megadására szolgál. Hasonlóképpen az `lab_vms_shutdown` érték beállítása létrehozza a labor virtuális gép leállítási idejét.

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

Ez a következő feladat létrehozza az alapértelmezett tesztkörnyezet virtuális hálózat.

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

## <a name="define-an-artifact-source-for-the-lab"></a>Műtermékforrás definiálása a laborszámára

A műtermékek forrása egy megfelelően strukturált GitHub-tárház, amely összetevő-definíciót és Az Azure Resource Manager-sablonokat tartalmaz. Minden laborhoz előre meghatározott nyilvános műtárgyak járnak. A következő feladatok bemutatják, hogyan hozhat létre egy műtárgy forrás egy tesztkörnyezetben.

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

## <a name="create-a-vm-within-the-lab"></a>Virtuális gép létrehozása a tesztkörnyezetben

Hozzon létre egy virtuális gép a laboron belül.

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

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>A labor műtárgyforrásainak és műtárgy-összetevőinek listázása

Az összes alapértelmezett és egyéni összetevőforrás felsorolásához használja a következő feladatot:

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

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Az Azure Resource Manager adatainak beszereznie a műtermék-forrásokról

Az Azure Resource Manager összes `public environment repository`sablonjának listázásához az előre definiált tárházban, amely sablonokat foglal el:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

És a következő feladat lekéri egy adott Azure Resource Manager-sablon részleteit a tárházból:

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

## <a name="create-the-lab-environment"></a>A laborkörnyezet létrehozása

A következő feladat létrehozza a labor környezet alapján a sablonok nyilvános környezeti tárházból származó sablonok alapján.

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

## <a name="create-the-lab-image"></a>A laborkép létrehozása

A következő feladat létrehoz egy lemezképet egy virtuális gép. A lemezkép lehetővé teszi, hogy azonos virtuális gépeket hozzon létre.

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

A tesztkörnyezet törléséhez használja a következő feladatot:

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

## <a name="get-the-sample-playbook"></a>A minta forgatókönyvének beszereznie

A teljes forgatókönyv kétféleképpen szerezhető be:
- [Töltse le a forgatókönyvet,](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) és mentse el `devtestlab-create.yml`.
- Hozzon létre `devtestlab-create.yml` egy új fájl nevű és másolja be a következő tartalmat:

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

Ebben a szakaszban futtassa a forgatókönyvet a cikkben bemutatott különböző funkciók teszteléséhez.

A forgatókönyv futtatása előtt hajtsa végre a következő módosításokat:
- A `vars` szakaszban cserélje `{{ resource_group_name }}` le a helyőrzőt az erőforráscsoport nevére.
- A GitHub-jogkivonatot egy `GITHUB_ACCESS_TOKEN`nevű környezeti változóként tárolja.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő `cleanup.yml`kódot:

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)