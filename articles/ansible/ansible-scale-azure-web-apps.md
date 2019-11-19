---
title: Oktatóanyag – alkalmazások méretezése Azure App Service a Ansible használatával
description: Ismerje meg, hogyan méretezhető egy alkalmazás a Azure App Service
keywords: Ansible, Azure, devops, bash, ötletekbõl, Azure App Service, webalkalmazás, skála, Java
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: 9eb50922361c817de8047dece4849a9b221677f0
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155915"
---
# <a name="tutorial-scale-apps-in-azure-app-service-using-ansible"></a>Oktatóanyag: alkalmazások méretezése Azure App Service Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Meglévő App Service csomaggal kapcsolatos tények beolvasása
> * A App Service-terv vertikális felskálázása S2-re három feldolgozóval

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Azure app Service alkalmazás** – ha nem rendelkezik Azure app Service alkalmazással, [a Ansible használatával konfiguráljon egy alkalmazást a Azure app Serviceban](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app"></a>Alkalmazás vertikális felskálázása

Két munkafolyamat áll rendelkezésre a skálázáshoz *: vertikális felskálázás és* horizontális felskálázás *.*

Vertikális **felskálázás:** A vertikális felskálázáshoz több erőforrást kell beszerezni. Ilyen erőforrások például a processzor, a memória, a lemezterület, a virtuális gépek és egyebek. Az alkalmazás vertikális felskálázása az App Service terv díjszabási szintjének módosításával, amelyhez az alkalmazás tartozik. 
Vertikális **felskálázás:** A kiskálázáshoz az alkalmazást futtató virtuálisgép-példányok számának növelését jelenti. A App Service csomag díjszabási szintjétől függően akár 20 példányra is kibővíthető. Az automatikus [skálázás](/azure/azure-monitor/platform/autoscale-get-started) lehetővé teszi, hogy az előre meghatározott szabályok és ütemtervek alapján automatikusan méretezheti a példányszámot.

Az ebben a szakaszban szereplő forgatókönyv-kód a következő műveletet határozza meg:

* Meglévő App Service csomaggal kapcsolatos tények beolvasása
* Az App Service-csomag frissítése S2-re három feldolgozóval

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Ansible az Azure-on](/azure/ansible/)