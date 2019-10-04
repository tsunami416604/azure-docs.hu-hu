---
title: Oktatóanyag – alkalmazások konfigurálása az Azure App Service az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy alkalmazást az Azure App Service-ben Java 8 és a Tomcat tároló-futtatókörnyezet
keywords: ansible, azure, devops, bash, forgatókönyv, Azure App Service, webalkalmazás, Java
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: aed09baf410ce25f2e5383aa746344a440e2a052
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231234"
---
# <a name="tutorial-configure-apps-in-azure-app-service-using-ansible"></a>Oktatóanyag: Alkalmazások konfigurálása az Azure App Service az Ansible-lel

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-app-service.md](../../includes/open-source-devops-intro-app-service.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Hozzon létre egy alkalmazást az Azure App Service-ben Java 8 és a Tomcat tároló-futtatókörnyezet
> * Az Azure Traffic Manager-profil létrehozása
> * A létrehozott alkalmazással Traffic Manager végpont definiálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-basic-app-service"></a>Hozzon létre egy alapszintű app service

A forgatókönyv ebben a szakaszban határozza meg azt az alábbi forrásanyagokat:

* Belül, amely az App Service-csomag és az alkalmazás telepítése Azure-erőforráscsoport
* Linux rendszeren Java 8- és a Tomcat tároló-futtatókörnyezet és az App Service-ben

Mentse a következő forgatókönyvet `firstwebapp.yml` néven:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook firstwebapp.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-and-use-azure-traffic-manager"></a>Hozzon létre egy alkalmazást, és az Azure Traffic Manager

[Az Azure Traffic Manager](/azure/app-service/web-sites-traffic-manager) segítségével szabályozható, hogyan oszlanak meg az Azure App Service Apps webes ügyfelektől érkező kérelmek. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, a Traffic Manager nyomon követi az App Service-alkalmazások állapotát. Az állapotok lehetnek: fut, leállítva és törölve. A TRAFFIC Manager segítségével döntse el, milyen végpontokat kell kapnia a forgalmat.

Az App Service-ben az alkalmazások [App Service-csomagban](/azure/app-service/overview-hosting-plans) futnak. App Service-csomag határozza meg az alkalmazás futtatásához használható számítási erőforrásokat. Különböző csoportokban kezelheti az App Service-csomagot és a webalkalmazást.

A forgatókönyv ebben a szakaszban határozza meg azt az alábbi forrásanyagokat:

* Belül, amely az App Service-csomagban üzembe helyezett Azure-erőforráscsoport
* App Service-csomag
* Azure erőforráscsoport belül, amely az alkalmazás telepítését
* Linux rendszeren Java 8- és a Tomcat tároló-futtatókörnyezet és az App Service-ben
* Traffic Manager-profil
* A létrehozott alkalmazással traffic Manager-végpont

Mentse a következő forgatókönyvet `webapp.yml` néven:

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook webapp.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create resource group for app service plan] 
changed: [localhost]

TASK [Create App Service Plan] 
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] 
changed: [localhost]

TASK [Get web app facts] 
ok: [localhost]

TASK [Create Traffic Manager Profile] 
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] 
changed: [localhost]

TASK [Get Traffic Manager Profile facts] 
ok: [localhost]

PLAY RECAP 
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Méretezhető alkalmazások az Azure App Service az Ansible-lel](/azure/ansible/ansible-scale-azure-web-apps)