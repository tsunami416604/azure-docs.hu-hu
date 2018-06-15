---
title: Hálózati biztonsági csoport Flow naplók és Azure hálózati figyelőt - Azure CLI 1.0 kezelése |} Microsoft Docs
description: Ezen a lapon ismerteti, hogyan kezelheti a hálózati biztonsági csoport Flow-naplók az Azure CLI 1.0 Azure hálózati figyelőt
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1cdef3454a326fbdbdef5ab51ac93cc8a558f34d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
ms.locfileid: "26774488"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli-10"></a>Hálózati biztonsági csoport Flow naplók konfigurálása az Azure CLI 1.0

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Hálózati biztonsági csoport folyamat egyik funkciója, amely lehetővé teszi, hogy tekintse meg a hálózati biztonsági csoporton keresztül bemenő és kimenő IP-forgalom hálózati figyelőt feldolgozásra. A folyamat naplók json formátumban vannak megírva, és a kimenő és bejövő forgalom alapon egy szabályt, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP-, forrás vagy a cél Port protokoll), 5 rekordos információk megjelenítése, és ha a forgalom lett engedélyez vagy tilt.

Ebben a cikkben az Azure CLI 1.0 platformfüggetlen, elérhető a Windows, Mac és Linux. Hálózati figyelőt jelenleg használ Azure CLI 1.0 CLI támogatására.

## <a name="register-insights-provider"></a>Elemzések szolgáltató regisztrálása

Ahhoz, hogy sikeresen, működjön naplózás folyamata a **Microsoft.Insights** szolgáltató regisztrálva kell lennie. Ha nem tudja, ha a **Microsoft.Insights** szolgáltató regisztrálva van, futtassa a következő parancsfájlt.

```azurecli
azure provider register --namespace Microsoft.Insights --subscription <subscriptionid>
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport Flow naplók engedélyezése

A parancsot a folyamat naplók engedélyezéséhez az alábbi példában látható:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e true
```

## <a name="disable-network-security-group-flow-logs"></a>Tiltsa le a hálózati biztonsági csoport folyamata naplók

Az alábbi példa használatával tiltsa le a naplók folyamata:

```azurecli
azure network watcher configure-flow-log -g resourceGroupName -n networkWatcherName -t nsgId -i storageAccountId -e false
```

## <a name="download-a-flow-log"></a>A folyamat napló letöltése

A folyamat napló tárolási helye a létrehozásakor van definiálva. A folyamat naplók tárfiókba menti eléréséhez eszköz a Microsoft Azure Tártallózó, amely innen tölthető le: http://storageexplorer.com/

Ha egy tárfiókot meg van adva, csomag rögzítési fájlok kerülnek a storage-fiókok a következő helyen:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

A napló szerkezete információt [hálózati biztonsági csoport Flow napló áttekintése](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [a powerbi-jal az NSG folyamata naplók megjelenítése](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Megtudhatja, hogyan [jelenítheti meg az NSG folyamata naplók nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
