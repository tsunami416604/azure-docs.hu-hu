---
title: Oktatóanyag – gyorsítótárak konfigurálása az Azure Cache redis Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre méretezhető, és indítsa újra az Azure Cache redis Ansible használatával
keywords: az ansible, azure, devops, bash, forgatókönyv, gyorsítótár, a redis
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 693b042f631044352eaae708905fefca02f38415
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231023"
---
# <a name="tutorial-configure-caches-in-azure-cache-for-redis-using-ansible"></a>Oktatóanyag: Gyorsítótárak Azure Cache-ben a Redis használata az Ansible konfigurálása

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Az Azure Cache redis](/azure/azure-cache-for-redis/) egy nyílt forráskódú kompatibilis szolgáltatás, amely lehetővé teszi, hogy az adatok gyors hozzáférés biztosítása révén adatszinkronizálást biztosító alkalmazások készítése. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Gyorsítótár létrehozása
> * Gyorsítótár méretezése
> * Gyorsítótár újraindítása
> * Vegyen fel egy tűzfalszabályt a Cache-gyorsítótárhoz
> * A gyorsítótár törlése

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-cache"></a>Gyorsítótár létrehozása

Hozzon létre egy Azure Cache Redis belül egy új erőforráscsoportot.

```yml
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1 
```

Cache gyorsítótár üzembe helyezése több percet is igénybe vehet. Az alábbi kód közli az Ansible várjon a művelet elvégzéséhez:

```yml
  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

A hosszú üzembe helyezési folyamat során több "error" üzenet jelenik meg. Ezek az üzenetek biztonságosan figyelmen kívül hagyhatja. A fontos üzenet az utolsó üzenet. Az alábbi példában nincsenek számos hibaüzenetek csak az utolsó ("ok") üzenet.

```Output
FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]
```

## <a name="scale-the-cache"></a>A gyorsítótár méretezése

Az Azure Cache redis különböző ajánlott az alkalmazás szükségleteinek rendelkezik. Ezek a gyorsítótár-beállítások a gyorsítótár méretét és a szolgáltatások választható rugalmasságot biztosítanak. Ha az alkalmazás követelményei a gyorsítótár létrehozása után módosíthatja, igény szerint méretezhető a gyorsítótárban. Méretezésével kapcsolatos további információkért lásd: [How to Scale Azure Cache redis](/azure/azure-cache-for-redis/cache-how-to-scale).

Az alábbi mintakód méretezi a gyorsítótár **Standard**:

```yml
- name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1
```

Gyorsítótár méretezése több percet is igénybe vehet. Az alábbi kód közli az Ansible várjon a művelet elvégzéséhez:

```yml
  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60
```

A feladat üzembe helyezni Azure Cache redis hasonlóan kimenet a következő üzenet például a normál:

```Ouput
**FAILED - RETRYING: Get facts (100 retries left)** is normal.
```

## <a name="reboot-the-cache"></a>Indítsa újra a gyorsítótárhoz

A következő kódot a korábbi szakaszokban létrehozott gyorsítótárak újraindul.

```yml
  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all
```

### <a name="add-firewall-rule"></a>Tűzfalszabály hozzáadása

A következő kódot egy tűzfalszabályt a gyorsítótár ad hozzá:

```yml
  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4
```

## <a name="delete-the-cache"></a>A gyorsítótár törlése

A következő kódot a gyorsítótár törlése:

```yml
  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="get-the-sample-playbook"></a>A mintául szolgáló forgatókönyvek beolvasása

