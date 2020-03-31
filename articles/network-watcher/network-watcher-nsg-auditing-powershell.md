---
title: NSG-naplózás automatizálása – Biztonsági csoport nézet
titleSuffix: Azure Network Watcher
description: Ez a lap a hálózati biztonsági csoport naplózásának konfigurálásához
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
ms.openlocfilehash: 59c1b6e6c281a736a79d110bd7d943344bcd5130
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840978"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Az NSG-naplózás automatizálása az Azure Network Watcher Security csoportnézetével

Az ügyfelek gyakran szembesülnek azzal a kihívással, hogy ellenőrizzék az infrastruktúrájuk biztonsági helyzetét. Ez a kihívás nem különbözik a virtuális gépek az Azure-ban. Fontos, hogy a hálózati biztonsági csoport (NSG) szabályai alapján hasonló biztonsági profil legyen alkalmazva. A biztonsági csoport nézet használatával most már lehívhatja az NSG-n belüli virtuális gépekre alkalmazott szabályok listáját. Megadhat egy arany NSG biztonsági profilt, és heti ütemben kezdeményezheti a Security Group View nézetet, és összehasonlíthatja a kimenetet az arany profillal, és létrehozhat egy jelentést. Így könnyedén azonosíthatja az összes olyan virtuális gépet, amely nem felel meg az előírt biztonsági profilnak.

Ha nem ismeri a hálózati biztonsági csoportokat, olvassa el a [Hálózati biztonság – áttekintés című témakört.](../virtual-network/security-overview.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben egy ismert jó alapkonfigurációt hasonlít össze a virtuális gép readott biztonságicsoport-nézet eredményeivel.

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit. A forgatókönyv azt is feltételezi, hogy egy érvényes virtuális géppel rendelkező erőforráscsoport használható.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv ebben a cikkben lekéri a virtuális gép biztonsági csoport nézetét.

Ebben a forgatókönyvben a következőket fogja:

- Ismert helyes szabálykészlet beolvasása
- Virtuális gép lekérése a Rest API-val
- Biztonsági csoport nézet beszereznie a virtuális géphez
- Válasz kiértékelése

## <a name="retrieve-rule-set"></a>Szabálykészlet lekérése

Ebben a példában az első lépés egy meglévő alaptervvel való munka. A következő példa néhány json kinyert egy meglévő `Get-AzNetworkSecurityGroup` hálózati biztonsági csoport a parancsmag, amely ebben a példában használt alapkonfiguráció.

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

## <a name="convert-rule-set-to-powershell-objects"></a>Szabálykészlet konvertálása PowerShell-objektumokká

Ebben a lépésben egy jsonfájlt olvasunk, amely korábban jött létre azokkal a szabályokkal, amelyek várhatóan a hálózati biztonsági csoportban lesznek ebben a példában.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Hálózati figyelő lekérése

A következő lépés a Network Watcher példány beolvasása. A `$networkWatcher` változó átkerül `AzNetworkWatcherSecurityGroupView` a parancsmagba.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>Virtuális gép beszerezni

A `Get-AzNetworkWatcherSecurityGroupView` parancsmag futtatásához virtuális gép szükséges. A következő példa lead egy virtuális gép objektumot.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A következő lépés a biztonsági csoport nézet eredményének beolvasása. Ezt az eredményt összehasonlítjuk a korábban bemutatott "kiindulási" jsonlal.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Az eredmények elemzése

A válasz hálózati adapterek szerint van csoportosítva. A visszaadott szabályok különböző típusai hatékony és alapértelmezett biztonsági szabályok. Az eredmény további bontásban, hogyan kell alkalmazni, akár egy alhálózaton, akár egy virtuális hálózati adapteren.

A következő PowerShell-parancsfájl összehasonlítja a biztonsági csoport nézet eredményeit egy NSG meglévő kimenetével. A következő példa egy egyszerű példa arra, `Compare-Object` hogyan lehet összehasonlítani az eredményeket a parancsmaggal.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

A következő példa az eredmény. Az első szabálykészletben lévő szabályok közül kettőt nem láthat az összehasonlításban.

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

Ha a beállítások megváltoztak, a [Hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) című témakörben találja nyomon a kérdéses hálózati biztonsági csoportokat és biztonsági szabályokat.













