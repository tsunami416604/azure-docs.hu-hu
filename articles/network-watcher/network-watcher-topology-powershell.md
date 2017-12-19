---
title: "Azure hálózati figyelőt topológia - PowerShell megtekintése |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan lehet a hálózati topológia lekérdezése a PowerShell használatával."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>A PowerShell-lel hálózati figyelőt topológia megtekintése

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [REST API](network-watcher-topology-rest.md)

A hálózati figyelőt topológia szolgáltatása a hálózati erőforrások, az előfizetés vizuális ábrázolását. A portál Ez a képi megjelenítés áll rendelkezésre, automatikusan. Az adatokat a topológia e nézetében a portálon mögött PowerShell kérhető.
Ez a funkció lehetővé teszi a topológiainfomációja rugalmasabb, az adatok a képi megjelenítés felé más eszközökkel feldolgozottként.

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

Ebben az esetben használhatja a `Get-AzureRmNetworkWatcherTopology` parancsmag használatával beolvassa a topológia információkat. Van még egy cikk való [beolvasni a REST API-t a hálózati topológia](network-watcher-topology-rest.md).

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv a cikkben szereplő lekéri a topológia választ adott erőforráscsoport.

## <a name="retrieve-network-watcher"></a>Hálózati figyelőt beolvasása

Az első lépés a hálózati figyelőt példányának lekéréséhez. A `$networkWatcher` változó átadott a `Get-AzureRmNetworkWatcherTopology` parancsmag.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Topológia beolvasása

A `Get-AzureRmNetworkWatcherTopology` parancsmag beolvassa a megadott erőforráscsoport-topológiát.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Results (Eredmények)

A visszaadott eredmények rendelkezik tulajdonsággal "Resources" nevét, amely tartalmazza a json válasz törzse a `Get-AzureRmNetworkWatcherTopology` parancsmag.  A válasz tartalmazza a hálózati biztonsági csoport és a társításokat (Ez azt jelenti, hogy a tartalmazza, a társított) erőforrásaihoz.

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan jelenítheti meg az NSG folyamata naplók a Power BI ellátogatva [megjelenítése NSG forgalomáramlás naplók és a Power bi-ban](network-watcher-visualize-nsg-flow-logs-power-bi.md)


