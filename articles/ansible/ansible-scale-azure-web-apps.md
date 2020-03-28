---
title: Oktatóanyag – Alkalmazások méretezése az Azure App Service-ben az Ansible használatával
description: Megtudhatja, hogyan skálázhat fel egy alkalmazást az Azure App Service-ben
keywords: ansible, azúr, devops, bash, ötletekbõl, Azure App Service, Web App, skála, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155915"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Oktatóanyag: Alkalmazások méretezése az Azure App Service-ben az Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Meglévő App Service-csomag tényeinek megismerése
> * Az App Service-csomag felskálázása S2-re három dolgozóval

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure App Service-alkalmazás** – Ha nem rendelkezik Azure App Service-alkalmazással, [konfiguráljon egy alkalmazást az Azure App Service-ben az Ansible használatával.](ansible-create-configure-azure-web-apps.md)

## <a name="scale-up-an-app"></a>Alkalmazás felskálázása

A skálázáshoz két munkafolyamat létezik: *felskálázás* és *horizontális felskálázás.*

**Felskálázás:** A felskálázás több erőforrás megszerzését jelenti. Ezek az erőforrások közé tartozik a PROCESSZOR, a memória, a lemezterület, a virtuális gépek és egyebek. Egy alkalmazást úgy skáláz, hogy módosítja annak az App Service-csomagnak a tarifacsomagját, amelyhez az alkalmazás tartozik. 
**Horizontális felskálázás:** Horizontális felskálázás azt jelenti, hogy az alkalmazás futtatásához futó virtuálisgép-példányok számának növelése. Az App Service-csomag tarifacsomagtól függően akár 20 példányra is skálázható. [Automatikus skálázás](/azure/azure-monitor/platform/autoscale-get-started) lehetővé teszi, hogy a példányok számának automatikus méretezése előre meghatározott szabályok és ütemezések alapján.

Ebben a szakaszban a forgatókönyv-kód a következő műveletet határozza meg:

* Meglévő App Service-csomag tényeinek megismerése
* Az App Service-csomag frissítése S2-re három dolgozóval

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

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook webapp_scaleup.yml
```

A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

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