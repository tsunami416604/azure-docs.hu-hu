---
title: NSG-folyamatnaplók kezelése - Azure CLI
titleSuffix: Azure Network Watcher
description: Ez a lap bemutatja, hogyan kezelhetők a network security group flow-naplók az Azure Network Watcher ben az Azure CLI segítségével
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
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840961"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Hálózati biztonsági csoportfolyamat-naplók konfigurálása az Azure CLI-vel

> [!div class="op_single_selector"]
> - [Azure-portál](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport folyamatnaplói a Network Watcher szolgáltatása, amely lehetővé teszi a hálózati biztonsági csoporton keresztül a be- és kilépésip-forgalommal kapcsolatos információkat. Ezek a folyamatnaplók json formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamat által alkalmazott hálózati adaptert, a folyamat 5-tuple információit (Forrás/cél IP, Forrás/cél port, Protokoll), és ha a forgalmat engedélyezték vagy megtagadták.

A cikkben ismertetett lépések végrehajtásához telepítenie kell [az Azure parancssori felületét Mac, Linux és Windows (CLI) rendszerhez.](/cli/azure/install-azure-cli)

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

A folyamatnaplózás sikeres működéséhez a **Microsoft.Insights-szolgáltatót** regisztrálni kell. Ha nem biztos abban, hogy a **Microsoft.Insights-szolgáltató** regisztrálva van-e, futtassa a következő parancsfájlt.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoportfolyamat-naplók engedélyezése

A folyamatnaplók engedélyezésére a következő példa jelenik meg:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

A megadott tárfiókhoz nem lehetnek olyan hálózati szabályok, amelyek csak a Microsoft-szolgáltatásokra vagy adott virtuális hálózatokra korlátozzák a hálózati hozzáférést. A tárfiók lehet ugyanabban, vagy egy másik Azure-előfizetés, mint az NSG, amely engedélyezi a folyamatnaplót. Ha különböző előfizetéseket használ, mindkettőt ugyanahhoz az Azure Active Directory-bérlőhöz kell társítani. Az egyes előfizetésekhez használt fióknak rendelkeznie kell a [szükséges engedélyekkel.](required-rbac-permissions.md) 

Ha a tárfiók a hálózati biztonsági csoporttól eltérő erőforráscsoportban vagy előfizetésben található, adja meg a tárfiók teljes azonosítóját a neve helyett. Ha például a tárfiók *egy RG-Storage*nevű erőforráscsoportban található, és nem adja meg a *storageAccountName értéket* az előző parancsban, akkor a */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName értéket kell megadnia.*

## <a name="disable-network-security-group-flow-logs"></a>Hálózati biztonsági csoportfolyamat-naplók letiltása

A folyamatnaplók letiltásához használja az alábbi példát:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Flow-napló letöltése

A folyamatnapló tárolási helye a létrehozáskor van definiálva. A tárfiókba mentett folyamatnaplók elérésének kényelmes eszköze a Microsoft Azure Storage Explorer, amely itt tölthető le:https://storageexplorer.com/

Ha meg van adva tárfiók, a folyamatnapló-fájlok a következő helyen tárolódnak egy tárfiókba:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Következő lépések

Az [NSG-folyamatnaplók visualualizálása a PowerBI-vel](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [jelenítheti meg az NSG-folyamatnaplókat nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
