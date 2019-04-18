---
title: Hibaelhárítás az Azure virtuális hálózati átjáró és kapcsolatok – PowerShell |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan használható az Azure Network Watcher PowerShell-parancsmag hibáinak elhárítása
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: b25ebeadff46ea04c2adf5add6aeb86b751681ad
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047211"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Virtuális hálózati átjáró és az Azure Network Watcher PowerShell-lel kapcsolatok hibaelhárítása

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos funkciót kínál a ismertetése az Azure-ban a hálózati erőforrások vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. Erőforrások hibaelhárítása a portal, PowerShell, CLI vagy REST API használatával is meghívható. Meghívni, Network Watcher megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és a csapatával az eredményeket adja vissza.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához.

Látogasson el a támogatott átjáró típusok, listáját [támogatott átjárótípusok](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Áttekintés

Erőforrás hibaelhárítás lehetővé teszi a virtuális hálózati átjárók és kapcsolatok felmerülő problémák hibaelhárításához. A hibaelhárítási erőforrás a kérelem elküldésekor folyamatban van-e a naplók kérdezhető le, és megvizsgálni. Ha az ellenőrzés befejeződött, a rendszer visszairányítja az eredményeket. Erőforrás-kérelmek hibaelhárítási sokáig futó kérelmek, amelyek több percig is eltarthat adja vissza az eredményt. Hibaelhárítás a naplók vannak tárolva egy tárolót a storage-fiók van megadva.

## <a name="retrieve-network-watcher"></a>A Network Watcher beolvasása

Az első lépés, hogy a Network Watcher-példány beolvasása. A `$networkWatcher` változó átadott a `Start-AzNetworkWatcherResourceTroubleshooting` parancsmag a 4. lépésben.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>A virtuális hálózati átjáró kapcsolat beolvasása

Ebben a példában erőforrás hibaelhárítás folyamatban futtatta a kapcsolatot. Is átadhatja azt a virtuális hálózati átjáró.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Erőforrás hibaelhárítás adja vissza az erőforrás állapotára vonatkozó adatok, naplók vizsgálni a storage-fiókba is menti. Ebben a lépésben hozunk létre egy storage-fiókot, ha egy meglévő tárfiók létezik használhatja azt.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Futtassa a Network Watcher erőforrás hibaelhárítás

Hibaelhárítás az erőforrásokat a `Start-AzNetworkWatcherResourceTroubleshooting` parancsmagot. A parancsmag adjuk át, a Network Watcher objektum, a kapcsolat vagy a virtuális hálózati átjáró azonosítóját, a tárfiók azonosítója és az elérési út eredményeket tárolja.

> [!NOTE]
> A `Start-AzNetworkWatcherResourceTroubleshooting` parancsmag mennyi ideig fut, és néhány percet is igénybe vehet.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

A parancsmag futtatása után a Network Watcher áttekinti az erőforrás állapotának ellenőrzése. Visszaadja az eredményeket a rendszerhéj és a megadott tárfiók az eredmények naplókat tárolja.

## <a name="understanding-the-results"></a>Az eredmények ismertetése

A művelet szöveg nyújt általános útmutatást a probléma megoldásához. Egy műveletet elvégezhet a problémára, ha egy hivatkozást a további útmutatást. Abban az esetben, ha nincsenek további útmutatást, a válasz biztosít támogatási eset nyitása URL-címét.  A válasz és foglalt tulajdonságaival kapcsolatos további információkért látogasson el a [Network Watcher hibaelhárítása – áttekintés](network-watcher-troubleshoot-overview.md)

Fájlok letöltése az azure storage-fiókokra vonatkozó utasításokért tekintse meg [.NET használatával az Azure Blob storage használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz használható a Storage Explorer. További információ a Storage Explorer itt található, a következő hivatkozásra: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha-beállításai lettek módosítva lett, hogy állítsa le VPN-kapcsolat, tekintse meg [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és biztonsági szabályok, amelyek az adott lehetnek.
