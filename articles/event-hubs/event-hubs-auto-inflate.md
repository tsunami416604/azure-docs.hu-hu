---
title: Automatikus vertikális felskálázás átviteli egységek – Azure Event Hubs |} A Microsoft Docs
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
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d705993c7cd3816e89da21625dc5b003435b9128
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408161"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Automatikus vertikális felskálázás az Azure Event Hubs átviteli egységek
Az Azure Event Hubs egy kiválóan méretezhető adatstreamelési platform. Mint ilyen az Event Hubs használatának gyakran nő, miután a szolgáltatás használatát. Az ilyen használatához szükséges, növelje az előre meghatározott [átviteli egységek](event-hubs-features.md#throughput-units) az Event Hubs méretezhető, és a nagyobb átviteli sebességre kezelésére. A **automatikus feltöltésről** az Event hubs szolgáltatás automatikusan felskálázással növelje átviteli egységek számát, a használattal kapcsolatos igények alapján. Átviteli egységek növelése megakadályozza, hogy a szabályozási forgatókönyvek, ahol:

* Bejövő forgalom díjait meghaladják a beállított kapacitásegységek.
* Adatok kimenő kérelemarányok haladhatja meg a set-átviteli egységek.

Az Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés növekszik a minimális küszöbérték ServerBusy hiba miatt sikertelenül működő kérések nélkül.

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


#### <a name="enable-at-the-time-of-creation"></a>A létrehozás időpontjában engedélyezése 
Engedélyezheti az automatikus feltöltési funkció **Event Hubs-névtér létrehozásakor**:
 
![Automatikus feltöltés az idő eseményközpont létrehozása:](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Ez a beállítás engedélyezve van, a kis kezdje az átviteli egységek és a méret a használat növelése igényei szerint. A felső határ az infláció nem azonnal befolyásolja díjszabás, az óránként felhasznált átviteli egységek számától függ.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Automatikus feltöltés engedélyezése egy meglévő eseményközpont
Engedélyezze az automatikus feltöltési funkció is, és módosítja annak beállításait az alábbi utasítások segítségével: 
 
1. Az a **Event Hubs-Namespace** lapon jelölje be **letiltott** alatt **átviteli egységek automatikus feltöltésről**.  

    ![Válassza ki az átviteli egységek az Event Hubs-névtér oldalon](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. Az a **méretezési beállítások** lapra, jelölje be a jelölőnégyzetet a **engedélyezése** (ha az automatikus skálázási funkció nincs engedélyezve).

    ![Válassza ki a engedélyezése](./media/event-hubs-auto-inflate/scale-settings.png)
3. Adja meg a **maximális** átviteli egységek vagy a görgetősávok használata értéket a száma. 
4. (nem kötelező) Frissítés a **minimális** Ez az oldal tetején lévő átviteli egységek száma. 


> [!NOTE]
> Ha Ön a alkalmazni az automatikus feltöltésről konfigurációját, és növelni az átviteli egységek, az Event Hubs szolgáltatás bocsát ki, miért és mikor az átviteli sebesség nagyobb információkkal szolgálnak a diagnosztikai naplók. Diagnosztikai naplózás az eseményközpontba engedélyezéséhez jelölje be **diagnosztikai beállítások** a bal oldali menüben az Event Hub oldalon az Azure Portalon. További információkért lásd: [állítsa be a diagnosztikai naplók az Azure event hub](event-hubs-diagnostic-logs.md). 

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

