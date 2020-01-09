---
title: NSG-adatforgalmi naplók kezelése – Azure CLI
titleSuffix: Azure Network Watcher
description: Ez az oldal ismerteti, hogyan kezelhetők a hálózati biztonsági csoport folyamatábrái az Azure Network Watcher Az Azure CLI-vel
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 21026a3de2b9feb11bc84cbdb60d4a363bd5a05c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454270"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Hálózati biztonsági csoport Folyamatábráinak konfigurálása az Azure CLI-vel

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport folyamatábrái a Network Watcher szolgáltatása, amely lehetővé teszi, hogy a hálózati biztonsági csoporton keresztül megtekintse a bejövő és kimenő IP-forgalomra vonatkozó információkat. Ezeket a folyamatokat a rendszer JSON formátumban írja be, és a kimenő és bejövő folyamatokat egy szabály alapján jeleníti meg, a flow a folyamatra vonatkozik, 5 rekordos információ a folyamatról (forrás/cél IP-cím, forrás/cél port, protokoll), és ha a forgalom engedélyezett vagy megtagadott volt.

A cikkben szereplő lépések végrehajtásához [telepítenie kell az Azure parancssori felületét Mac, Linux és Windows rendszerre (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamatok naplózása sikeresen működjön, regisztrálni kell a **Microsoft. bepillantást** nyújtó szolgáltatót. Ha nem biztos abban, hogy a **Microsoft.** betekintő szolgáltató regisztrálva van, futtassa az alábbi szkriptet.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport Folyamatábráinak engedélyezése

A flow-naplók engedélyezésére szolgáló parancs az alábbi példában látható:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

A megadott Storage-fiók nem rendelkezhet olyan hálózati szabályokkal, amelyek csak a Microsoft-szolgáltatásokhoz vagy adott virtuális hálózatokhoz való hálózati hozzáférést korlátozzák. A Storage-fiók azonos vagy egy másik Azure-előfizetéssel is rendelkezhet, mint a NSG. Ha különböző előfizetéseket használ, mindkettőhöz ugyanahhoz a Azure Active Directory bérlőhöz kell tartoznia. Az egyes előfizetésekhez használt fióknak rendelkeznie kell a [szükséges engedélyekkel](required-rbac-permissions.md). 

Ha a Storage-fiók egy másik erőforráscsoporthoz vagy előfizetéshez kapcsolódik, mint a hálózati biztonsági csoport, a neve helyett adja meg a Storage-fiók teljes AZONOSÍTÓját. Ha például a Storage-fiók egy *RG-Storage*nevű erőforráscsoporthoz van megadva, ahelyett, hogy *storageAccountName* az előző parancsban, akkor a */Subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/Providers/Microsoft.Storage/storageAccounts/storageAccountName*-t kell megadnia.

## <a name="disable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport Folyamatábráinak letiltása

A következő példa használatával tiltsa le a folyamat naplóit:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Flow-napló letöltése

A folyamat naplójának tárolási helye a létrehozáskor van meghatározva. A rendszer a Storage-fiókba mentett adatforgalmi naplók elérésére alkalmas eszközt Microsoft Azure Storage Explorer, amely letölthető innen: https://storageexplorer.com/

Ha meg van adva Storage-fiók, a rendszer a flow-naplófájlokat a következő helyen menti a Storage-fiókba:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [jelenítheti meg a NSG flow-naplókat a PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) használatával

Ismerje meg, hogyan [jelenítheti meg a NSG-flow-naplókat nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
