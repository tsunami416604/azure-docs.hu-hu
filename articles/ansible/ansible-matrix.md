---
title: Ansible modul és Version Matrix az Azure-hoz | Microsoft Docs
description: Az Azure-hoz készült Ansible modul és Version Matrix
keywords: Ansible, szerepkörök, mátrix, verzió, Azure, devops
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155984"
---
# <a name="ansible-module-and-version-matrix"></a>Az Ansible modul és a verziómátrix

A Ansible tartalmazza az Azure-erőforrások üzembe helyezéséhez és konfigurálásához használható modulok csomagját. Ezen erőforrások közé tartoznak a virtuális gépek, a méretezési csoportok, a hálózati szolgáltatások és a tároló szolgáltatások. Ez a cikk az Azure különböző Ansible-moduljait és a Ansible-verzióit sorolja fel.

## <a name="ansible-modules-for-azure"></a>Ansible-modulok az Azure-hoz

A következő modulok közvetlenül a távoli gazdagépeken vagy a forgatókönyvek használatával hajthatók végre.  

Ezek a modulok a Ansible hivatalos kiadásában és a következő Microsoft forgatókönyv-szerepkörökben érhetők el.

> [!NOTE]
> A Ansible 2,9-től kezdődően az összes * _facts modult átnevezték * _infora, hogy megfeleljenek a Ansible elnevezési konvenciónak. A régi és az átnevezett modulok úgy vannak összekapcsolva, hogy elavult figyelmeztetést jelenítenek meg, az összes modul pedig a korábban leírtaknak megfelelően működik.

| Ansible modul az Azure-hoz                   |  Ansible 2,4 |  Ansible 2,5 |  Ansible 2,6 | Ansible 2,7 | Ansible 2,8 | Ansible 2,9 | Ansible szerepkör | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Számítás**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_availabilityset_info              | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_deployment                         | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_functionapp                        | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_functionapp_info                  | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_image                              | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_resource                           | -            | -                           | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_resource_info                     | -            | -                           | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_resourcegroup                      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_resourcegroup_info                | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_virtualmachine                     | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachineextension            | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualmachineimage_info          | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescaleset             | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescaleset_info       | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Igen          | Igen          | Igen          |
| **Hálózat**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| azure_rm_dnsrecordset                       | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_dnsrecordset_info                 | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_dnszone                            | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_dnszone_info                      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_loadbalancer                       | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_loadbalancer_info                 | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_networkinterface                   | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_networkinterface_info             | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_publicipaddress                    | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_publicipaddress_info              | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_route                              | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_routetable                         | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_routetable_info                   | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_securitygroup                      | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_subnet                             | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualnetwork                     | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualnetwork_info               | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Igen          | Igen          |
| **Tárolás**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_manageddisk_info                  | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_storageaccount                     | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_storageaccount_info               | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_storageblob                        | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_webapp                             | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_webapp_info                       | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Igen          | Igen          | Igen          |
| **Tárolók**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Igen          | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_aks                                | -            | -                           | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_aks_info                          | -            | -                           | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_containerinstance                  | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_containerregistry                  | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Igen          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Igen          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Igen          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Igen          |
| **Adatbázisok**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mysqldatabase                      | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_mysqlserver                        | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_postgresqldatabase                 | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_postgresqlserver                   | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_sqldatabase                        | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Igen          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Igen          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Igen          | Igen          | Igen          | Igen          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_sqlserver                          | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_sqlserver_info                    | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| **Elemzés**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Igen          | Igen          |
| **Integrációs**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| **Biztonság**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Igen          | Igen          | Igen          |
| azure_rm_keyvaultkey                        | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_keyvaultsecret                     | -            | Igen                         | Igen          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Igen          | Igen          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Igen          | Igen          | Igen          | Igen          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Igen          | Igen          | Igen          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Igen          | Igen          |
| **Felügyelet és irányítás**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Igen        | Igen          |
| **Eszközök internetes hálózata**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Igen        | Igen          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Igen        | Igen          |

## <a name="introduction-to-playbook-role-for-azure"></a>Bevezetés az Azure-hoz készült ötletekbõl-szerepkörbe

A [azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) forgatókönyv-szerepkör tartalmazza az összes legújabb Azure-modult. A frissítések és a hibajavítások a hivatalos Ansible-kiadásnál jobban megvalósíthatók. Ha Ansible-t használ az Azure Resource kiépítési célokra, javasoljuk, hogy telepítse a `azure_preview_module` forgatókönyv-szerepkört.

A `azure_preview_module` forgatókönyvek szerepköre három hetente jelenik meg.

## <a name="next-steps"></a>Következő lépések

További információ a forgatókönyv-szerepkörökről: [újrafelhasználható](https://docs.ansible.com/ansible/latest/playbooks_reuse.html)forgatókönyvek létrehozása. 