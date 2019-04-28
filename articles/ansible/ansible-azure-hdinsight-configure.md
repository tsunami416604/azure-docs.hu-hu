---
title: Oktatóanyag – fürt konfigurálása az Azure HDInsight, az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre, és a egy Azure HDInsight átméretezése az Ansible használatával
keywords: ansible, azure, devops, bash, playbook, apache hadoop, hdinsight
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 94d889eb3d9b917f483d62f3b0719e053fb779ce
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767270"
---
# <a name="tutorial-configure-a-cluster-in-azure-hdinsight-using-ansible"></a>Oktatóanyag: Az Ansible-lel az Azure HDInsight-fürt konfigurálása

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Az Azure HDInsight](/azure/hdinsight/) egy Hadoop-alapú elemzési szolgáltatás, az adatok feldolgozása. HDInsight egy együttműködni a big data - az ETL (extract, transform, load) eszköz vagy strukturált vagy strukturálatlan. HDInsight támogatja több [fürttípusok](/azure/hdinsight/hadoop/apache-hadoop-introduction#cluster-types-in-hdinsight) ahol egyes összetevőket külön készletét támogatja-e. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * A HDInsight a storage-fiók létrehozása
> * Konfigurálja a [HDInsight Spark-fürt](/azure/hdinsight/spark/apache-spark-overview).
> * A fürt átméretezése
> * Fürt törlése

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-random-postfix"></a>Hozzon létre egy véletlenszerű utótag

Ebben a szakaszban a forgatókönyv kód létrehoz egy véletlenszerű utótag használata az Azure HDInsight-fürt neve részeként.

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"  
  tasks:
    - name: Prepare random prefix
      set_fact:
        rpfx: "{{ resource_group | hash('md5') | truncate(7, True, '') }}{{ 1000 | random }}"
      run_once: yes
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure erőforrások üzembe helyezése és felügyelt.

A forgatókönyv kód ebben a szakaszban létrehoz egy erőforráscsoportot.


```yml
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

## <a name="create-a-storage-account-and-retrieve-key"></a>Hozzon létre egy tárfiókot és a kulcs lekérése

Egy Azure storage-fiókot az alapértelmezett tárolóként szolgál a HDInsight-fürt. 

Ebben a szakaszban a forgatókönyv kód lekéri a tárfiók eléréséhez használt kulcs.

```yml
- name: Create storage account
  azure_rm_storageaccount:
      resource_group: "{{ resource_group }}"
      name: "{{ storage_account_name }}"
      account_type: Standard_LRS
      location: eastus2

- name: Get storage account keys
  azure_rm_resource:
    api_version: '2018-07-01'
    method: POST
    resource_group: "{{ resource_group }}"
    provider: storage
    resource_type: storageaccounts
    resource_name: "{{ storage_account_name }}"
    subresource:
      - type: listkeys
  register: storage_output

- debug:
    var: storage_output
```

## <a name="create-an-hdinsight-spark-cluster"></a>HDInsight Spark-fürt létrehozása

Ebben a szakaszban a forgatókönyv kódot az Azure HDInsight-fürtöt hoz létre.

```yml
- name: Create instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    location: eastus2
    cluster_version: 3.6
    os_type: linux
    tier: standard
    cluster_definition:
      kind: spark
      gateway_rest_username: http-user
      gateway_rest_password: MuABCPassword!!@123
    storage_accounts:
      - name: "{{ storage_account_name }}.blob.core.windows.net"
        is_default: yes
        container: "{{ cluster_name }}"
        key: "{{ storage_output['response']['keys'][0]['value'] }}"
    compute_profile_roles:
      - name: headnode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: workernode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: zookeepernode
        target_instance_count: 3
        vm_size: Medium
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
```

A példány létrehozása több percet is igénybe vehet.

## <a name="resize-the-cluster"></a>A fürt átméretezése

Fürt létrehozása után módosíthatja az egyetlen beállítás a munkavégző csomópontok számát. 

Ebben a szakaszban a forgatókönyv kód növeli a munkavégző csomópontok számát frissítésével `target_instance_count` belül `workernode`.

```yml
- name: Resize cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    location: eastus2
    cluster_version: 3.6
    os_type: linux
    tier: standard
    cluster_definition:
      kind: spark
      gateway_rest_username: http-user
      gateway_rest_password: MuABCPassword!!@123
    storage_accounts:
      - name: "{{ storage_account_name }}.blob.core.windows.net"
        is_default: yes
        container: "{{ cluster_name }}"
        key: "{{ storage_output['response']['keys'][0]['value'] }}"
    compute_profile_roles:
      - name: headnode
        target_instance_count: 1
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: workernode
        target_instance_count: 2
        vm_size: Standard_D3
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
      - name: zookeepernode
        target_instance_count: 3
        vm_size: Medium
        linux_profile:
          username: sshuser
          password: MuABCPassword!!@123
    tags:
      aaa: bbb
  register: output
```

## <a name="delete-the-cluster-instance"></a>A példány törlése

A HDInsight-fürtök számlázása elszámolás percdíjas. 

Ebben a szakaszban a forgatókönyv kód törli a fürtöt.

```yml
- name: Delete instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    state: absent
```

## <a name="get-the-sample-playbook"></a>A mintául szolgáló forgatókönyvek beolvasása

A teljes minta forgatókönyv beolvasásához két módja van:
- [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/hdinsight_create.yml) , és mentse a `hdinsight_create.yml`.
- Hozzon létre egy új fájlt `hdinsight_create.yml` és másolja bele a következő tartalommal:

```yml
---
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Prepare random prefix
      set_fact:
        rpfx: "{{ resource_group | hash('md5') | truncate(7, True, '') }}{{ 1000 | random }}"
      run_once: yes

- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    location: eastus2
    vnet_name: myVirtualNetwork
    subnet_name: mySubnet
    cluster_name: mycluster{{ rpfx }}
    storage_account_name: mystorage{{ rpfx }}

  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create storage account
      azure_rm_storageaccount:
        resource_group: "{{ resource_group }}"
        name: "{{ storage_account_name }}"
        account_type: Standard_LRS
        location: "{{ location }}"

    - name: Get storage account keys
      azure_rm_resource:
        api_version: '2018-07-01'
        method: POST
        resource_group: "{{ resource_group }}"
        provider: storage
        resource_type: storageaccounts
        resource_name: "{{ storage_account_name }}"
        subresource:
          - type: listkeys
      register: storage_output
    - debug:
        var: storage_output

    - name: Create instance of Cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        location: "{{ location }}"
        cluster_version: 3.6
        os_type: linux
        tier: standard
        cluster_definition:
          kind: spark
          gateway_rest_username: http-user
          gateway_rest_password: MuABCPassword!!@123 
        storage_accounts:
          - name: "{{ storage_account_name }}.blob.core.windows.net" 
            is_default: yes
            container: "{{ cluster_name }}"
            key: "{{ storage_output['response']['keys'][0]['value'] }}"
        compute_profile_roles:
          - name: headnode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: workernode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: zookeepernode
            target_instance_count: 3
            vm_size: Medium
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123

    - name: Resize cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        location: "{{ location }}"
        cluster_version: 3.6
        os_type: linux
        tier: standard
        cluster_definition:
          kind: spark
          gateway_rest_username: http-user
          gateway_rest_password: MuABCPassword!!@123 
        storage_accounts:
          - name: "{{ storage_account_name }}.blob.core.windows.net"
            is_default: yes
            container: "{{ cluster_name }}"
            key: "{{ storage_output['response']['keys'][0]['value'] }}"
        compute_profile_roles:
          - name: headnode
            target_instance_count: 1
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: workernode
            target_instance_count: 2
            vm_size: Standard_D3
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
          - name: zookeepernode
            target_instance_count: 3
            vm_size: Medium
            linux_profile:
              username: sshuser
              password: MuABCPassword!!@123
        tags:
          aaa: bbb
      register: output
    - debug:
        var: output

    - name: Assert the state has changed
      assert:
        that:
          - output.changed

    - name: Delete instance of Cluster
      azure_rm_hdinsightcluster:
        resource_group: "{{ resource_group }}"
        name: "{{ cluster_name }}"
        state: absent
```

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ebben a szakaszban a forgatókönyv teszteléséhez a jelen cikkben ismertetett különféle szolgáltatások futtatása.

A forgatókönyv futtatása előtt végezze el az alábbi módosításokat:
- Az a `vars` szakaszban, cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevét.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook hdinsight.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)