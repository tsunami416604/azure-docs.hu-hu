---
title: NSG naplózás Azure hálózati figyelő biztonsági csoport megtekintése automatizálásához |} Microsoft Docs
description: Ezen a lapon utasításokat biztosít a hálózati biztonsági csoport naplózásának konfigurálása
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 0c9450099ad3561611796a9b45157932f9ac85a9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizálható a NSG naplózás Azure hálózati figyelő biztonsági csoport megtekintése

Az ügyfelek gyakran az ellenőrzést, a biztonságot, az infrastruktúra ellenőrzésére szemben. Ez a kérdés ugyanolyan helyzetet teremt, a virtuális gépek Azure-ban. Fontos a hálózati biztonsági csoport (NSG) szabályok alkalmazása hasonló biztonsági profilt. Használja a biztonsági csoport megtekintése, most olvashatók és egy virtuális Gépet egy NSG belül alkalmazott szabályok listája. Egy arany NSG biztonsági profil meghatározásához és biztonsági csoport megtekintése heti ütemben történik kezdeményezzen, és hasonlítsa össze a kimeneti arany profilt és jelentés létrehozása. Így azonosíthatja a könnyű, amelyek nem felelnek meg az előírt biztonsági profil az összes virtuális.

Ha nem ismeri a hálózati biztonsági csoportokkal, látogasson el a [hálózati biztonsági – áttekintés](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben hasonlítsa össze a virtuális gép adott biztonsági csoport megtekintése eredmények ismert helyes alapterv.

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt. A forgatókönyv feltételezi, hogy létezik-e egy erőforráscsoportot, egy érvényes virtuális géppel használandó.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv a cikkben szereplő lekérdezi a biztonsági csoport nézet egy virtuális géphez.

Ebben a forgatókönyvben a tartalma:

- Egy ismert helyes szabálykészletben beolvasása
- Rest API-hoz egy virtuális gép beolvasása
- A virtuális gép biztonsági csoport nézet beolvasása
- Értékelje ki a válasz

## <a name="retrieve-rule-set"></a>Szabály beolvasása

Ez a példa első lépése, hogy egy meglévő alapterv használata. A következő példa néhány json kinyert használó meglévő hálózati biztonsági csoport a `Get-AzureRmNetworkSecurityGroup` ennél a példánál az alapkonfiguráció használt parancsmagot.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Szabálykészlet átalakítása PowerShell objektumok

Ebben a lépésben azt olvassák a szabályokat, amelyek a hálózati biztonsági csoportot az ebben a példában a várhatók a korábban létrehozott json-fájlt.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Hálózati figyelőt beolvasása

A következő lépésre hálózati figyelőt példányának lekéréséhez. A `$networkWatcher` változó átadott a `AzureRmNetworkWatcherSecurityGroupView` parancsmag.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>A virtuális gép beolvasása

A virtuális gépek futtatásához szükséges a `Get-AzureRmNetworkWatcherSecurityGroupView` parancsmag ellen. A következő példa egy Virtuálisgép-objektum beolvasása.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A program a következő lépés a biztonsági csoport megtekintése eredménye. Ezt az eredményt a rendszer összehasonlítja a korábban bemutatott "eredeti" json.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Az eredmények elemzése

A válasz hálózati illesztők szerint vannak csoportosítva. A különböző típusú visszaadott szabályok érvényben, és az alapértelmezett biztonsági szabályokat. Az eredmény további oszlanak meg az alkalmazásának módját, egy alhálózatot vagy egy virtuális hálózati adaptert.

A következő PowerShell-parancsfájl összehasonlítja az eredmények a biztonsági csoport nézet egy meglévő kimenete egy NSG. A következő példa egy egy egyszerű példa eredménye hogyan hasonlítható a `Compare-Object` parancsmag.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

A következő példa eredménye. Két, a szabályok, amelyek az első szabály volt nem szerepel az összehasonlítás tekintheti meg.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>További lépések

Ha a beállítások módosítása, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági szabályok, amelyek a szóban forgó.













