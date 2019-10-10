---
title: Oktatóanyag – témakörök konfigurálása Azure Service Bus a Ansible használatával
description: Megtudhatja, hogyan hozhat létre Azure Service Bus témakört a Ansible használatával
keywords: Ansible, Azure, devops, bash, ötletekbõl, Service Bus, témakörök, előfizetések
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 952779db582f9437f10608bf86b0b80560ded2c0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241216"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Oktatóanyag: témakörök konfigurálása Azure Service Bus Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Üzenettémakör létrehozása
> * Előfizetés létrehozása
> * SAS-szabályzat létrehozása
> * Névtér adatainak beolvasása
> * Témakör-és előfizetési adatok beolvasása
> * SAS-szabályzat visszavonása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>A Service Bus-témakör létrehozása

A példaként szolgáló ötletekbõl-kód a következő erőforrásokat hozza létre:
- Azure-erőforráscsoport
- Service Bus névtér az erőforráscsoport belül
- Service Bus témakör a névtérrel

Mentse a következő forgatókönyvet `servicebus_topic.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: topic
    - debug:
          var: topic
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Előfizetés létrehozása

Az előfizetést egy Service Bus témakörben hozza létre. Azure Service Bus témakörök több előfizetéssel is rendelkezhetnek. A témakör előfizetője a témakörbe küldött összes üzenet másolatát fogadhatja. Az előfizetések névvel ellátott entitások, amelyek tartósan jönnek létre, de igény szerint lejárnak.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Create a subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs
    - debug:
          var: subs
```

Mentse a következő forgatókönyvet `servicebus_subscription.yml` néven:

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>SAS-szabályzat létrehozása

A [közös hozzáférésű aláírás (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) a jogkivonatokat használó jogcím-alapú engedélyezési mechanizmus. 

A példaként szolgáló ötletekbõl-kód két SAS-szabályzatot hoz létre egy Service Bus üzenetsor számára, különböző jogosultságokkal.

Mentse a következő forgatókönyvet `servicebus_topic_policy.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-{{ item }}"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: "{{ item }}"
      with_items:
        - send
        - listen
      register: policy
    - debug:
          var: policy
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_topic_policy.yml
```

## <a name="retrieve-namespace-information"></a>Névtér adatainak beolvasása

A példaként szolgáló forgatókönyv-kód lekérdezi a névtér információit.

Mentse a következő forgatókönyvet `servicebus_namespace_info.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:
- A @no__t 0 érték azt jelzi, hogy megjelenjenek-e a SAS-szabályzatok a megadott névtérben. Alapértelmezés szerint az érték `False` a további hálózati terhelés elkerülése érdekében.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Témakör-és előfizetési adatok beolvasása

A példa az alábbi információkra kérdezi le a forgatókönyveket:
- Service Bus témakör adatai
- A témakör előfizetési adatainak listája
 
Mentse a következő forgatókönyvet `servicebus_list.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Get a topic's information
      azure_rm_servicebus_facts:
          type: topic
          name: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: topic_fact
    - name: "List subscriptions under topic {{ topic }}"
      azure_rm_servicebus_facts:
          type: subscription
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: subs_fact
    - debug:
          var: "{{ item }}"
      with_items:
        - topic_fact.servicebuses[0]
        - subs_fact.servicebuses
```

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:
- A @no__t 0 érték azt jelzi, hogy megjelenjenek-e a SAS-szabályzatok a megadott várólistán. Alapértelmezés szerint ez az érték `False` értékűre van állítva a további hálózati terhelés elkerülése érdekében.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_list.yml
```

## <a name="revoke-the-queue-sas-policy"></a>A várólista SAS-szabályzatának visszavonása

A példa a forgatókönyvhöz tartozó SAS-szabályzatot törli.

Mentse a következő forgatókönyvet `servicebus_queue_policy_delete.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
  tasks:
    - name: Delete a policy
      azure_rm_servicebussaspolicy:
          name: "{{ topic }}-policy"
          topic: "{{ topic }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`-ként:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      topic: servicebustesttopic
      subscription: servicebustestsubs
  tasks:
    - name: Delete subscription
      azure_rm_servicebustopicsubscription:
          name: "{{ subscription }}"
          topic: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete topic
      azure_rm_servicebustopic:
          name: "{{ topic }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)