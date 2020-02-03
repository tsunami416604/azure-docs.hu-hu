---
title: Oktatóanyag – várólisták konfigurálása Azure Service Bus Ansible használatával
description: Útmutató Azure Service Bus üzenetsor létrehozásához a Ansible használatával
keywords: Ansible, Azure, devops, bash, ötletekbõl, Service Bus, üzenetsor
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713222"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Oktatóanyag: várólisták konfigurálása Azure Service Bus Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Várólista létrehozása
> * SAS-plicy létrehozása
> * Névtér adatainak beolvasása
> * Várólista-információk lekérése
> * A várólista SAS-szabályzatának visszavonása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>Az Service Bus üzenetsor létrehozása

A példaként szolgáló ötletekbõl-kód a következő erőforrásokat hozza létre:
- Azure-erőforráscsoport
- Service Bus névtér az erőforráscsoport belül
- Service Bus üzenetsor a névtérrel

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>SAS-szabályzat létrehozása

A [közös hozzáférésű aláírás (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) a jogkivonatokat használó jogcím-alapú engedélyezési mechanizmus. 

A példaként szolgáló ötletekbõl-kód két SAS-szabályzatot hoz létre egy Service Bus üzenetsor számára, különböző jogosultságokkal.

Mentse a következő forgatókönyvet `servicebus_queue_policy.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:
- A `rights` érték azt a jogosultságot jelöli, amelyet a felhasználó a várólistával rendelkezik. A következő értékek egyikét kell megadnia: `manage`, `listen`, `send`vagy `listen_send`.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_queue_policy.yml
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
- A `show_sas_policies` érték azt jelzi, hogy megjelenjenek-e a SAS-szabályzatok a megadott névtérben. Alapértelmezés szerint az érték `False` a további hálózati terhelés elkerülése érdekében.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## <a name="retrieve-queue-information"></a>Várólista-információk lekérése

A minta forgatókönyv-kód lekérdezési várólistájának adatai. 

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

A forgatókönyv futtatása előtt tekintse meg a következő megjegyzéseket:
- A `show_sas_policies` érték azt jelzi, hogy megjelenjenek-e a SAS-szabályzatok a megadott várólistában. Alapértelmezés szerint ez az érték `False`re van állítva a további hálózati terhelés elkerülése érdekében.

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_queue_info.yml
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
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen privilege
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat. 

Mentse a következő kódot `cleanup.yml`ként:

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"] 
> [Oktatóanyag: témakör konfigurálása Azure Service Bus Ansible használatával](ansible-service-bus-topic-configure.md)