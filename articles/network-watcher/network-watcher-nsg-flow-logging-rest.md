---
title: Az Azure Network Watcher – REST API-t kezelheti a hálózati biztonsági csoport folyamatnaplóit |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan kezelheti a hálózati biztonsági csoportok folyamatnaplóit az Azure Network Watcher REST API-val
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 003335aad0452e7a2dbfff49ed29a6b99b5d54d2
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089629"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Hálózati biztonsági csoport beállítása Folyamatnaplók REST API használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Hálózati biztonsági csoportok folyamatnaplóit érhetők el a Network Watcher, amely lehetővé teszi a bejövő és kimenő IP-forgalom hálózati biztonsági csoport használatával kapcsolatos információk megtekintéséhez. A folyamat-naplók json formátumban íródtak, és a kimenő és bejövő folyamatok megjelenítése / szabály történik, a hálózati Adapterhez, a folyamat vonatkozik, a folyamat (a forrás és cél IP-cím, forrás és a cél-Port, protokoll), 5-ször több információt, és ha a forgalom engedélyezett vagy tiltott.

## <a name="before-you-begin"></a>Előkészületek

ARMclient hívás a REST API, PowerShell-lel történik. ARMClient megtalálható a chocolatey [ARMClient a chocolatey-t](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához.

> [!Important]
> A Network Watcher REST API-hívásokat az erőforráscsoport neve a kérés URI az az erőforráscsoport, amely tartalmazza a Network Watcher az erőforrások nem végez a diagnosztikai műveletek.

## <a name="scenario"></a>Forgatókönyv

Ebben a cikkben ismertetett forgatókönyv bemutatja, hogyan engedélyezheti, tiltsa le, és lekérdezése a REST API-val Folyamatnaplók. Hálózati biztonsági csoport a folyamat loggings kapcsolatos további információkért látogasson el [hálózati biztonsági csoportforgalom naplózása – áttekintés](network-watcher-nsg-flow-logging-overview.md).

Ebben a forgatókönyvben tartalma:

* Forgalmi naplók engedélyezése
* Tiltsa le a folyamat-Naplók
* Folyamat-naplók állapot lekérdezése

## <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

Jelentkezzen be Azure hitelesítő adataival armclient.

```PowerShell
armclient login
```

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamat sikeres működéséhez naplózása a **Microsoft.Insights** szolgáltató regisztrálva kell lennie. Ha nem, hogy ha a **Microsoft.Insights** szolgáltató regisztrálva van, futtassa a következő szkriptet.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Engedélyezze a hálózati biztonsági csoport folyamatnaplóit

A parancs a folyamat-naplók engedélyezésére az alábbi példában látható:

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Az előző példából származó visszaadott válasz a következőképpen történik:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Tiltsa le a hálózati biztonsági csoportok folyamatnaplóit

Az alábbi példa használatával tiltsa le a folyamat-naplók. A hívás nem ugyanaz, mint folyamatnaplóit, így kivételével **hamis** az engedélyezett tulajdonság értéke.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Az előző példából származó visszaadott válasz a következőképpen történik:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    }
  }
}
```

## <a name="query-flow-logs"></a>Lekérdezési folyamat-Naplók

A hálózati biztonsági csoport bejelentkezik a folyamat állapotát a következő REST-hívást lekérdezéseket.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Az alábbiakban látható egy példa a visszaadott válasz:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    }
  }
}
```

## <a name="download-a-flow-log"></a>Egy flow-napló letöltése

A tárolási helye egy flow-log létrehozásakor van meghatározva. Ezen forgalmi naplók tárfiókba menti el az eszköz itt tölthető le a Microsoft Azure Storage Explorerben:  http://storageexplorer.com/

Ha egy storage-fiók van megadva, packet capture fájlok egy storage-fiókba, a következő helyen találhatóak meg:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [jelenítheti meg az NSG-Folyamatnaplók a powerbi használatával](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [jelenítheti meg az NSG-Folyamatnaplók nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
