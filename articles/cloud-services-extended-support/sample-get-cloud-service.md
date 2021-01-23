---
title: Azure PowerShell minták – az Azure Cloud Service (bővített támogatás) beszerzése – részletek
description: Példa az Azure Cloud Services (kiterjesztett támogatás) telepítésével kapcsolatos információk beolvasására szolgáló parancsfájlokra
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: cd272e0c6ce76ff69e3e58417845836fe2f55b39
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98745038"
---
# <a name="retrieve-information-about-your-azure-cloud-service-extended-support-deployments"></a>Információk beolvasása az Azure Cloud Service (bővített támogatás) üzembe helyezéséről

Ezek a minták többek között a meglévő Azure Cloud Service-környezetek (kiterjesztett támogatás) üzembe helyezésére vonatkozó információk lekérését foglalták magukban.

## <a name="get-all-cloud-services-under-a-resource-group"></a>Az összes Cloud Services beolvasása egy erőforráscsoport alatt

```powershell
Get-AzCloudService -ResourceGroup "ContosOrg"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded
ContosOrg         ContosoCSTest     eastus2euap Failed
```

## <a name="get-single-cloud-service"></a>Egyetlen felhőalapú szolgáltatás beszerzése
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded

$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
$cloudService | Format-List
ResourceGroupName                       : ContosOrg
Configuration                           : <?xml version="1.0" encoding="utf-8"?>
                                          <ServiceConfiguration 
                                          xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
                                          </ServiceConfiguration>
ConfigurationUrl                        :
ExtensionProfileExtension               : {RDPExtension}
Id                                      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCS
Location                                : eastus2euap
Name                                    : ContosoCS
NetworkProfileLoadBalancerConfiguration : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
OSProfileSecret                         : {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}
PackageUrl                              : https://xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
ProvisioningState                       : Succeeded
RoleProfileRole                         : {ContosoFrontEnd, ContosoBackEnd}
StartCloudService                       :
SwappableCloudServiceId                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ContosOrg/providers/Microsoft.Compute/cloudServices/ContosoCSTest
Tag                                     : {
                                            "Owner": "Contos"
                                          }
Type                                    : Microsoft.Compute/cloudServices
UniqueId                                : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
UpgradeMode                             : Auto
```

## <a name="get-cloud-service-instance-view"></a>Cloud Service-példányok nézetének beolvasása
```powershell
Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS" -InstanceView | Format-List

RoleInstanceStatusesSummary : {{
                                "code": "ProvisioningState/succeeded",
                                "count": 4
                              }, {
                                "code": "PowerState/started",
                                "count": 4
                              }}
Statuses                    : {{
                                "code": "ProvisioningState/succeeded",
                                "displayStatus": "Provisioning succeeded",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "PowerState/started",
                                "displayStatus": "Started",
                                "level": "Info",
                                "time": "2020-10-20T13:13:45.0067685Z"
                              }, {
                                "code": "CurrentUpgradeDomain/-1",
                                "displayStatus": "Current Upgrade Domain of Cloud Service is -1.",
                                "level": "Info"
                              }, {
                                "code": "MaxUpgradeDomain/1",
                                "displayStatus": "Max Upgrade Domain of Cloud Service is 1.",
                                "level": "Info"
                              }}
```

## <a name="next-steps"></a>További lépések

Az Azure Cloud Services (bővített támogatás) szolgáltatással kapcsolatos további információkért lásd: [azure Cloud Services (bővített támogatás) – áttekintés](overview.md).