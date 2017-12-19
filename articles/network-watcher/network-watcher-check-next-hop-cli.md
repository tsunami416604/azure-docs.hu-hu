---
title: "Következő Ugrás az Azure hálózati figyelő következő ugrás - Azure CLI 2.0 található |} Microsoft Docs"
description: "Ez a cikk leírja, hogyan megtalálhatja a következő ugrás típusa van, és használó Azure parancssori felület használatával, a következő ugrás IP-cím."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 0700c274-3e0d-4dca-acfa-3ceac8990613
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fb4a24fd758ad4b7231364f3ee7d56a9a2dbccb1
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-azure-cli-20"></a>Megtudhatja, milyen a következő ugrás típusa van a következő ugrás funkció használatát az Azure CLI 2.0 verziót használja Azure hálózati figyelőt

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Az Azure REST API-n](network-watcher-check-next-hop-rest.md)

Következő ugrás csak a hálózati figyelő, amely a képességét get biztosít, a következő ugrás típusa és az IP-cím a megadott virtuális gép alapján. Ez a szolgáltatás akkor hasznos, meghatározni, hogy ha egy virtuális gép elhagyó forgalomra halad át egy átjárót, az interneten vagy a virtuális hálózatok a cél eléréséhez.

Ez a cikk használja a következő generációs CLI a erőforrás management üzembe helyezési modellel, Azure CLI 2.0, elérhető a Windows, Mac és Linux.

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület Mac, Linux és Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben az Azure parancssori felület használandó keresse meg a következő ugrás típusa és az IP-címet.

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt. A forgatókönyv feltételezi, hogy létezik-e egy erőforráscsoportot, egy érvényes virtuális géppel használandó.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv, a cikkben szereplő használja a következő ugrás, egyik funkciója, amely a következő ugrás típusa és az IP-cím erőforrás megkeresése hálózati figyelőt. Következő ugrás kapcsolatos további információkért látogasson el a [következő ugrásaként áttekintése](network-watcher-next-hop-overview.md).


## <a name="get-next-hop"></a>Következő ugrás beolvasása

A következő ugrás nevezzük eléréséhez a `az network watcher show-next-hop` parancsmag. Azt adja át a parancsmag a hálózati figyelőt erőforráscsoport NetworkWatcher, virtuális gép azonosítója, IP-forráscím, és az IP-címre. Ebben a példában a cél IP-címét, hogy egy virtuális Gépet egy másik virtuális hálózaton. Nincs a virtuális hálózati átjáró a két virtuális hálózatok között.

Ha még nem még konfigurál, a legutóbbi [Azure CLI 2.0](/cli/azure/install-az-cli2) és való bejelentkezéshez az Azure fiók használatával [az bejelentkezési](/cli/azure/#login). Ezután futtassa a következő parancsot:

```azurecli
az network watcher show-next-hop --resource-group <resourcegroupName> --vm <vmNameorID> --source-ip <source-ip> --dest-ip <destination-ip>

```

> [!NOTE]
Ha a virtuális Gépnek több hálózati adapter legyen, és IP-továbbítás engedélyezve van, bármely, a hálózati adapterek, akkor a hálózati adapter paramétert (-i nic-id) meg kell adni. Ellenkező esetben nem kötelező.

## <a name="review-results"></a>Eredmények áttekintése

Amikor végzett, a eredményei. Továbbá az erőforrás típusa a következő ugrás IP-címet adja vissza.

```azurecli
{
    "nextHopIpAddress": null,
    "nextHopType": "Internet",
    "routeTableId": "System Route"
}
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

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan ellátogatva programozott módon tekintse át a hálózati biztonsági csoport beállításainak [NSG naplózás hálózati figyelőt](network-watcher-nsg-auditing-powershell.md)
