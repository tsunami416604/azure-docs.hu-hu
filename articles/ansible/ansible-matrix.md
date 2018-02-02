---
title: "Az Azure-modul és verzió mátrix Ansible"
description: "Az Azure-modul és verzió mátrix Ansible"
ms.service: ansible
keywords: "ansible, a szerepkörök, a mátrix, a verzió, a azure, a devops"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: f62cc2df9e4ce815c4427b80e271ddc672748e4f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="ansible-module-and-version-matrix"></a>Ansible modul és verzió mátrix

## <a name="ansible-modules-for-azure"></a>Az Azure-Ansible modulok
Ansible részét képező számos modult, amely közvetlenül a távoli állomáson, vagy playbooks keresztül hajtható végre.
Ez a cikk az Azure-hoz, amely építhető ki az Azure felhőalapú erőforrásokat, például a virtuális gépet, a hálózat és tároló szolgáltatások Ansible modulok listája. Ezek a modulok Ansible hivatalos kiadása, illetve a következő forgatókönyv szerepkörök, Microsoft által kiadott kaphat.

| Az Azure-Ansible modul                   |  Ansible 2.4 |  Alkalmazástervezési szerepkör [azure_module](#introduction-to-azuremodule) |  Playbook Role [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Számítás**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Igen          | Igen                         | Igen                                 | 
| azure_rm_availabilityset_facts              | Igen          | Igen                         | Igen                                 | 
| azure_rm_deployment                         | Igen          | Igen                         | Igen                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Igen          | Igen                         | Igen                                 | 
| azure_rm_virtualmachineimage_facts          | Igen          | Igen                         | Igen                                 | 
| azure_rm_resourcegroup                      | Igen          | Igen                         | Igen                                 | 
| azure_rm_resourcegroup_facts                | Igen          | Igen                         | Igen                                 | 
| azure_rm_virtualmachine                     | Igen          | Igen                         | Igen                                 | 
| azure_rm_virtualmachine_extension           | Igen          | Igen                         | Igen                                 | 
| azure_rm_virtualmachine_scaleset            | Igen          | Igen                         | Igen                                 | 
| azure_rm_image                              |              | Igen                         | Igen                                 | 
| **Hálózat**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Igen          | Igen                         | Igen                                 | 
| azure_rm_virtualnetwork_facts               | Igen          | Igen                         | Igen                                 | 
| azure_rm_subnet                             | Igen          | Igen                         | Igen                                 | 
| azure_rm_networkinterface                   | Igen          | Igen                         | Igen                                 | 
| azure_rm_networkinterface_facts             | Igen          | Igen                         | Igen                                 | 
| azure_rm_publicipaddress                    | Igen          | Igen                         | Igen                                 | 
| azure_rm_publicipaddress_facts              | Igen          | Igen                         | Igen                                 | 
| azure_rm_dnsrecordset                       | Igen          | Igen                         | Igen                                 | 
| azure_rm_dnsrecordset_facts                 | Igen          | Igen                         | Igen                                 | 
| azure_rm_dnszone                            | Igen          | Igen                         | Igen                                 | 
| azure_rm_dnszone_facts                      | Igen          | Igen                         | Igen                                 | 
| azure_rm_loadbalancer                       | Igen          | Igen                         | Igen                                 | 
| azure_rm_loadbalancer_facts                 | Igen          | Igen                         | Igen                                 | 
| azure_rm_appgw                              | -            | -                           | Igen                                 | 
| azure_rm_appgwroute                         | -            | -                           | Igen                                 | 
| azure_rm_appgwroute                         | -            | -                           | Igen                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Igen                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Igen                                 |
| azure_rm_securitygroup                      | Igen          | Igen                         | Igen                                 | 
| azure_rm_appgwroutetable_facts              | Igen          | Igen                         | Igen                                 | 
| **Storage**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Igen          | Igen                         | Igen                                 | 
| azure_rm_storageaccount_facts               | Igen          | Igen                         | Igen                                 | 
| azure_rm_storageblob                        | Igen          | Igen                         | Igen                                 | 
| azure_rm_managed_disk                       | Igen          | Igen                         | Igen                                 | 
| azure_rm_managed_disk_facts                 | Igen          | Igen                         | Igen                                 | 
| **Tárolók**                    |           |                          |                                  | 
| azure_rm_acs                                | Igen          | Igen                         | Igen                                 | 
| azure_rm_containerinstance                  | -            | Igen                         | Igen                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Igen                                 | 
| azure_rm_containerregistry                  | -            | Igen                         | Igen                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Igen                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Igen                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Igen                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Igen                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Igen                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Igen          | Igen                         | Igen                                 | 
| azure_rm_functionapp_facts                  | Igen          | Igen                         | Igen                                 | 
| **Adatbázisok**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Igen                         | Igen                                 | 
| azure_rm_sqlserver_facts                    | -            | -                           | Igen                                 | 
| azure_rm_sqldatabase                        | -            | Igen                         | Igen                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Igen                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Igen                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Igen                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Igen                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Igen                                 | 
| azure_rm_mysqlserver                        | -            | Igen                         | Igen                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Igen                                 | 
| azure_rm_mysqldatabase                      | -            | Igen                         | Igen                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Igen                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Igen                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Igen                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Igen                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Igen                                 | 
| azure_rm_postgresqlserver                   | -            | Igen                         | Igen                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Igen                                 | 
| azure_rm_postgresqldatabase                 | -            | Igen                         | Igen                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Igen                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Igen                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Igen                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Igen                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Igen                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | -                           | Igen                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Igen                                 |
| azure_rm_keyvaultkey                        | -            | -                           | Igen                                 |
| azure_rm_keyvaultsecret                     | -            | -                           | Igen                                 |

## <a name="introduction-to-azuremodule"></a>Azure_module bemutatása
A [azure_module alkalmazástervezési szerepkör](https://galaxy.ansible.com/Azure/azure_modules/) tartalmazza a legújabb módosításait és az Azure modulok hibajavítások a [devel fiókirodai Ansible tárház](https://github.com/ansible/ansible/tree/devel). Ha nem várja meg a Ansible a következő verzióra, a azure_module szerepkör telepítése akkor hasznos.

A azure_module alkalmazástervezési szerepkör minden három héttel megjelenik.

## <a name="introduction-to-azurepreviewmodule"></a>Azure_preview_module bemutatása
A [azure_preview_module alkalmazástervezési szerepkör](https://galaxy.ansible.com/Azure/azure_preview_modules/) olyan legteljesebb körű szerepkör és a legújabb Azure modulokat tartalmaz. A frissítések és hibajavítások a hivatalos Ansible kiadási-nál több időben történik. Ansible kiépítés célra az Azure erőforrás használja, ha Ön a azure_preview_module szerepkör telepítse.

A azure_preview_module alkalmazástervezési szerepkör minden három héttel megjelenik.

## <a name="next-steps"></a>További lépések
Forgatókönyv szerepkörök kapcsolatos további információ [létrehozása egyszer használatos Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
