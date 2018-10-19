---
title: Azure-webalkalmazás létrehozása az Ansible (előzetes verzió) használatával
description: Megtudhatja, hogy az Ansible használatával Linux rendszeren hogyan hozhat létre webalkalmazást Java 8-cal és Tomcat-tároló futtatókörnyezettel az App Service-ben
ms.service: ansible
keywords: ansible, azure, devops, bash, forgatókönyv, Azure App Service, webalkalmazás, Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 1899b1fc1e0a38d859fb3a7ce2153585579650f3
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586668"
---
# <a name="create-azure-app-service-web-apps-using-ansible-preview"></a>Azure App Service Web Apps létrehozása az Ansible (előzetes verzió) használatával
Az [Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (vagy röviden Web Apps) webalkalmazásokat, REST API-kat és mobilháttereket üzemeltető szolgáltatás. Kedvenc nyelvén fejleszthet, legyen az .NET, .NET Core, Java, Ruby, Node.js, PHP, vagy Python.

Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Ebből a cikkből megtudhatja, hogyan hozhat létre webalkalmazást Java futtatókörnyezettel az Ansible használatával. 

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.7-es verziója szükséges. A `sudo pip install ansible[azure]==2.7.0rc2` parancs futtatásával telepítheti az Ansible 2.7 RC verziót. Az Ansible 2.7 2018 októberében válik elérhetővé. Ezt követően nem kell megadnia a verziót, mert az alapértelmezett verzió a 2.7-es lesz. 

## <a name="create-a-simple-app-service"></a>Egyszerű App Service létrehozása
Ez a szakasz egy Ansible-mintaforgatókönyvet mutat be, amely az alábbi erőforrásokat határozza meg:
- Erőforráscsoport, amelyben az App Service-csomagot és a webalkalmazást üzembe helyezi
- Webalkalmazás, egy webalkalmazás Java 8-cal és Tomcat-tároló futtatókörnyezettel az App Service-ben, Linux rendszeren

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myfirstResourceGroup
    webapp_name: myfirstWebApp
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
          name: myappplan
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
Mentse a fenti forgatókönyvet firstwebapp.yml néven.

A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook firstwebapp.yml
```

Az Ansible-forgatókönyv futtatásából származó kimenetben látható, hogy a webalkalmazás sikeresen létrejött:

```
TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ******************************
 [WARNING]: Azure API profile latest does not define an entry for
WebSiteManagementClient
changed: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0   
```

## <a name="create-app-service-with-traffic-manager"></a>App Service létrehozása a Traffic Manager használatával
Az [Azure Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) használatával irányíthatja, hogyan legyenek elosztva a webes ügyfelektől származó kérések az alkalmazások között az Azure App Service-ben. Amikor App Service-végpontokat ad egy Azure Traffic Manager-profilhoz, az Azure Traffic Manager nyomon követi az App Service-alkalmazások állapotát (fut, leállítva vagy törölve), így eldöntheti, hogy melyik végpont kapja a forgalmat.

Az App Service-ben az alkalmazások [App Service-csomagban](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview
) futnak. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait. Különböző csoportokban kezelheti az App Service-csomagot és a webalkalmazást.

Ez a szakasz egy Ansible-mintaforgatókönyvet mutat be, amely az alábbi erőforrásokat határozza meg:
- Erőforráscsoport, amelyben az App Service-csomagot üzembe helyezi
- App Service-csomag
- Másodlagos erőforráscsoport, amelyben a webalkalmazást üzembe helyezi
- Webalkalmazás, egy webalkalmazás Java 8-cal és Tomcat-tároló futtatókörnyezettel az App Service-ben, Linux rendszeren
- Traffic Manager-profil
- Traffic Manager-végpont, létrehozott webhellyel

```
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_resource_group: planResourceGroup
    app_name: myLinuxWebApp
    location: eastus
    linux_plan_name: myAppServicePlan
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ plan_resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ plan_resource_group }}"
      name: "{{ linux_plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ app_name }}"
        plan:
          resource_group: "{{ plan_resource_group }}"
          name: "{{ linux_plan_name }}"
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
      resource_group: "{{ resource_group }}"
      name: "{{ app_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group }}"
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
      resource_group: "{{ resource_group }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"

```
Mentse a fenti forgatókönyvet webapp.yml néven, vagy [töltse le a forgatókönyvet](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

A forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:
```bash
ansible-playbook webapp.yml
```

Az Ansible-forgatókönyv futtatásából származó kimenetben látható, hogy az App Service-csomag, a webalkalmazás, a Traffic Manager-profil és a végpont sikeresen létrejött:
```
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
> [Ansible az Azure-on](https://docs.microsoft.com/azure/ansible/)