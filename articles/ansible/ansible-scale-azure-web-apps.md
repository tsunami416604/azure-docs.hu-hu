---
title: Oktatóanyag – méretezési alkalmazások az Azure App Service az Ansible-lel |} A Microsoft Docs
description: 'Útmutató: az Azure App Service alkalmazás vertikális felskálázása'
keywords: az ansible, azure, a devops, a bash, a forgatókönyv, az Azure App Service, webalkalmazás, méretezhető, a Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 213c4e086db8b40fdec26ce9fb3e0be5ad055cbc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729426"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Oktatóanyag: Méretezhető alkalmazások az Azure App Service az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Egy meglévő App Service-csomagot a tények beolvasása
> * Az App Service-csomag vertikális s2 három feldolgozók használata

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Az Azure App Service-alkalmazás** – Ha nem rendelkezik az Azure App Service-alkalmazások, [konfigurálhat egy alkalmazást az Azure App Service az Ansible-lel](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Alkalmazás vertikális felskálázása

Két munkafolyamatok skálázásához: *vertikális felskálázás* és *horizontális felskálázása*.

**Vertikális felskálázás:** Vertikális felskálázás azt jelenti, hogy további erőforrást szerez. Ilyen erőforrások többek között a CPU, memória, lemezterület, virtuális gépek és egyéb. Alkalmazás vertikális felskálázáshoz módosítsa az alkalmazást, amelyhez tartozik az App Service-csomag tarifacsomagját. 
**Horizontális felskálázás:** Horizontális felskálázás azt jelenti, hogy az alkalmazást futtató Virtuálisgép-példányok számának növelése. Az App Service-csomag tarifacsomagját, attól függően, horizontálisan akár 20-példányokhoz. [Az automatikus skálázás](/azure/azure-monitor/platform/autoscale-get-started) példányok száma automatikusan alapján előre meghatározott szabályok és az ütemezések segítségével.

A forgatókönyv ebben a szakaszban határozza meg azt a következő műveletet:

* Egy meglévő App Service-csomagot a tények beolvasása
* Az App service-csomag frissítése s2 három feldolgozók használata

Mentse a következő forgatókönyvet `webapp_scaleup.yml` néven:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App service plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook webapp_scaleup.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get facts of existing App service plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] 
changed: [localhost]

TASK [Get facts] 
ok: [localhost]

TASK [debug] 
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP 
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)