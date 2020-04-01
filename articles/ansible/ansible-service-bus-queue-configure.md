---
title: Oktatóanyag – Várólisták konfigurálása az Ansible használatával az Azure Service Busban
description: Ismerje meg, hogyan hozhat létre az Ansible segítségével egy Azure Service Bus-várólistát
keywords: ansible, azúr, devops, bash, ötletekbõl, szolgáltatás busz, sorban
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 8ba4c2296d903c4f35aa36eb92dfbc3b56ec4b18
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76713222"
---
# <a name="tutorial-configure-queues-in-azure-service-bus-using-ansible"></a>Oktatóanyag: Várólisták konfigurálása az Azure Service Bus ban az Ansible használatával

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-servicebus.md](../../includes/open-source-devops-intro-servicebus.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Üzenetsor létrehozása
> * SAS-kondisz létrehozása
> * Névtéradatok lekérése
> * Várólista adatainak beolvasása
> * A várólista SAS-házirendjének visszavonása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-service-bus-queue"></a>A Service Bus-várólista létrehozása

A minta forgatókönyvkód a következő erőforrásokat hozza létre:
- Azure-erőforráscsoport
- Service Bus névtér az erőforráscsoporton belül
- Service Bus-várólista a névtérrel

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_queue.yml
```

## <a name="create-the-sas-policy"></a>A SAS-házirend létrehozása

A [megosztott hozzáférésű aláírás (SAS)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) jogcímalapú engedélyezési mechanizmus jogkivonatokat használó. 

A minta forgatókönyvkód két SAS-házirendet hoz létre egy különböző jogosultságokkal rendelkező Service Bus-várólistához.

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:
- Az `rights` érték azt a jogosultságot jelöli, amelyet a felhasználó a várólistával kapcsolatban birtokol. Adja meg a következő `manage` `listen`értékek `send`egyikét: , , , vagy `listen_send`.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_queue_policy.yml
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

## <a name="retrieve-queue-information"></a>Várólista adatainak beolvasása

A minta forgatókönyvkód lekérdezések várólista adatait. 

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

A forgatókönyv futtatása előtt tekintse meg az alábbi megjegyzéseket:
- Az `show_sas_policies` érték azt jelzi, hogy a megadott várólista alatt megjelenjenek-e a SAS-házirendek. Alapértelmezés szerint ez az `False` érték úgy van beállítva, hogy elkerülje a további hálózati terhelést.

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_queue_info.yml
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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Oktatóanyag: Témakör konfigurálása az Azure Service Bus ban az Ansible használatával](ansible-service-bus-topic-configure.md)