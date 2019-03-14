---
title: Az Azure web Apps alkalmazások létrehozása az Ansible segítségével
description: Megtudhatja, hogyan hozhat létre az Ansible használatával Linux rendszeren webalkalmazást Java 8-cal és Tomcat-tároló futtatókörnyezettel az App Service-ben.
ms.service: azure
keywords: ansible, azure, devops, bash, forgatókönyv, Azure App Service, webalkalmazás, Java
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 5f67a9f7d629eec9ab1462a25940355869c1cd28
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791222"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Az Azure App Service web Apps alkalmazások létrehozása az Ansible segítségével
[Az Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/overview) (vagy röviden Web Apps) gazdagépek webes alkalmazások, a REST API-k és a mobilalkalmazások háttérkomponensei. Kedvenc nyelvén fejleszthet, legyen az&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP vagy Python.

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ebből a cikkből megtudhatja, hogyan hozhat létre az Ansible segítségével webalkalmazásokat Java futtatókörnyezet használatával. 

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.7-es verziója szükséges.

## <a name="create-a-simple-app-service"></a>Egyszerű App Service létrehozása
Ez a szakasz egy Ansible-mintaforgatókönyvet mutat be, amely az alábbi erőforrásokat határozza meg:
- Erőforráscsoport, amelyben az App Service-csomagot és a webalkalmazást üzembe helyezi
- Webalkalmazás Java 8-cal és a Tomcat-tároló futtatókörnyezettel az App Service-ben, Linux rendszeren

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
Mentse a fenti forgatókönyvet **firstwebapp.yml** néven.

A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook firstwebapp.yml
```

Az Ansible-forgatókönyv futtatásából származó kimenetben látható, hogy a webalkalmazás sikeresen létrejött:

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>App Service létrehozása a Traffic Manager használatával
Az [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) használatával irányíthatja, hogyan legyenek elosztva a webes ügyfelektől származó kérések az alkalmazások között az Azure App Service-ben. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, a Traffic Manager nyomon követi az App Service-alkalmazások állapotát. Az állapotok lehetnek: fut, leállítva és törölve. A Traffic Manager így eldöntheti, hogy melyik végpontok fogadják a forgalmat.

Az App Service-ben az alkalmazások [App Service-csomagban](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
) futnak. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait. Különböző csoportokban kezelheti az App Service-csomagot és a webalkalmazást.

Ez a szakasz egy Ansible-mintaforgatókönyvet mutat be, amely az alábbi erőforrásokat határozza meg:
- Erőforráscsoport, amelyben az App Service-csomagot üzembe helyezi
- App Service-csomag
- Másodlagos erőforráscsoport, amelyben a webalkalmazást üzembe helyezi
- Webalkalmazás Java 8-cal és a Tomcat-tároló futtatókörnyezettel az App Service-ben, Linux rendszeren
- Traffic Manager-profil
- Traffic Manager-végpont, a létrehozott webhellyel

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
Mentse a fenti forgatókönyvet **webapp.yml** néven, vagy [töltse le a forgatókönyvet](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook webapp.yml
```

Az Ansible-forgatókönyv futtatásából származó kimenetben látható, hogy az App Service-csomag, a webalkalmazás, a Traffic Manager-profil és a végpont sikeresen létrejött:
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Az Azure App Service web Apps alkalmazások méretezése Ansible-lel](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)