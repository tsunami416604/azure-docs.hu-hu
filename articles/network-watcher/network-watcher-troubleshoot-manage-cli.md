---
title: Az Azure virtuális hálózati átjáró és a kapcsolatok - Azure CLI 2.0 hibaelhárítása |} Microsoft Docs
description: Ez a lap ismerteti, hogyan Azure hálózati figyelőt Azure CLI 2.0 hibaelhárítása
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 81a31365a222cde8e57258ff47d8a64af53c05c2
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-20"></a>Virtuális hálózati átjáró és az Azure hálózati figyelő Azure CLI 2.0 verziót használja kapcsolatok hibáinak elhárítása

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Hálózati figyelőt sok képességeket biztosít, a hálózati erőforrások az Azure-ban ismertetése vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. Erőforrások hibaelhárítása hívható a portálon, a PowerShell, a CLI vagy a REST API-n keresztül. Meghívásakor, a hálózati figyelőt megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és visszaadja az eredményekről.

Ez a cikk használja a következő generációs CLI a erőforrás management üzembe helyezési modellel, Azure CLI 2.0, elérhető a Windows, Mac és Linux.

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület Mac, Linux és Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

Látogasson el a támogatott átjáró típusok, listája [támogatott átjárótípusok](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Áttekintés

Erőforrás hibakeresési lehetővé teszi a virtuális hálózati átjárók és kapcsolatok felmerülő problémák hibaelhárításához. A kérelem erőforrás hibáinak elhárításához, amikor folyamatban van-e a naplók lekérdezett és megvizsgálni. Ha vizsgálat befejeződött, a rendszer visszairányítja az eredményeket. Erőforrás-hibaelhárítási kérelmek hosszú ideig futniuk kér, amely több percig is beletelhet visszaküldeni az eredményt. A naplók hibaelhárítási egy tárolót, a megadott tárfiók tárolja.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>A virtuális hálózati átjáró kapcsolat beolvasása

Ebben a példában erőforrás hibakeresési van folyamatban futtatott egy kapcsolatot. Is átadhatja azt a virtuális hálózati átjáró. A következő parancsmagot a vpn-kapcsolatok a erőforráscsoport sorolja fel.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Miután a kapcsolat neve, futtathatja a parancsot az erőforrás-azonosítót:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Create a storage account

Erőforrás hibakeresési erőforrás állapotával kapcsolatos adatokat ad vissza, a naplók tárfiókba vizsgálni is menti. Ebben a lépésben létrehozhatunk egy tárfiókot, ha létezik-e egy meglévő tárfiók használata.

1. A storage-fiók létrehozása

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. A tárfiók kulcsait beolvasása

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. A tároló létrehozása

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Futtassa a hálózati figyelőt erőforrás hibaelhárítása

Hibaelhárítás az erőforrások a `az network watcher troubleshooting` parancsmag. Azt át a parancsmag az erőforráscsoportot, a hálózati figyelőt, a kapcsolat, a tárfiók Id azonosítója neve és elérési útját a hibaelhárítás tárolni a blob eredményez.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

A parancsmag futtatása után hálózati figyelőt ellenőrzi, hogy az erőforrás állapotát ellenőrizni. Az eredményt ad vissza. a rendszerhéj, és az eredmények naplók a megadott tárfiók tárolja.

## <a name="understanding-the-results"></a>Az eredmények ismertetése

A művelet szöveg általános útmutatást biztosít a probléma megoldására. Is művelet az a probléma, ha egy hivatkozás által biztosított további útmutatást. Abban az esetben nincs további útmutatás, ha a válasz biztosít nyissa meg a támogatási esetet URL-címét.  A válasz és tartalmát képező tulajdonságaival kapcsolatos további információkért látogasson el a [hálózati figyelő hibaelhárítása – áttekintés](network-watcher-troubleshoot-overview.md)

A fájlok letöltését az azure storage-fiókok útmutatásért tekintse meg [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz, amely használható a Tártallózó. Tártallózó további információt itt található: a következő hivatkozásra: [Tártallózó](http://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha a beállítások módosítása, hogy stop VPN-kapcsolatot, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági szabályok, amelyek lehet, hogy a szóban forgó.
