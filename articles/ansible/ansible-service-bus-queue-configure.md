---
title: Oktatóanyag – üzenetsorok konfigurálása az Azure Service Bus, az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure Service Bus-üzenetsorba a Ansible használatával
keywords: ansible, azure, devops, bash, playbook, service bus, queue
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 6efc11106fae18beac43ab1896733ab6bfc64dad
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230772"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Oktatóanyag: Üzenetsorok konfigurálása az Azure Service Bus, az Ansible-lel

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Üzenetsor létrehozása
> * Hozzon létre egy SAS plicy
> * Névtér-információk lekérése
> * Üzenetsor-információk lekérése
> * A várólista SAS-szabályzat visszavonása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>A Service Bus-üzenetsor létrehozása

A forgatókönyv-mintakód a következő erőforrásokat hozza létre:
- Azure-erőforráscsoport
- Service Bus-névtér az erőforráscsoporton belül
- A névtér a Service Bus-üzenetsorba

Mentse a következő forgatókönyvet `servicebus_queue.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>A SAS-szabályzat létrehozása

A [közös hozzáférésű Jogosultságkód (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) egy jogcímalapú engedélyezési mechanizmus jogkivonatok használatával. 

A forgatókönyv-mintakód két SAS-házirendeket a Service Bus-üzenetsort hoz létre különböző jogosultságokkal.

Mentse a következő forgatókönyvet `servicebus_queue_policy.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:
- A `rights` érték jelzi a jogosultságot a felhasználó rendelkezik az üzenetsorhoz. Adja meg a következő értékek egyikét: `manage`, `listen`, `send`, vagy `listen_send`.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_queue_policy.yml
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

## <a name="retrieve-queue-information"></a>Üzenetsor-információk lekérése

A forgatókönyv kód mintalekérdezések várólista-információkat. 

Mentse a következő forgatókönyvet `servicebus_queue_info.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzések:
- A `show_sas_policies` érték azt jelzi, hogy a SAS-szabályzatok szerint a megadott várólista megjelenjen-e. Alapértelmezés szerint ez az érték van beállítva, `False` további hálózati terhelés elkerülése érdekében.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_queue_info.yml
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
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
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
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
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
> [Oktatóanyag: A témakör konfigurálása az Azure Service Bus, az Ansible-lel](ansible-service-bus-topic-configure.md)