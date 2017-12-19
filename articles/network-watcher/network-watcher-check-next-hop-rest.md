---
title: "Következő Ugrás az Azure-hálózat megfigyelő következő ugrás - REST található |} Microsoft Docs"
description: "Ez a cikk leírja, hogyan megtalálhatja a következő ugrás típusa van, és használó Azure REST API használatával, a következő ugrás IP-cím"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Megtudhatja, milyen a következő ugrás típusa a következő ugrás funkció használ, amely a következőkre hálózati figyelőt Azure REST API használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Az Azure REST API-n](network-watcher-check-next-hop-rest.md)

Következő ugrás csak a hálózati figyelő, amely a képességét get biztosít, a következő ugrás típusa és az IP-cím a megadott virtuális gép alapján. Ez a funkció akkor hasznos, meghatározni, hogy ha egy virtuális gép elhagyó forgalomra halad át egy átjárót, az interneten vagy a virtuális hálózatok a cél eléréséhez.

## <a name="before-you-begin"></a>Előkészületek

A PowerShell használatával REST API hívása ARMclient szolgál. ARMClient verziója van telepítve, chocolatey [a Chocolatey ARMClient](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv, a cikkben szereplő használja a következő ugrás, egyik funkciója, amely a következő ugrás típusa és az IP-cím erőforrás megkeresése hálózati figyelőt. Következő ugrás kapcsolatos további információkért látogasson el a [következő ugrásaként áttekintése](network-watcher-next-hop-overview.md).

Ebben a forgatókönyvben a tartalma:

* A következő ugrás a virtuális gép beolvasása.

## <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

Jelentkezzen be a Azure hitelesítő adataival armclient.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>A virtuális gép beolvasása

Futtassa a következő parancsfájl egy virtuális gép visszaállítására. Ezek az információk szükségesek a következő ugrás futtatásához.

A következő kódot a következő változó igényeihez értékek:

- **a subscriptionId** -az előfizetési azonosító használatával.
- **resourceGroupName** -virtuális gépeket tartalmazó erőforráscsoport nevét.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A virtuális gép azonosítóját a következő kimeneti használják a következő példa:

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Következő ugrás beolvasása

Az engedélyezési fejléc létrehozása után a következő ugrás a virtuális gép lehet beolvasni. A példa működéséhez a következő értékek cserélni.

> [!Important]
> A hálózati figyelő REST API-hívásokat az erőforráscsoport neve a kérés URI a hálózati figyelőt tartalmazó erőforráscsoportot nem az erőforrások hajt a diagnosztikai műveletek a.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> Következő ugrás megköveteli, hogy a virtuális gép erőforrásához lefoglalt futtatásához.

## <a name="results"></a>Results (Eredmények)

A következő kódrészletet a kimeneti kapott példája. Az eredmények tartalmazzák a következő értékeket:

* **nextHopType** -ezt az értéket a következő értékek egyike: Internet, VirtualAppliance, pedig, VnetLocal, HyperNetGateway vagy None.
* **nextHopIpAddress** -a következő ugrás IP-cím.
* **routeTableId** – vagy egy URI-azonosítóját az útvonalhoz tartozó útvonaltábla értéke, vagy ha a felhasználó által definiált nincs definiálva van-e az útvonal értékének *Rendszerútvonal* adja vissza.

Az alábbiakban az eredmények json formátumban.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Következő lépések

Ha rendelkezik-e el a következő ugrás a virtuális gép találja, megtekintheti látogasson el a hálózati erőforrások biztonságának [biztonsági nézettel áttekintése](network-watcher-security-group-view-overview.md)














