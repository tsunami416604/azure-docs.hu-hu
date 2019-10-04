---
title: Oktatóanyag – témakörök konfigurálása az Azure Service Bus, az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy Azure Service Bus-témakörbe az Ansible segítségével
keywords: az ansible, azure, devops, bash, forgatókönyv, a service bus, témakörök, előfizetések
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: ca8d849796520ac260d888d772c064316db68a30
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230861"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Oktatóanyag: Témakörök konfigurálása az Azure Service Bus, az Ansible-lel

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Üzenettémakör létrehozása
> * Előfizetés létrehozása
> * Hozzon létre egy SAS-szabályzat
> * Névtér-információk lekérése
> * A témakör és előfizetés-információk lekérése
> * Visszavonja a SAS-szabályzat

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>A Service Bus-témakör létrehozása

A forgatókönyv-mintakód a következő erőforrásokat hozza létre:
- Azure-erőforráscsoport
- Service Bus-névtér az erőforráscsoporton belül
- A névtér a Service Bus-témakörbe

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Az előfizetés létrehozása

A forgatókönyv-mintakód a Service Bus-témakörbe az előfizetést hoz létre. Az Azure Service Bus-üzenettémák több előfizetéssel is rendelkezhet. A témakör az előfizető is a témakörbe küldött minden üzenet másolatot kap. Előfizetések elnevezése entitások, amelyek tartósan jönnek létre, de igény szerint is lejár.

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>A SAS-szabályzat létrehozása

A [közös hozzáférésű Jogosultságkód (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) egy jogcímalapú engedélyezési mechanizmus jogkivonatok használatával. 

A forgatókönyv-mintakód két SAS-házirendeket a Service Bus-üzenetsort hoz létre különböző jogosultságokkal.

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_topic_policy.yml
```

## <a name="retrieve-namespace-information"></a>Névtér-információk lekérése

A forgatókönyv-mintakód a névtér-információkat kérdezi le.

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:
- A `show_sas_policies` érték azt jelzi, hogy a SAS-szabályzatok szerint a megadott névtér megjelenjen-e. Alapértelmezés szerint az érték a `False` további hálózati terhelés elkerülése érdekében.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>A témakör és előfizetés-információk lekérése

A forgatókönyv kód mintalekérdezései a következő információkat:
- A Service Bus témakör-információ
- A témakör előfizetés részleteinek listája
 
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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:
- A `show_sas_policies` érték azt jelzi, hogy a SAS-szabályzatok szerint a megadott várólista megjelenjen-e. Alapértelmezés szerint ez az érték van beállítva, `False` további hálózati terhelés elkerülése érdekében.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_list.yml
```

## <a name="revoke-the-queue-sas-policy"></a>A várólista SAS-szabályzat visszavonása

A forgatókönyv-mintakód egy üzenetsor SAS-szabályzat törlése.

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)