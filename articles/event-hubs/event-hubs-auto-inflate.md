---
title: Automatikus vertikális felskálázás az Azure Event Hubs átviteli egységek |} A Microsoft Docs
description: Automatikus feltöltés engedélyezése a névtér automatikusan növelheti az átviteli egységek.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 32f99b43a37277e70d209f1f315dcb398c2b5931
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004792"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatikus vertikális felskálázás az Azure Event Hubs átviteli egységek

Az Azure Event Hubs egy kiválóan méretezhető adatstreamelési platform. Mint ilyen az Event Hubs használatának gyakran nő, miután a szolgáltatás használatát. Az ilyen használatához szükséges, növelje az előre meghatározott [átviteli egységek](event-hubs-features.md#throughput-units) az Event Hubs méretezhető, és a nagyobb átviteli sebességre kezelésére. A **automatikus feltöltésről** az Event hubs szolgáltatás automatikusan felskálázással növelje átviteli egységek számát, a használattal kapcsolatos igények alapján. Átviteli egységek növelése megakadályozza, hogy a szabályozási forgatókönyvek, ahol:

* Bejövő forgalom díjait meghaladják a beállított kapacitásegységek.
* Adatok kimenő kérelemarányok haladhatja meg a set-átviteli egységek.

## <a name="how-auto-inflate-works"></a>Az automatikus feltöltési működése

Event Hubs forgalmat kezelnek [átviteli egységek](event-hubs-features.md#throughput-units). Egy átviteli egység 1 MB másodpercenként bejövő és kimenő forgalom kétszer adott mennyiségű lehetővé teszi. Standard szintű event hubs 1 – 20 átviteli egység is konfigurálhatók. Az automatikus feltöltési lehetővé teszi, hogy kezdjen kicsiben a minimálisan szükséges átviteli egységek választja. A szolgáltatás majd méretezhetőségének automatikusan van szükség, attól függően, az adatforgalom növekedésének kapacitásegységek maximális számát. Automatikus feltöltés az alábbi előnyöket nyújtja:

- Egy olyan hatékony méretezés mechanizmust kezdhetik, és a növekedés arányában méret.
- Szabályozási problémák nélkül, automatikusan skálázhatja a megadott felső korlátot.
- További szabályozhatóbbá méretezés, mert meghatározhatja, mikor, és mekkora a méretezési csoport.

## <a name="enable-auto-inflate-on-a-namespace"></a>Automatikus feltöltés engedélyezése egy névtérhez

Engedélyezheti vagy letilthatja az automatikus feltöltési az Event Hubs-névtér az alábbi módszerek egyikével:

- A [az Azure portal](https://portal.azure.com).
- Egy [Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Automatikus feltöltés engedélyezése a portálon keresztül

Event Hubs-névtér létrehozásakor engedélyezheti az automatikus feltöltési funkció:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Ez a beállítás engedélyezve van, a kis kezdje az átviteli egységek és a méret a használat növelése igényei szerint. A felső határ az infláció nem azonnal befolyásolja díjszabás, az óránként felhasznált átviteli egységek számától függ.

Az automatikus feltöltési használatával is engedélyezheti a **méretezési** lehetőséget a beállítások panel a portálon:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Engedélyezze az automatikus feltöltésről egy Azure Resource Manager-sablon használatával

Az automatikus feltöltési egy Azure Resource Manager-sablon telepítése során engedélyezheti. Például állítsa be a `isAutoInflateEnabled` tulajdonságot **igaz** és `maximumThroughputUnits` 10-re. Példa:

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

A teljes sablont, tekintse meg a [létrehozása az Event Hubs-névtér és a feltöltés engedélyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) sablon a Githubon.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)