A teljes minta forgatókönyv beolvasásához két módja van:
- [Töltse le a forgatókönyv](https://github.com/Azure-Samples/ansible-playbooks/blob/master/rediscache.yml) , és mentse a `rediscache.yml`.
- Hozzon létre egy új fájlt `rediscache.yml` és másolja bele a következő tartalommal:

```yml
- name: Manage Azure Cache for Redis
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    redis_name: "redis{{ resource_group_name }}"
    location: eastus2

  roles:
    - azure.azure_preview_modules

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: basic
        size: C1

  - name: Wait for Redis provisioning to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Scale up Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      sku:
        name: standard
        size: C1

  - name: Wait for Redis scaling up to complete
    azure_rm_rediscache_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
    register: facts
    until: "{{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}"
    retries: 100
    delay: 60

  - name: Reboot Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      reboot:
        reboot_type: all

  - name: Add Firewall rule
    azure_rm_rediscachefirewallrule:
      resource_group: "{{ resource_group }}"
      cache_name: "{{ redis_name }}"
      name: rule1
      start_ip_address: 168.1.1.1
      end_ip_address: 168.1.1.4

  - name: Delete Azure Cache for Redis
    azure_rm_rediscache:
      resource_group: "{{ resource_group }}"
      name: "{{ redis_name }}"
      state: absent
```

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ebben a szakaszban a forgatókönyv teszteléséhez a jelen cikkben ismertetett különféle szolgáltatások futtatása.

Az a `vars` szakaszban, cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevét.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook rediscache.yml
```

A kimenet a következő eredményeket hasonlóan néz ki:

```Output
TASK [create resource group] 
Tuesday 12 March 2019  16:21:07 +0800 (0:00:00.054)       0:00:01.503 
ok: [localhost]

TASK [Create Azure Cache for Redis] 
Tuesday 12 March 2019  16:21:09 +0800 (0:00:01.950)       0:00:03.454 
 [WARNING]: Azure API profile latest does not define an entry for RedisManagementClient

changed: [localhost]

TASK [Dump host name] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:40.125)       0:00:43.580 
ok: [localhost] =>
  output['host_name']: redis0312.redis.cache.windows.net

TASK [Get facts] 
Tuesday 12 March 2019  16:21:49 +0800 (0:00:00.056)       0:00:43.636 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
FAILED - RETRYING: Get facts (90 retries left).
ok: [localhost]

TASK [Scale up Azure Cache for Redis] 
Tuesday 12 March 2019  16:33:20 +0800 (0:11:31.296)       0:12:14.933 
changed: [localhost]

TASK [Get facts] 
Tuesday 12 March 2019  16:33:29 +0800 (0:00:09.164)       0:12:24.097 
 [WARNING]: conditional statements should not include jinja2 templating delimiters such as {{ }} or {% %}. Found: {{ facts.rediscaches[0]['provisioning_state'] == 'Succeeded' }}

FAILED - RETRYING: Get facts (100 retries left).
FAILED - RETRYING: Get facts (99 retries left).
FAILED - RETRYING: Get facts (98 retries left).
FAILED - RETRYING: Get facts (97 retries left).
FAILED - RETRYING: Get facts (96 retries left).
FAILED - RETRYING: Get facts (95 retries left).
FAILED - RETRYING: Get facts (94 retries left).
FAILED - RETRYING: Get facts (93 retries left).
FAILED - RETRYING: Get facts (92 retries left).
FAILED - RETRYING: Get facts (91 retries left).
ok: [localhost]

TASK [Reboot Azure Cache for Redis] 
Tuesday 12 March 2019  16:43:57 +0800 (0:10:27.740)       0:22:51.838 
ok: [localhost]

TASK [Add Firewall rule] 
Tuesday 12 March 2019  16:44:02 +0800 (0:00:05.432)       0:22:57.271 
changed: [localhost]

TASK [Delete Azure Cache for Redis] 
Tuesday 12 March 2019  16:44:08 +0800 (0:00:05.137)       0:23:02.409 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=10   changed=4    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

Tuesday 12 March 2019  16:44:14 +0800 (0:00:06.217)       0:23:08.626 

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A következő kód, Mentés `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Az a `vars` szakaszban, cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevét.

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](https://docs.microsoft.com/azure/ansible/)