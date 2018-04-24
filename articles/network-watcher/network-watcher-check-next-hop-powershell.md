---
title: "Következő Ugrás az Azure hálózati figyelő következő ugrás - PowerShell található |} Microsoft Docs"
description: "Ez a cikk leírja, hogyan megtalálhatja a következő ugrás típusa van, és használja a PowerShell használatával, a következő ugrás IP-címet."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Megtudhatja, milyen a következő ugrás típusa a következő ugrás funkció használ az Azure hálózati figyelőt PowerShell használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Az Azure REST API-n](network-watcher-check-next-hop-rest.md)

Következő ugrás csak a hálózati figyelő, amely a képességét get biztosít, a következő ugrás típusa és az IP-cím a megadott virtuális gép alapján. Ez a szolgáltatás akkor hasznos, meghatározni, hogy ha egy virtuális gép elhagyó forgalomra halad át egy átjárót, az interneten vagy a virtuális hálózatok a cél eléréséhez.

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben szüksége lesz az Azure-portálon a következő ugrás típusa és az IP-cím kereséséhez.

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt. A forgatókönyv feltételezi, hogy létezik-e egy erőforráscsoportot, egy érvényes virtuális géppel használandó.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv, a cikkben szereplő használja a következő ugrás, egyik funkciója, amely a következő ugrás típusa és az IP-cím erőforrás megkeresése hálózati figyelőt. Következő ugrás kapcsolatos további információkért látogasson el a [következő ugrásaként áttekintése](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Hálózati figyelőt beolvasása

Az első lépés a hálózati figyelőt példányának lekéréséhez. A `$networkWatcher` változó átadása a következő ugrási parancsmag ellenőrzése.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>A virtuális gép beolvasása

Következő ugrás a virtuális gép a következő ugrás és a következő ugrás IP-címét adja vissza. A virtuális gép azonosítóját a parancsmag szükség. Ha már ismeri az Azonosítót a virtuális gép használja, kihagyhatja ezt a lépést.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> Következő ugrás megköveteli, hogy a virtuális gép erőforrásához lefoglalt futtatásához.

## <a name="get-the-network-interfaces"></a>A hálózati adapterek beolvasása

A virtuális gépen egy hálózati adapter IP-címe szükséges, ebben a példában beolvassuk a hálózati adaptert egy virtuális gépen. Ha már ismeri a virtuális gépen vizsgálni kívánt IP-cím, kihagyhatja ezt a lépést.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Következő ugrás beolvasása

Most közvetlen telepítésnek a `Get-AzureRmNetworkWatcherNextHop` parancsmag. Azt adja át a parancsmag a hálózati figyelőt, a virtuális gép azonosítója, a forrás IP-cím és a cél IP-címét. Ebben a példában a cél IP-címét, hogy egy virtuális Gépet egy másik virtuális hálózaton. Nincs a virtuális hálózati átjáró a két virtuális hálózatok között.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Eredmények áttekintése

Amikor végzett, a eredményei. Továbbá az erőforrás típusa a következő ugrás IP-címet adja vissza. Az ebben az esetben a virtuális hálózati átjáró nyilvános IP-címét is.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

Az alábbi listában a jelenleg rendelkezésre álló NextHopType értékeket mutatja:

**Következő ugrás típusa**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan ellátogatva programozott módon tekintse át a hálózati biztonsági csoport beállításainak [NSG naplózás hálózati figyelőt](network-watcher-nsg-auditing-powershell.md)

















