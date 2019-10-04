---
title: Oktatóanyag – fürt konfigurálása az Azure HDInsight-ben a Ansible használatával | Microsoft Docs
description: Útmutató az Azure-HDInsight létrehozásához és átméretezéséhez a Ansible használatával
keywords: ansible, azure, devops, bash, playbook, apache hadoop, hdinsight
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ff135473443589650d90bfb7f3e5aa248f72e821
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861272"
---
# <a name="tutorial-configure-a-cluster-in-azure-hdinsight-using-ansible"></a>Oktatóanyag: Fürt konfigurálása az Azure HDInsight-ben a Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

Az [Azure HDInsight](/azure/hdinsight/) egy Hadoop-alapú elemzési szolgáltatás az adatfeldolgozáshoz. A HDInsight egy ETL (kinyerési, átalakítási, betöltési) eszköz, amellyel a big data strukturált vagy strukturálatlan lehet. A HDInsight többféle [típusú fürtöt](/azure/hdinsight/hadoop/apache-hadoop-introduction) támogat, ahol az egyes típusok támogatják az összetevők különböző készletét. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Storage-fiók létrehozása a HDInsight
> * [HDInsight Spark-fürt](/azure/hdinsight/spark/apache-spark-overview)konfigurálása.
> * Fürt átméretezése
> * Fürt törlése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-random-postfix"></a>Véletlenszerű Postfix létrehozása

Az ebben a szakaszban szereplő forgatókönyv-kód egy véletlenszerű Postfix-t hoz létre az Azure HDInsight-fürt nevének részeként való használatra.

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

Az Azure-erőforráscsoport olyan logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése történik.

Az ebben a szakaszban található forgatókönyv-kód egy erőforráscsoportot hoz létre.


```yml
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

## <a name="create-a-storage-account-and-retrieve-key"></a>Storage-fiók létrehozása és a kulcs lekérése

Az Azure Storage-fiók a HDInsight-fürt alapértelmezett tárolója. 

Az ebben a szakaszban található forgatókönyv-kód a Storage-fiók eléréséhez használt kulcsot kérdezi le.

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

Az ebben a szakaszban található forgatókönyv-kód létrehozza az Azure HDInsight-fürtöt.

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

A fürt létrehozása után az egyetlen módosítható beállítás a munkavégző csomópontok száma. 

Az ebben a szakaszban szereplő forgatókönyv-kód a alkalmazásban történő frissítéssel `target_instance_count` `workernode`növeli a munkavégző csomópontok számát.

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

## <a name="delete-the-cluster-instance"></a>A fürt példányának törlése

A HDInsight-fürtök számlázása percenkénti arányban történik. 

Az ebben a szakaszban szereplő forgatókönyv-kód törli a fürtöt.

```yml
- name: Delete instance of Cluster
  azure_rm_hdinsightcluster:
    resource_group: "{{ resource_group }}"
    name: "{{ cluster_name }}"
    state: absent
```

## <a name="get-the-sample-playbook"></a>A minta forgatókönyvének beolvasása

A teljes példa a következő két módon szerezhető be:
- [Töltse le a](https://github.com/Azure-Samples/ansible-playbooks/blob/master/hdinsight_create.yml) forgatókönyvet, és mentse `hdinsight_create.yml`a következőre:.
- Hozzon létre egy nevű `hdinsight_create.yml` új fájlt, és másolja bele a következő tartalomba:

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

Ebben a szakaszban a forgatókönyv futtatásával tesztelheti a cikkben látható különféle funkciókat.

A forgatókönyv futtatása előtt végezze el a következő módosításokat:
- A `vars` szakaszban cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevére.

Futtassa a forgatókönyvt a `ansible-playbook` parancs használatával:

```bash
ansible-playbook hdinsight.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`:

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

Futtassa a forgatókönyvt a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)