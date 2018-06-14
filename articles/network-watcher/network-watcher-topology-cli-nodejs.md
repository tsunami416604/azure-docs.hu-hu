---
title: Azure hálózati figyelőt topológia - Azure CLI 1.0 megtekintése |} Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan Azure CLI 1.0 használata a hálózati topológia lekérdezni.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bf12e1bde56c06e496d29ad27ba3da65cd94629e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23864048"
---
# <a name="view-network-watcher-topology-with-azure-cli-10"></a>Azure CLI 1.0 hálózati figyelőt topológia megtekintése

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

A hálózati figyelőt topológia szolgáltatása a hálózati erőforrások, az előfizetés vizuális ábrázolását. A portál Ez a képi megjelenítés áll rendelkezésre, automatikusan. Az adatokat a topológia e nézetében a portálon mögött PowerShell kérhető.
Ez a funkció lehetővé teszi a topológiainfomációja rugalmasabb, az adatok a képi megjelenítés felé más eszközökkel feldolgozottként.

Ebben a cikkben az Azure CLI 1.0 platformfüggetlen, elérhető a Windows, Mac és Linux. 

Az összekapcsolási két kapcsolatok alapján van modellezve.

- **Befoglaltsági** -példa: virtuális hálózat tartalmaz egy alhálózat egy hálózati Adaptert tartalmaz
- **Kapcsolódó** -példa: a hálózati adapter kapcsolódik a virtuális gépek

Az alábbi lista a topológia REST API lekérdezésekor visszaadott tulajdonságait.

* **név** – az erőforrás neve
* **azonosító** -erőforrás uri.
* **hely** – a hely, ahol az erőforrás található.
* **társítások** -társítást a hivatkozott objektum listáját.
    * **név** -a hivatkozott erőforrás nevét.
    * **resourceId** – az erőforrás-azonosítója a található a hivatkozott erőforrás uri.
    * **associationType** -ezt az értéket a gyermekobjektum és a szülő közötti kapcsolat hivatkozik. Érvényes értékek a következők **Contains** vagy **társított**.

## <a name="before-you-begin"></a>Előkészületek

Ebben az esetben használhatja a `network watcher topology` parancsmag használatával beolvassa a topológia információkat. Van még egy cikk való [beolvasni a REST API-t a hálózati topológia](network-watcher-topology-rest.md).

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv a cikkben szereplő lekéri a topológia választ adott erőforráscsoport.

## <a name="retrieve-topology"></a>Topológia beolvasása

A `network watcher topology` parancsmag beolvassa a megadott erőforráscsoport-topológiát. Adja hozzá a következő argumentum "--json" megtekintéséhez a oput json formátumban

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Results (Eredmények)

A visszaadott eredmények rendelkezik tulajdonsággal "Resources" nevét, amely tartalmazza a json válasz törzse a `network watcher topology` parancsmag.  A válasz tartalmazza a hálózati biztonsági csoport és a társításokat (Ez azt jelenti, hogy a tartalmazza, a társított) erőforrásaihoz.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>További lépések

További információ a biztonsági szabályok látogasson el a hálózati erőforrások alkalmazott [biztonsági csoport megtekintése – áttekintés](network-watcher-security-group-view-overview.md)
