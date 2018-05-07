---
title: Az Azure virtuális hálózati átjáró és a kapcsolatok - PowerShell hibaelhárítása |} Microsoft Docs
description: Ez a lap ismerteti, hogyan Azure hálózati figyelőt PowerShell-parancsmaggal hibaelhárításához
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
ms.openlocfilehash: 0a967759e477d213dd0a33298a4079fc48caa81f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Virtuális hálózati átjáró és az Azure hálózati figyelő PowerShell kapcsolatok hibáinak elhárítása

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Hálózati figyelőt sok képességeket biztosít, a hálózati erőforrások az Azure-ban ismertetése vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. Erőforrások hibaelhárítása hívható a portálon, a PowerShell, a CLI vagy a REST API-n keresztül. Meghívásakor, a hálózati figyelőt megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és visszaadja az eredményekről.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

Látogasson el a támogatott átjáró típusok, listája [támogatott átjárótípusok](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Áttekintés

Erőforrás hibakeresési lehetővé teszi a virtuális hálózati átjárók és kapcsolatok felmerülő problémák hibaelhárításához. A kérelem erőforrás hibáinak elhárításához, amikor folyamatban van-e a naplók lekérdezett és megvizsgálni. Ha vizsgálat befejeződött, a rendszer visszairányítja az eredményeket. Erőforrás-hibaelhárítási kérelmek hosszú ideig futniuk kér, amely több percig is beletelhet visszaküldeni az eredményt. A naplók hibaelhárítási egy tárolót, a megadott tárfiók tárolja.

## <a name="retrieve-network-watcher"></a>Hálózati figyelőt beolvasása

Az első lépés a hálózati figyelőt példányának lekéréséhez. A `$networkWatcher` változó átadott a `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmag a 4. lépésben.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>A virtuális hálózati átjáró kapcsolat beolvasása

Ebben a példában erőforrás hibakeresési van folyamatban futtatott egy kapcsolatot. Is átadhatja azt a virtuális hálózati átjáró.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Create a storage account

Erőforrás hibakeresési erőforrás állapotával kapcsolatos adatokat ad vissza, a naplók tárfiókba vizsgálni is menti. Ebben a lépésben létrehozhatunk egy tárfiókot, ha létezik-e egy meglévő tárfiók használata.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzureRmCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzureStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Futtassa a hálózati figyelőt erőforrás hibaelhárítása

Hibaelhárítás az erőforrások a `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmag. Azt adja át a parancsmag a hálózati figyelő objektum, a kapcsolat, vagy a virtuális hálózati átjáró azonosítóját, a tárfiók azonosítója és az elérési út az eredményeket fogja tárolni.

> [!NOTE]
> A `Start-AzureRmNetworkWatcherResourceTroubleshooting` parancsmag hosszú ideig futó, és előfordulhat, hogy néhány percet igénybe vehet.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

A parancsmag futtatása után hálózati figyelőt ellenőrzi, hogy az erőforrás állapotát ellenőrizni. Az eredményt ad vissza. a rendszerhéj, és az eredmények naplók a megadott tárfiók tárolja.

## <a name="understanding-the-results"></a>Az eredmények ismertetése

A művelet szöveg általános útmutatást biztosít a probléma megoldására. Is művelet az a probléma, ha egy hivatkozás által biztosított további útmutatást. Abban az esetben nincs további útmutatás, ha a válasz biztosít nyissa meg a támogatási esetet URL-címét.  A válasz és tartalmát képező tulajdonságaival kapcsolatos további információkért látogasson el a [hálózati figyelő hibaelhárítása – áttekintés](network-watcher-troubleshoot-overview.md)

A fájlok letöltését az azure storage-fiókok útmutatásért tekintse meg [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz, amely használható a Tártallózó. Tártallózó további információt itt található: a következő hivatkozásra: [Tártallózó](http://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha a beállítások módosítása, hogy stop VPN-kapcsolatot, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági szabályok, amelyek lehet, hogy a szóban forgó.
