---
title: Az Azure VNet-átjáró és-kapcsolatok – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan használható az Azure Network Watcher a PowerShell-parancsmaggal való hibakereséshez
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0723ddc9b0e2f15d5c8e51c96d51f58f1313493a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673666"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Az Azure Network Watcher PowerShell-lel Virtual Network átjáró és kapcsolatok hibáinak megoldása

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos képességgel rendelkezik, amelyek az Azure hálózati erőforrásainak megismeréséhez kapcsolódnak. Ezen képességek egyike az erőforrás-hibaelhárítás. Az erőforrás-hibaelhárítás a portál, a PowerShell, a CLI vagy a REST API használatával hívható meg. A híváskor Network Watcher megvizsgálja egy Virtual Network átjáró vagy egy kapcsolat állapotát, és visszaadja az eredményeit.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

A támogatott átjáró-típusok listáját a [támogatott átjárók típusainál](network-watcher-troubleshoot-overview.md#supported-gateway-types)találhatja meg.

## <a name="overview"></a>Áttekintés

Az erőforrás-hibaelhárítás lehetővé teszi a Virtual Network átjárókkal és kapcsolatokkal kapcsolatos hibák elhárítását. Ha az erőforrás-hibaelhárításra vonatkozó kérést végez, a rendszer lekérdezi és ellenőrzi a naplókat. Az ellenőrzés befejezésekor a rendszer az eredményeket adja vissza. Az erőforrás-hibaelhárítási kérelmek hosszú ideig futó kérelmek, amelyek több percet is igénybe vehetnek az eredmények visszaküldéséhez. A hibaelhárítási naplók a megadott Storage-fiók tárolójában tárolódnak.

## <a name="retrieve-network-watcher"></a>Network Watcher beolvasása

Az első lépés az Network Watcher példány beolvasása. A `$networkWatcher` változó a 4. lépésben megadott `Start-AzNetworkWatcherResourceTroubleshooting` parancsmagnak lesz átadva.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Virtual Network átjáróval létesített kapcsolatok beolvasása

Ebben a példában az erőforrás-hibaelhárítást egy kapcsolatban futtatja a rendszer. Virtual Network átjárót is átadhat.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az erőforrás-hibaelhárítás az erőforrás állapotával kapcsolatos információkat ad vissza, és a naplókat is menti egy áttekinthető Storage-fiókba. Ebben a lépésben létrehozunk egy Storage-fiókot, ha meglévő Storage-fiók létezik, használhatja azt.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Network Watcher erőforrás-hibaelhárítás futtatása

Az `Start-AzNetworkWatcherResourceTroubleshooting` parancsmaggal elháríthatja az erőforrásokat. Átadjuk a parancsmagot a Network Watcher objektumnak, a kapcsolatok azonosítójának, a Virtual Network átjárónak, a Storage-fiók azonosítójának, valamint az eredmények tárolásának elérési útnak.

> [!NOTE]
> Az `Start-AzNetworkWatcherResourceTroubleshooting` parancsmag hosszú ideig fut, és eltarthat néhány percig.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

A parancsmag futtatása után Network Watcher áttekinti az erőforrást az állapot ellenőrzéséhez. Visszaadja az eredményeket a rendszerhéjnak, és a megadott Storage-fiókban tárolja az eredmények naplóit.

## <a name="understanding-the-results"></a>Az eredmények megismerése

A művelet szövege általános útmutatást nyújt a probléma megoldásához. Ha a probléma megoldására is sor kerül, további útmutatást nyújt a hivatkozáshoz. Abban az esetben, ha nincs további útmutatás, a válasz egy támogatási eset megnyitására szolgáló URL-címet biztosít.  A válasz tulajdonságaival és a benne foglalt információkkal kapcsolatos további információkért tekintse meg [Network Watcher a hibakeresés áttekintése című](network-watcher-troubleshoot-overview.md) témakört.

A fájlok Azure Storage-fiókokból való letöltésével kapcsolatos utasításokért tekintse meg az [Azure Blob Storage használatának első lépései a .NET használatával](../storage/blobs/storage-dotnet-how-to-use-blobs.md)című témakört. Egy másik eszköz is használható Storage Explorer. Storage Explorer további információ a következő hivatkozáson található: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha a beállítások megváltoztak a VPN-kapcsolat leállításakor, tekintse meg a [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) a hálózati biztonsági csoport és az esetlegesen érintett biztonsági szabályok nyomon követéséhez című témakört.
