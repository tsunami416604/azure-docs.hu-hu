---
title: Virtuális hálózati átjáró és az Azure Network Watcher – REST használó kapcsolatok hibaelhárítása |} A Microsoft Docs
description: Jelen lap bemutatja, hogyan háríthatók el a virtuális hálózati átjárók és kapcsolatok az Azure Network Watcher REST használatával
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: e3c172a0e88988acfb0244fff4603dc644aee03f
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091023"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Virtuális hálózati átjáró és az Azure Network Watcher segítségével kapcsolatok hibaelhárítása

> [!div class="op_single_selector"]
> - [Portál](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

A Network Watcher számos funkciót kínál a ismertetése az Azure-ban a hálózati erőforrások vonatkozik. Ezek a képességek egyik erőforrás hibaelhárítás. Erőforrások hibaelhárítása a portal, PowerShell, CLI vagy REST API használatával is meghívható. Meghívni, Network Watcher megvizsgálja a virtuális hálózati átjáró vagy a kapcsolat állapotát, és a csapatával az eredményeket adja vissza.

Ez a cikk végigvezeti a különböző felügyeleti feladatok, amelyek jelenleg az erőforrás hibaelhárítás.

- [**A virtuális hálózati átjáró hibáinak elhárítása**](#troubleshoot-a-virtual-network-gateway)
- [**Kapcsolat hibaelhárítása**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Előkészületek

ARMclient hívás a REST API, PowerShell-lel történik. ARMClient megtalálható a chocolatey [ARMClient a chocolatey-t](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához.

Látogasson el a támogatott átjáró típusok, listáját [támogatott átjárótípusok](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Áttekintés

Network Watcher troubleshooting lehetővé teszi a virtuális hálózati átjárók és kapcsolatok felmerülő problémák hibaelhárításához. Az erőforrás hibaelhárítás a kérelem elküldésekor a naplók kérdez le, és megvizsgálni. Ha az ellenőrzés befejeződött, a rendszer visszairányítja az eredményeket. A hibaelhárítás API-kérések hosszúak futó kérelmek, amelyek adja vissza az eredményt több percet is igénybe vehet. A storage-fiók egy tároló naplókat tárolja.

## <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

```PowerShell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>A virtuális hálózati átjáró hibáinak elhárítása


### <a name="post-the-troubleshoot-request"></a>A hibaelhárítás kérelem KÜLDÉSE

Az alábbi példa egy virtuális hálózati átjáró állapotának lekérdezése.

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

Mivel ez a művelet hosszú fut, a művelet és az URI-t lekérdezéséhez, az eredményt adja vissza a fejlécet, ahogyan az a következő választ az URI:

**Fontos értéket**

* **Az Azure-AsyncOperation** – Ez a tulajdonság tartalmazza az URI-t a lekérdezés az aszinkron művelet hibaelhárítása
* **Hely** – Ez a tulajdonság tartalmazza az URI-t, hol találhatók az eredmények, a művelet befejezésekor

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

### <a name="query-the-async-operation-for-completion"></a>Lekérdezés az aszinkron művelet befejezésére

A lekérdezési műveletek URI-t használja a műveletnek az előrehaladását az alábbi példában látható módon:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Amíg a művelet folyamatban van, a válasz megjeleníti **InProgress** az alábbi példában látható módon:

```json
{
  "status": "InProgress"
}
```

A művelet befejezésekor az állapot változik **sikeres**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Kérje le az eredményeket

Miután az állapot visszaadott **sikeres**, egy GET metódust hívja meg az operationresult adatokat a URI-t kérje le az eredményeket.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Példák a következő válaszok tipikus csökkentett teljesítményű választ adott vissza az eredményeket a hibaelhárítási átjáró lekérdezésekor. Lásd: [ismertetése az eredmények](#understanding-the-results) tisztázása érdekében a válaszban szereplő tulajdonságok jelenti be.

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
          "actionUri": "http://azure.microsoft.com/support",
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
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Kapcsolatok hibaelhárítása

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
> A hibaelhárítás műveletet nem lehet futtatni, párhuzamos kapcsolatot és a megfelelő átjárók. A művelet azt az előző erőforráson futtatása előtt kell be.

Mivel ez egy hosszú ideig futó tranzakció, a fejlécet, ahogyan az a következő választ az URI-t, a művelet, és az eredmény URI Azonosítóját adja vissza:

**Fontos értéket**

* **Az Azure-AsyncOperation** – Ez a tulajdonság tartalmazza az URI-t a lekérdezés az aszinkron művelet hibaelhárítása
* **Hely** – Ez a tulajdonság tartalmazza az URI-t, hol találhatók az eredmények, a művelet befejezésekor

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

### <a name="query-the-async-operation-for-completion"></a>Lekérdezés az aszinkron művelet befejezésére

A lekérdezési műveletek URI-t használja a műveletnek az előrehaladását az alábbi példában látható módon:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Amíg a művelet folyamatban van, a válasz megjeleníti **InProgress** az alábbi példában látható módon:

```json
{
  "status": "InProgress"
}
```

A művelet befejeződése után állapotra vált **sikeres**.

```json
{
  "status": "Succeeded"
}
```

Példák a következő válaszok tipikus választ adott vissza az eredményeket egy kapcsolatot a hibaelhárítás lekérdezésekor.

### <a name="retrieve-the-results"></a>Kérje le az eredményeket

Miután az állapot visszaadott **sikeres**, egy GET metódust hívja meg az operationresult adatokat a URI-t kérje le az eredményeket.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Példák a következő válaszok tipikus választ adott vissza az eredményeket egy kapcsolatot a hibaelhárítás lekérdezésekor.

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
          "actionUri": "http://azure.microsoft.com/support",
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
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Az eredmények ismertetése

A művelet szöveg nyújt általános útmutatást a probléma megoldásához. Egy műveletet elvégezhet a problémára, ha egy hivatkozást a további útmutatást. Abban az esetben, ha nincsenek további útmutatást, a válasz biztosít támogatási eset nyitása URL-címét.  A válasz és foglalt tulajdonságaival kapcsolatos további információkért látogasson el a [Network Watcher hibaelhárítása – áttekintés](network-watcher-troubleshoot-overview.md)

Fájlok letöltése az azure storage-fiókokra vonatkozó utasításokért tekintse meg [.NET használatával az Azure Blob storage használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Egy másik eszköz használható a Storage Explorer. További információ a Storage Explorer itt található, a következő hivatkozásra: [Storage Explorerrel](http://storageexplorer.com/)

## <a name="next-steps"></a>További lépések

Ha-beállításai lettek módosítva lett, hogy állítsa le VPN-kapcsolat, tekintse meg [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) nyomon követheti a hálózati biztonsági csoport és biztonsági szabályok, amelyek az adott lehetnek.
