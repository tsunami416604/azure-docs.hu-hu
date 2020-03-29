---
title: VNET-átjáró és kapcsolatok – Azure REST API – Hibaelhárítás
titleSuffix: Azure Network Watcher
description: Ez a lap bemutatja, hogyan háríthatók el a virtuális hálózati átjárók és kapcsolatok az Azure Network Watcherrel a REST használatával
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
ms.openlocfilehash: ab9f7fd95d7081b66e05dfd3d6a5ef47eb3c4053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840672"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Virtuális hálózati átjáró és kapcsolatok – az Azure Network Watcher használatával – problémamegoldás

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos lehetőséget kínál az Azure-beli hálózati erőforrások megértéséhez. Az egyik ilyen képesség az erőforrás-hibaelhárítás. Az erőforrás-hibaelhárítás a portálon, a PowerShellen, a CLI-n vagy a REST API-n keresztül hívható meg. A hálózatfigyelő a virtuális hálózati átjáró vagy a kapcsolat állapotát vizsgálja, és visszaadja annak eredményeit.

Ez a cikk végigvezeti az erőforrás-hibaelhárításhoz jelenleg elérhető különböző felügyeleti feladatokon.

- [**Virtuális hálózati átjáró – problémamegoldás**](#troubleshoot-a-virtual-network-gateway)
- [**Kapcsolat – problémamegoldás**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Előkészületek

Az ARMclient a REST API-t a PowerShell használatával hívja meg. ARMClient található csokoládés [armclient a Chocolatey](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit.

A támogatott átjárótípusok listájáért látogasson el a [Támogatott átjárótípusok .](network-watcher-troubleshoot-overview.md#supported-gateway-types)

## <a name="overview"></a>Áttekintés

A Network Watcher hibaelhárítása lehetővé teszi a virtuális hálózati átjárókkal és kapcsolatokkal kapcsolatos problémák elhárítását. Amikor az erőforrás hibaelhárítási kérelmet, naplók lekérdezése és ellenőrzése. Ha a vizsgálat befejeződött, az eredményeket visszaadja. Az API-kérelmek hibaelhárítási kérelmek hosszú ideig futó kérelmek, amelyek eredménye több percet is igénybe vehet. A naplók egy tárolóban tárolóban tárolóban tárolóban tárolóban tárolóban tárolóban tárolóban vannak tárolva.

## <a name="log-in-with-armclient"></a>Bejelentkezés armclient nal

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Virtuális hálózati átjáró – problémamegoldás


### <a name="post-the-troubleshoot-request"></a>Post a hibaelhárítási kérelem

A következő példa lekérdezi egy virtuális hálózati átjáró állapotát.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Mivel ez a művelet már régóta fut, a művelet lekérdezéséhez szükséges URI és az eredmény URI-ja a válaszfejlécben jelenik meg, ahogy az a következő válaszban látható:

**Fontos értékek**

* **Azure-AsyncOperation** – Ez a tulajdonság tartalmazza az Async hibaelhárítási művelet lekérdezéséhez szükséges URI-t
* **Hely** - Ez a tulajdonság tartalmazza azt az URI-t, ahol az eredmények a művelet befejezésekor vannak.

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Az aszinkron művelet befejezése

Az URI műveletek segítségével a következő példában látható módon kérdezheti a művelet előrehaladását:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Amíg a művelet folyamatban van, a válasz az **InProgress-t** mutatja a következő példában látható módon:

```json
{
  "status": "InProgress"
}
```

A művelet befejezése után az állapot **sikeresre változik.**

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Az eredmény lekérése

Miután a visszaadott állapot **sikeres,** hívja meg a GET metódust az operationResult URI-n az eredmények lekéréséhez.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Az alábbi válaszok példák egy tipikus degradált válasz, amely az átjáró hibaelhárítási eredményeinek lekérdezésekénél jelenik meg. Lásd: [Az eredmények megértése,](#understanding-the-results) hogy tisztázza, mit jelentenek a tulajdonságok a válaszban.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Kapcsolatok – problémamegoldás

A következő példa lekérdezi a kapcsolat állapotát.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> A hibaelhárítási művelet nem futtatható párhuzamosan a kapcsolaton és a hozzátartozók átjáróin. A műveletnek be kell fejeződnie, mielőtt az előző erőforráson futtatna.

Mivel ez egy hosszú ideig futó tranzakció, a válaszfejlécben a művelet lekérdezéséhez szükséges URI és az eredmény URI-ja a következő válaszban látható módon jelenik meg:

**Fontos értékek**

* **Azure-AsyncOperation** – Ez a tulajdonság tartalmazza az Async hibaelhárítási művelet lekérdezéséhez szükséges URI-t
* **Hely** - Ez a tulajdonság tartalmazza azt az URI-t, ahol az eredmények a művelet befejezésekor vannak.

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Az aszinkron művelet befejezése

Az URI műveletek segítségével a következő példában látható módon kérdezheti a művelet előrehaladását:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Amíg a művelet folyamatban van, a válasz az **InProgress-t** mutatja a következő példában látható módon:

```json
{
  "status": "InProgress"
}
```

Ha a művelet befejeződött, az állapot Sikeres állapotra **változik.**

```json
{
  "status": "Succeeded"
}
```

A következő válaszok példák egy tipikus válasz, amely a kapcsolat hibaelhárítási eredményeinek lekérdezésekénél jelenik meg.

### <a name="retrieve-the-results"></a>Az eredmény lekérése

Miután a visszaadott állapot **sikeres,** hívja meg a GET metódust az operationResult URI-n az eredmények lekéréséhez.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

A következő válaszok példák egy tipikus válasz, amely a kapcsolat hibaelhárítási eredményeinek lekérdezésekénél jelenik meg.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Az eredmények megértése

A műveletszöveg általános útmutatást ad a probléma megoldásához. Ha a probléma érdekében lehet műveletet végrehajtani, a hivatkozás további útmutatást kap. Abban az esetben, ha nincs további útmutatást, a válasz megadja az URL-t, hogy nyissa meg a támogatási eset.  A válasz tulajdonságairól és a benne foglalt információkról a [Network Watcher hibaelhárítás – áttekintés című témakörben olvashat bővebben.](network-watcher-troubleshoot-overview.md)

A fájlok Azure storage-fiókokból való letöltésével kapcsolatos tudnivalókat az [Azure Blob storage .NET használatával történő használatának első lépései .](../storage/blobs/storage-dotnet-how-to-use-blobs.md) Egy másik eszköz, amely használható a Storage Explorer. További információ a Storage Explorer ről itt található az alábbi linken: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha olyan beállítások módosultak, amelyek leállítják a VPN-kapcsolatot, olvassa el a [Hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) című témakört a kérdéses hálózati biztonsági csoport és biztonsági szabályok nyomon követéséhez.
