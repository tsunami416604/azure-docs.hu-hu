---
title: Az Azure-hoz készült Ansible modul és Version Matrix
description: Az Azure-hoz készült Ansible modul és Version Matrix
keywords: Ansible, szerepkörök, mátrix, verzió, Azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 18654500a78178c46e72f9f6cd01e8507fa179f0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241541"
---
# <a name="ansible-module-and-version-matrix"></a>Az Ansible modul és a verziómátrix

A Ansible tartalmazza az Azure-erőforrások üzembe helyezéséhez és konfigurálásához használható modulok csomagját. Ezen erőforrások közé tartoznak a virtuális gépek, a méretezési csoportok, a hálózati szolgáltatások és a tároló szolgáltatások. Ez a cikk az Azure különböző Ansible-moduljait és a Ansible-verzióit sorolja fel.

## <a name="ansible-modules-for-azure"></a>Ansible-modulok az Azure-hoz

A következő modulok közvetlenül a távoli gazdagépeken vagy a forgatókönyvek használatával hajthatók végre.

Ezek a modulok a Ansible hivatalos kiadásában és a következő Microsoft forgatókönyv-szerepkörökben érhetők el.

| Ansible modul az Azure-hoz                   |  Ansible 2,4 |  Ansible 2,5 |  Ansible 2,6 | Ansible 2,7 | Ansible 2,8 | Ansible szerepkör | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Számítás**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_availabilityset_facts              | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_deployment                         | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_functionapp                        | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_functionapp_facts                  | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_image                              | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_resource                           | -            | -                           | Igen          | Igen          | Igen          | Igen          |
| azure_rm_resource_facts                     | -            | -                           | Igen          | Igen          | Igen          | Igen          |
| azure_rm_resourcegroup                      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_resourcegroup_facts                | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachine                     | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualmachineextension           | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_virtualmachineimage_facts          | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescaleset            | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescaleset_facts      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Igen          | Igen          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Igen          | Igen          |
| **Hálózat**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Igen          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Igen          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Igen          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Igen          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Igen          | Igen          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Igen          | Igen          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Igen          | Igen          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Igen          | Igen          |
| azure_rm_dnsrecordset                       | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_dnsrecordset_facts                 | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_dnszone                            | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_dnszone_facts                      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_loadbalancer                       | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_loadbalancer_facts                 | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_networkinterface                   | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_networkinterface_facts             | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_publicipaddress                    | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_publicipaddress_facts              | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_route                              | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_routetable                         | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_securitygroup                      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_subnet                             | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_virtualnetwork                     | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualnetwork_facts               | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Igen          | Igen          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_manageddisk_facts                  | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_storageaccount                     | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_storageaccount_facts               | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_storageblob                        | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_webapp                             | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Igen          | Igen          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_aks                                | -            | -                           | Igen          | Igen          | Igen          | Igen          |
| azure_rm_aks_facts                          | -            | -                           | Igen          | Igen          | Igen          | Igen          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_containerinstance                  | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_containerregistry                  | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Igen          | Igen          |
| **Adatbázisok**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mysqldatabase                      | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_mysqlserver                        | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_postgresqldatabase                 | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_postgresqlserver                   | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_sqldatabase                        | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Igen          | Igen          | Igen          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_sqlserver                          | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_sqlserver_facts                    | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| **Elemzés**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Igen          | Igen          |
| **Integrációs**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Igen          | Igen          |
| **Biztonság**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Igen          | Igen          |
| azure_rm_keyvaultkey                        | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_keyvaultsecret                     | -            | Igen                         | Igen          | Igen          | Igen          | Igen          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Igen          | Igen          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Igen          | Igen          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Igen          | Igen          | Igen          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Igen          | Igen          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Igen          | Igen          |

## <a name="introduction-to-playbook-role-for-azure"></a>Bevezetés az Azure-hoz készült ötletekbõl-szerepkörbe

A [azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) forgatókönyv-szerepkör tartalmazza az összes legújabb Azure-modult. A frissítések és a hibajavítások a hivatalos Ansible-kiadásnál jobban megvalósíthatók. Ha Ansible-t használ az Azure Resource kiépítési célokra, javasoljuk, hogy telepítse a `azure_preview_module` forgatókönyv-szerepkört.

A `azure_preview_module` forgatókönyv-szerepkör három hetente jelenik meg.

## <a name="next-steps"></a>Következő lépések

További információ a forgatókönyv-szerepkörökről: [újrafelhasználható](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)forgatókönyvek létrehozása. 
