---
title: Automatikus méretezése az Azure Event Hubs átviteli egységek |} Microsoft Docs
description: Automatikus megnöveli a névtér automatikus méretezése az átviteli egységek engedélyezése
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 20ee0e6cff2a07cbd62a79799eada5708c7a0f07
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28018609"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Azure Event Hubs átviteli egységek automatikus méretezése

Az Azure Event Hubs egy kiválóan méretezhető adatfolyam platform. Így az Event Hubs használati gyakran nő, miután a szolgáltatás használatának megkezdéséhez. Ilyen használatához szükséges, az előre meghatározott átviteli egységek az Event Hubs skálázása, és nagyobb átviteli sebesség kezelésére növelését. A *automatikus megnöveli* az Event hubs szolgáltatás automatikusan méretezi használati igényeinek átviteli egységek számát. Átviteli egységek növelése megakadályozza, hogy a sávszélesség-szabályozás forgatókönyvek, amelyben:

* Adatok érkező díjszabás haladhatja meg a set-átviteli egységek.
* Adatok kimenő kérelem díjszabás haladhatja meg a set-átviteli egységek.

## <a name="how-auto-inflate-works"></a>Automatikus megnöveli működése

Event Hubs forgalom átviteli egységek vezérli. Egy átviteli egység lehetővé teszi, hogy 1 MB másodpercenkénti bemenő és kimenő forgalom kétszer adott mennyisége. Standard az event hubs 1 – 20 átviteli egység konfigurálható. Automatikus megnöveli lehetővé teszi a kisebb a minimálisan szükséges átviteli egységek használatával. A szolgáltatás majd alkalmazkodnak automatikusan van szükség, attól függően, hogy az adatforgalom növekedésének átviteli egységek maximális száma. Automatikus megnöveli a következő előnyökkel jár:

- Egy hatékony méretezési mechanizmus kezdje kis lépésekkel, és növelhető növelheti.
- Automatikus méretezése a megadott felső határérték nélkül szabályozási problémákat.
- Több vezérlése skálázás, meghatározhatja, mikor és milyen mértékű skála.

## <a name="enable-auto-inflate-on-a-namespace"></a>Engedélyezze az automatikus megnöveli a névtér

Engedélyezheti vagy tiltsa le az automatikus megnöveli az Event Hubs névtéren az a következő módszerek valamelyikével:

1. A [Azure-portálon](https://portal.azure.com).
2. Az Azure Resource Manager-sablon.

### <a name="enable-auto-inflate-through-the-portal"></a>Engedélyezze az automatikus megnöveli a portálon keresztül

Az automatikus megnöveli a szolgáltatás engedélyezéséhez az Event Hubs névtér létrehozása során:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

A funkció engedélyezését kezdje kis lépésekkel az átviteli egységek, és a használati mutatói igények növekedése méret. Inflációs felső határa nem azonnal befolyásolja árképzési, óránként használt átviteli egységek száma függ.

Automatikus-megnöveli használatával is engedélyezheti a **méretezési** lehetőséget a beállítások panelen a portálon:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Engedélyezze az automatikus-megnöveli Azure Resource Manager-sablonnal

Automatikus megnöveli az Azure Resource Manager sablon telepítése során engedélyezheti. Például a `isAutoInflateEnabled` tulajdonságot **igaz** és `maximumThroughputUnits` 10-re.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

A teljes sablon, tekintse meg a [létrehozása az Event Hubs-névteret és engedélyezése megnöveli](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) sablon a Githubon.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)

