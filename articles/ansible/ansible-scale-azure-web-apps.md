---
title: Az Azure App Service web Apps alkalmazások méretezése Ansible-lel
description: Megtudhatja, hogyan hozhat létre az Ansible használatával Linux rendszeren webalkalmazást Java 8-cal és Tomcat-tároló futtatókörnyezettel az App Service-ben.
ms.service: ansible
keywords: az ansible, azure, a devops, a bash, a forgatókönyv, az Azure App Service, webalkalmazás, méretezhető, a Java
author: tomarchermsft
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: a63289c3dec41217725199614420935532a04aa4
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051679"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Az Azure App Service web Apps alkalmazások méretezése Ansible-lel
[Az Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (vagy röviden Web Apps) gazdagépek webes alkalmazások, a REST API-k és a mobil háttérrendszer. Kedvenc nyelvén fejleszthet, legyen az&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP vagy Python.

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ez a cikk bemutatja, hogyan skálázhatja őket az Azure App Service-ben az Ansible használatával.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Az Azure App Service Web Apps** – Ha még nem rendelkezik egy Azure app service web appsban, [Azure web Apps alkalmazások létrehozása az Ansible használatával](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Az App Service-alkalmazás vertikális felskálázása
Akkor is vertikális felskálázáshoz módosítsa az alkalmazáshoz tartozó App Service-csomag tarifacsomagját. Ez a szakasz bemutatja egy minta Ansible-forgatókönyvek, amely meghatározza a művelet a következő:
- Egy meglévő App Service-csomagot a tények beolvasása
- Az App service-csomag frissítése s2 három feldolgozók használata

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
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

Mentse a forgatókönyvet, *webapp_scaleup.yml*.

A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook webapp_scaleup.yml
```

A forgatókönyv futtatása után az alábbi példához hasonló kimenetet jeleníti meg, hogy az App service-csomag frissítése sikerült s2 három feldolgozók használata:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ansible az Azure-on](https://docs.microsoft.com/azure/ansible/)