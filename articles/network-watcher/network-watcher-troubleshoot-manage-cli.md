---
title: Azure VNET átjáró és kapcsolatok – Azure CLI – Problémamegoldás
titleSuffix: Azure Network Watcher
description: Ez az oldal bemutatja, hogyan használhatja az Azure Network Watcher hibaelhárítását az Azure CLI
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
ms.openlocfilehash: dc0aa8e6099a7ec017aead2fe0f16e9712e17936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840723"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Virtuális hálózati átjáró és kapcsolatok – az Azure Network Watcher használatával – problémamegoldás az Azure CLI használatával

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos lehetőséget kínál az Azure-beli hálózati erőforrások megértéséhez. Az egyik ilyen képesség az erőforrás-hibaelhárítás. Az erőforrás-hibaelhárítás a portálon, a PowerShellen, a CLI-n vagy a REST API-n keresztül hívható meg. A hálózatfigyelő a virtuális hálózati átjáró vagy a kapcsolat állapotát vizsgálja, és visszaadja annak eredményeit.

A cikkben ismertetett lépések végrehajtásához telepítenie kell [az Azure parancssori felületét Mac, Linux és Windows (CLI) rendszerhez.](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit.

A támogatott átjárótípusok listájáért látogasson el a [Támogatott átjárótípusok .](network-watcher-troubleshoot-overview.md#supported-gateway-types)

## <a name="overview"></a>Áttekintés

Az erőforrás-hibaelhárítás lehetővé teszi a virtuális hálózati átjárókkal és kapcsolatokkal kapcsolatban felmerülő problémák elhárítását. Amikor az erőforrás-hibaelhárításra vonatkozó kéréstörténik, a naplók lekérdezése és vizsgálata folyamatban van. Ha a vizsgálat befejeződött, az eredményeket visszaadja. Az erőforrás-hibaelhárítási kérelmek hosszú ideig futó kérelmek, amelyek eredménye több percet is igénybe vehet. A hibaelhárításból származó naplók egy tárolóban tárolóban tárolóban vannak tárolva, amely meg van adva.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Virtuális hálózati átjárókapcsolat beolvasása

Ebben a példában az erőforrás-hibaelhárítás egy kapcsolaton fut. Azt is adja át a virtuális hálózati átjáró. A következő parancsmag egy erőforráscsoport vpn-kapcsolatait sorolja fel.

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

Miután rendelkezik a kapcsolat nevével, futtathatja ezt a parancsot az erőforrás-azonosító levezetéséhez:

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>Create a storage account

Az erőforrás-hibaelhárítás az erőforrás állapotára vonatkozó adatokat ad vissza, emellett naplókat is ment egy felülvizsgálandó tárfiókba. Ebben a lépésben létrehozunk egy tárfiókot, ha egy meglévő tárfiók létezik, használhatja azt.

1. A tárfiók létrehozása

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. A tárfiók kulcsainak beszereznie

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. A tároló létrehozása

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>A Hálózatfigyelő erőforrás-hibaelhárításának futtatása

Az erőforrások hibaelhárítása a `az network watcher troubleshooting` parancsmaggal. Átadjuk a parancsmasa az erőforráscsoport, a neve a network watcher, a kapcsolat azonosítója, a tárfiók azonosítója, és a blob elérési útját a hibaelhárítási eredmények tárolására.

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

Miután futtatta a parancsmast, a Network Watcher áttekinti az erőforrást az állapot ellenőrzéséhez. Visszaadja az eredményeket a rendszerhéjba, és az eredmények naplóit a megadott tárfiókban tárolja.

## <a name="understanding-the-results"></a>Az eredmények megértése

A műveletszöveg általános útmutatást ad a probléma megoldásához. Ha a probléma érdekében lehet műveletet végrehajtani, a hivatkozás további útmutatást kap. Abban az esetben, ha nincs további útmutatást, a válasz megadja az URL-t, hogy nyissa meg a támogatási eset.  A válasz tulajdonságairól és a benne foglalt információkról a [Network Watcher hibaelhárítás – áttekintés című témakörben olvashat bővebben.](network-watcher-troubleshoot-overview.md)

A fájlok Azure storage-fiókokból való letöltésével kapcsolatos tudnivalókat az [Azure Blob storage .NET használatával történő használatának első lépései .](../storage/blobs/storage-dotnet-how-to-use-blobs.md) Egy másik eszköz, amely használható a Storage Explorer. További információ a Storage Explorer ről itt található az alábbi linken: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha olyan beállítások módosultak, amelyek leállítják a VPN-kapcsolatot, olvassa el a [Hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) című témakört a kérdéses hálózati biztonsági csoport és biztonsági szabályok nyomon követéséhez.
