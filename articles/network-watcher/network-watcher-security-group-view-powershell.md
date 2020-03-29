---
title: Hálózati biztonság elemzése – Biztonsági csoport nézete – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez a cikk ismerteti, hogyan használhatja a PowerShell t a virtuális gépek biztonságának elemzésére a Security Group View használatával.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 4cba2c7e25b5f76b0638da1c551514f102247ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840791"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>A virtuális gép biztonságának elemzése a Biztonsági csoport nézetben a PowerShell használatával

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

A biztonsági csoport nézet a virtuális gépekre alkalmazott konfigurált és hatékony hálózati biztonsági szabályokat adja vissza. Ez a funkció a hálózati biztonsági csoportok és a virtuális gépeken konfigurált szabályok naplózásához és diagnosztizálásához hasznos, amelyek biztosítják a forgalom megfelelő engedélyezik vagy megtagadják. Ebben a cikkben bemutatjuk, hogyan lehet lekérni a konfigurált és hatékony biztonsági szabályokat egy virtuális gépre a PowerShell használatával


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben az esetben a `Get-AzNetworkWatcherSecurityGroupView` parancsmag futtatásával lekérheti a biztonsági szabály adatait.

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv ebben a cikkben lekéri a konfigurált és hatékony biztonsági szabályokat egy adott virtuális gép.

## <a name="retrieve-network-watcher"></a>Hálózati figyelő lekérése

Az első lépés a Network Watcher példány beolvasása. Ez a változó `Get-AzNetworkWatcherSecurityGroupView` átkerül a parancsmagba.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>Virtuális gép beszerezni

A `Get-AzNetworkWatcherSecurityGroupView` parancsmag futtatásához virtuális gép szükséges. A következő példa lead egy virtuális gép objektumot.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A következő lépés a biztonsági csoport nézet eredményének beolvasása.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

A következő példa a visszaadott eredmények rövidített válasza. Az eredmények a virtuális gépen érvényes és alkalmazott biztonsági szabályokat mutatják **a NetworkInterfaceSecurityRules**, **DefaultSecurityRules**és **EffectiveSecurityRules**csoportokra bontva.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>További lépések

Látogasson el [a Network Security Groups (NSG)](network-watcher-nsg-auditing-powershell.md) és a Network Watcher elemre, ahol megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítését.


