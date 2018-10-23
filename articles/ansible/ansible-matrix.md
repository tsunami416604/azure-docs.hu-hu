---
title: Az Ansible modul és verzió mátrix az Azure-hoz
description: Az Ansible modul és verzió mátrix az Azure-hoz
ms.service: ansible
keywords: az ansible, szerepkörök, mátrix, verzió, azure, fejlesztés és üzemeltetés
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 09/22/2018
ms.topic: article
ms.openlocfilehash: a71f28413028efe3547df7c464ec852fe2161f63
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649941"
---
# <a name="ansible-module-and-version-matrix"></a>Az Ansible modul és verzió mátrix

## <a name="ansible-modules-for-azure"></a>Az Ansible-modulok az Azure-hoz
Az Ansible tartalmaz egy számot, amelyek közvetlenül a távoli gazdagépeken vagy forgatókönyvek keresztül is végrehajthatók.
Ez a cikk az Azure-ban, amely építhető ki az Azure felhőbeli erőforrások, például a virtuális gép, hálózati és tárolószolgáltatások Ansible modulok listája. Ezeket a modulokat az Ansible hivatalos kiadás, illetve a következő forgatókönyv-szerepkörök, a Microsoft által közzétett kérheti le.

| Az Ansible-modul az Azure-hoz                   |  Az Ansible 2.4 |  Az Ansible 2,5 |  Az Ansible 2.6 | Az Ansible 2.7 | [Az Ansible-szerepkör](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|-------------------------------------| 
| **Számítás**                    |           |                          |                          |                            |                                | 
| azure_rm_availabilityset                    | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_availabilityset_facts              | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_deployment                         | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_resource                           | -            | -                           | Igen          | Igen          | Igen                                 | 
| azure_rm_resource_facts                     | -            | -                           | Igen          | Igen          | Igen                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_virtualmachineimage_facts          | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_resourcegroup                      | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_resourcegroup_facts                | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_virtualmachine                     | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_virtualmachine_extension           | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_virtualmachine_scaleset            | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_image                              |              | Igen                         | Igen          | Igen          | Igen                                 | 
| **Hálózat**                    |           |                          |                          |                             |                               | 
| azure_rm_virtualnetwork                     | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_virtualnetwork_facts               | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_subnet                             | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_networkinterface                   | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_networkinterface_facts             | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_publicipaddress                    | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_publicipaddress_facts              | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_dnsrecordset                       | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_dnsrecordset_facts                 | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_dnszone                            | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_dnszone_facts                      | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_loadbalancer                       | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_loadbalancer_facts                 | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_appgateway                         | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | -            | Igen                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | Igen                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | Igen                                 |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_securitygroup                      | Igen          | Igen                         | Igen          | Igen          | Igen                                 |
| azure_rm_route                              | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_routetable                         | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_routetable_facts                   | -            | -                           | -            | Igen          | Igen                                 | 
| **Tárolás**                    |           |                          |                          |                             |                               | 
| azure_rm_storageaccount                     | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_storageaccount_facts               | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_storageblob                        | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_managed_disk                       | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_managed_disk_facts                 | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| **Containers**                    |           |                          |                          |                            |                                | 
| azure_rm_aks                                | -            | -                           | Igen          | Igen          | Igen                                 | 
| azure_rm_aks_facts                          | -            | -                           | Igen          | Igen          | Igen                                 | 
| azure_rm_acs                                | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_containerinstance                  | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -              | -            | Igen                                 | 
| azure_rm_containerregistry                  | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Igen                                 | 
| **Azure Functions**                    |           |                          |                          |                            |                                | 
| azure_rm_functionapp                        | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_functionapp_facts                  | Igen          | Igen                         | Igen          | Igen          | Igen                                 | 
| **Adatbázisok**                    |           |                          |                          |                             |                               | 
| azure_rm_sqlserver                          | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_sqlserver_facts                    | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_sqldatabase                        | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_mysqlserver                        | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_mysqldatabase                      | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_postgresqlserver                   | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_postgresqldatabase                 | -            | Igen                         | Igen          | Igen          | Igen                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Igen          | Igen                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Igen                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Igen                                 | 
| **Key Vault**                    |           |                          |                          |                             |                               | 
| azure_rm_keyvault                           | -            | Igen                         | Igen          | Igen          | Igen                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -              | Igen                               |
| azure_rm_keyvaultkey                        | -            | Igen                         | Igen          | Igen          | Igen                                 |
| azure_rm_keyvaultsecret                     | -            | Igen                         | Igen          | Igen          | Igen                                 |
| **Web Apps**                    |           |                          |                          |                             |                               | 
| azure_rm_appserviceplan                          | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_appserviceplan_facts                    | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_webapp                                  | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_webapp_facts                            | -            | -                         | -          | Igen          | Igen                                 | 
| **Traffic Manager**                    |           |                          |                          |                             |                               | 
| azure_rm_trafficmanagerendpoint                  | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_trafficmanagerendpoint_facts            | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_trafficmanagerprofile                   | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_trafficmanagerprofile_facts             | -            | -                         | -          | Igen          | Igen                                 | 
| **Az automatikus méretezés**                    |           |                          |                          |                             |                               | 
| azure_rm_autoscale                  | -            | -                         | -          | Igen          | Igen                                 | 
| azure_rm_autoscale_facts            | -            | -                         | -          | Igen          | Igen                                 | 

## <a name="introduction-to-playbook-role-for-azure"></a>Forgatókönyv szerepkör bemutatása az Azure-hoz
A [azure_preview_module forgatókönyv szerepkör](https://galaxy.ansible.com/Azure/azure_preview_modules/) a legteljesebb körű szerepkört, és minden a legújabb Azure-modulokat tartalmaz. A frissítések és hibajavításokat tartalmaz, mint a hivatalos Ansible-kiadás további időben kell elvégezni. Ansible az Azure erőforrás-kiépítés célra használja, ha Ön javasoljuk, hogy a azure_preview_module forgatókönyv szerepkör telepítése.

A forgatókönyv azure_preview_module szerepkör három hetente akkor szabadul fel.

## <a name="next-steps"></a>További lépések
Forgatókönyv-szerepkörök kapcsolatos további információkért lásd [létrehozása újrahasznosítható forgatókönyvek](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
