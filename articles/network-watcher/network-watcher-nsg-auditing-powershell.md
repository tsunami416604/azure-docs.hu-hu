---
title: NSG-t az Azure Network Watcher biztonsági csoport nézet naplózás automatizálása |} A Microsoft Docs
description: Ezen a lapon ez útmutatást nyújt a hálózati biztonsági csoportok naplózásának konfigurálása
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
ms.openlocfilehash: 3d35860452aabb6aecc4e8549c7b5ce4447d7aa4
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047670"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatizálhatja az NSG-naplózás az Azure Network Watcher biztonsági csoport Nézet

Ügyfeleink gyakran kihívással szembesülnek azzal a ellenőrzésére, hogy az infrastruktúra biztonsági állapotát. Ez a kérdés nem nem azonos az Azure-beli virtuális gépek. Fontos a alkalmazni hálózati biztonsági csoport (NSG) szabályai alapján hasonló biztonsági profillal rendelkezik. A biztonsági csoport nézet most már beszerezheti a belül egy NSG-t virtuális gépre alkalmazott szabályok listáját. Egy arany NSG biztonsági profil meghatározásához és kezdeményezheti a biztonsági csoport nézet heti kiadása ütemben történik, és hasonlítsa össze a kimenetet a hamisított Kerberos-profil és -jelentés létrehozása. Ezzel a módszerrel azonosíthatja egyszerűen tartozó virtuális gépeket, amelyek nem felelnek meg az előírt biztonsági profil.

Ha nincs tisztában a hálózati biztonsági csoportok, tekintse meg [hálózat biztonsági áttekintése](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben a biztonsági csoport nézet adatsorban visszaadott egy virtuális gép ismert jó alapterv összehasonlítása.

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához. A forgatókönyv azt is feltételezi, hogy létezik-e egy érvényes virtuális gépet egy erőforráscsoportot használni.

## <a name="scenario"></a>Forgatókönyv

Az ebben a cikkben ismertetett forgatókönyv lekérdezi a virtuális gép biztonsági csoport nézet.

Ebben a forgatókönyvben tartalma:

- Beolvasni egy ismert jó sadu pravidel
- Rest API-val rendelkező virtuális gép beolvasása
- Virtuális gép biztonsági csoport nézet beolvasása
- Válasz kiértékelése

## <a name="retrieve-rule-set"></a>Szabálykészlet beolvasása

Ebben a példában az első lépés, hogy egy meglévő alapterv használata. Az alábbi példában néhány kinyert egy meglévő hálózati biztonsági csoport a json a `Get-AzNetworkSecurityGroup` ebben a példában a referenciakonfiguráció használt parancsmagot.

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

## <a name="convert-rule-set-to-powershell-objects"></a>PowerShell-objektumok sadu pravidel átalakítása

Ebben a lépésben azt olvassák a szabályokat, amelyek várhatóan az ebben a példában a hálózati biztonsági csoport a korábban létrehozott json-fájlt.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>A Network Watcher beolvasása

A következő lépés, hogy a Network Watcher-példány beolvasása. A `$networkWatcher` változó átadott a `AzNetworkWatcherSecurityGroupView` parancsmagot.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>A virtuális gép lekérése

A virtuális gépek futtatásához szükséges a `Get-AzNetworkWatcherSecurityGroupView` parancsmag ellen. Az alábbi példa lekéri a Virtuálisgép-objektum.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A következő lépés, hogy a biztonsági csoport nézet eredmény beolvasása. Ezt az eredményt a rendszer összehasonlítja a "baseline" JSON-fájllal, amely korábban látható volt.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Az eredmények elemzésével

A válasz hálózati adapterek szerint vannak csoportosítva. A különböző típusú szabályok adott vissza érvényes és alapértelmezett biztonsági szabályokat. Az eredmény további bontják alkalmazásának módját, egy alhálózatot vagy egy virtuális hálózati adapteren.

A következő PowerShell-parancsfájlt a biztonsági csoport nézet egy NSG-t meglévő kimenetre eredményeit hasonlítja össze. Az alábbi példában az eredmények hogyan összehasonlíthatók az egyszerű példát `Compare-Object` parancsmagot.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

A következő példa eredménye. Láthatja, hogy két, az első szabály beállított szabályok nem volt jelen a ezzel szemben.

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

Beállítások módosítása, ha [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági szabályok, amelyek az adott.













