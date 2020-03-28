---
title: Oktatóanyag – Témakörök konfigurálása az Azure Service Bus ban az Ansible használatával
description: Ismerje meg, hogyan hozhat létre az Ansible segítségével egy Azure Service Bus-témakört
keywords: ansible, azúr, devops, bash, ötletekbõl, service bus, témák, előfizetések
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: d07c7622043353a79d5a82994c2fab4f0835b453
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155798"
---
# <a name="tutorial-configure-topics-in-azure-service-bus-using-ansible"></a>Oktatóanyag: Témakörök konfigurálása az Azure Service Bus ban az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Üzenettémakör létrehozása
> * Előfizetés létrehozása
> * SAS-házirend létrehozása
> * Névtéradatok lekérése
> * Téma- és előfizetési adatok lekérése
> * SAS-házirend visszavonása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-topic"></a>A Service Bus témakör létrehozása

A minta forgatókönyvkód a következő erőforrásokat hozza létre:
- Azure-erőforráscsoport
- Service Bus névtér az erőforráscsoporton belül
- A Service Bus témaköre a névtérrel

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_topic.yml
```

## <a name="create-the-subscription"></a>Az előfizetés létrehozása

A minta forgatókönyv-kód létrehozza az előfizetést egy Service Bus témakörben. Az Azure Service Bus-témakörök több előfizetéssel is rendelkezhetnek. A témakör előfizetője minden, a témakörnek küldött üzenetről másolatot kaphat. Az előfizetések nevesített entitások, amelyek tartósan létre, de opcionálisan lejár.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_subscription.yml
```

## <a name="create-the-sas-policy"></a>A SAS-házirend létrehozása

A [megosztott hozzáférésű aláírás (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) jogcímalapú engedélyezési mechanizmus jogkivonatokat használó. 

A minta forgatókönyvkód két SAS-házirendet hoz létre egy különböző jogosultságokkal rendelkező Service Bus-várólistához.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_topic_policy.yml
```

## <a name="retrieve-namespace-information"></a>Névtéradatok lekérése

A minta forgatókönyvkód lekérdezi a névtér adatait.

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:
- Az `show_sas_policies` érték azt jelzi, hogy a megadott névtérben megjelenjenek-e a SAS-házirendek. Alapértelmezés szerint az `False` érték a további hálózati terhelés elkerülése.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-topic-and-subscription-information"></a>Téma- és előfizetési adatok lekérése

A minta forgatókönyv-kód lekérdezések a következő információkat:
- A Service Bus témaköradatai
- A témakör előfizetési részleteinek listája
 
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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:
- Az `show_sas_policies` érték azt jelzi, hogy a megadott várólista alatt megjelenjenek-e a SAS-házirendek. Alapértelmezés szerint ez az `False` érték úgy van beállítva, hogy elkerülje a további hálózati terhelést.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_list.yml
```

## <a name="revoke-the-queue-sas-policy"></a>A várólista SAS-házirendjének visszavonása

A minta forgatókönyvkód törli a várólista SAS-házirend.

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_topic_policy_delete.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő `cleanup.yml`kódot:

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)