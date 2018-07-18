---
title: Elemezheti a hálózati biztonság Azure Network Watcher biztonsági csoport nézet – PowerShell |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan elemezheti a biztonsági csoport nézet a virtuális gépek biztonsági a PowerShell használatával.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9cc06e97730ac846e8aa42c2cee77dfe17be99bb
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089646"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Biztonsági csoport nézet PowerShell használatával a virtuális gép biztonsági elemzése

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Biztonsági csoport nézet konfigurált és érvényben lévő hálózati biztonsági szabályok a virtuális gépek alkalmazott adja vissza. Ez a funkció akkor hasznos, naplózás, és diagnosztizálhatja hálózati biztonsági csoportok és annak érdekében, hogy folyamatban van a forgalom egy virtuális gépen konfigurált szabályok megfelelően engedélyezi vagy megtagadja. Ebben a cikkben bemutatjuk, hogyan kérheti le a konfigurált és érvényben lévő biztonsági szabályokat egy virtuális gépet PowerShell-lel

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben futtatja a `Get-AzureRmNetworkWatcherSecurityGroupView` a biztonsági szabály adatok beolvasásához.

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához.

## <a name="scenario"></a>Forgatókönyv

Az ebben a cikkben ismertetett forgatókönyvben egy adott virtuális géphez konfigurált és érvényben lévő biztonsági szabályok kérdezi le.

## <a name="retrieve-network-watcher"></a>A Network Watcher beolvasása

Az első lépés, hogy a Network Watcher-példány beolvasása. Az megörökli a változót a `Get-AzureRmNetworkWatcherSecurityGroupView` parancsmagot.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>A virtuális gép lekérése

A virtuális gépek futtatásához szükséges a `Get-AzureRmNetworkWatcherSecurityGroupView` parancsmag ellen. Az alábbi példa lekéri a Virtuálisgép-objektum.

```powershell
$VM = Get-AzurermVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A következő lépés, hogy a biztonsági csoport nézet eredmény beolvasása.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

Az alábbi példa a visszaadott eredmények akkor használhatja rövidített választ. Az eredmények megjelenítése hatékony és alkalmazott biztonsági szabályok a virtuális gépen a csoportok szerinti bontásban **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, és  **EffectiveSecurityRules**.

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

Látogasson el [naplózás hálózati biztonsági csoportok (NSG) és a Network Watcher](network-watcher-nsg-auditing-powershell.md) megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítése.


