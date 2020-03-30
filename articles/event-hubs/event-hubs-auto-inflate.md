---
title: Az átviteli egységek automatikus felskálázása - Azure Event Hubs | Microsoft dokumentumok
description: Engedélyezze az automatikus felfújást egy névtérben az átviteli egységek automatikus méretezéséhez.
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
ms.openlocfilehash: dc6edaebebe89b6d4a35ada58d40795f86a935d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264477"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Az Azure Event Hubs átviteli egységeinek automatikus skálázása
Az Azure Event Hubs egy jól méretezhető adatfolyam-továbbítási platform. Mint ilyen, az Event Hubs használata gyakran növekszik a szolgáltatás használatának megkezdése után. Az ilyen használat az előre meghatározott [átviteli egységek](event-hubs-scalability.md#throughput-units) növelését igényli az Eseményközpontok méretezéséhez és a nagyobb átviteli sebesség kezeléséhez. Az Event Hubs **automatikus felfújási** funkciója automatikusan felskálázódik az átviteli egységek számának növelésével, a használati igények nek megfelelően. Az átviteli egységek növelése megakadályozza a sávszélesség-szabályozási forgatókönyveket, amelyekben:

* Az adatbe- és be- és adatátviteli sebességek meghaladják a beállított átviteli egységeket.
* Az adatforgalom kérési sebessége meghaladja a beállított átviteli egységeket.

Az Event Hubs szolgáltatás növeli az átviteli terhelést, ha a terhelés meghaladja a minimális küszöbértéket, anélkül, hogy a ServerBusy hibákkal kapcsolatos kérelmek sikertelenek lennenek.

## <a name="how-auto-inflate-works"></a>Az automatikus felfújás működése

Az Event Hubs-forgalmat [átviteli egységek](event-hubs-scalability.md#throughput-units)szabályozzák. Egyetlen átviteli egység lehetővé teszi, hogy 1 MB másodpercenként a be- és kétszer annyi kimenő. A szabványos eseményközpontok 1–20 átviteli egységgel konfigurálhatók. Az automatikus felfújás lehetővé teszi, hogy kicsiben induljon a kiválasztott minimális átviteli egységekkel. A szolgáltatás ezután automatikusan átméretezi a szükséges átviteli egységek maximális korlátjára, a forgalom növekedésétől függően. Az automatikus felfújás a következő előnyöket nyújtja:

- Hatékony skálázási mechanizmus a kisméretezéshez és a növekedéshez.
- Automatikusan méretezhető a megadott felső korlátra szabályozási problémák nélkül.
- A méretezés hatékonyabb szabályozása, mivel ön szabályozza a méretezés mértékét és mértékét.

## <a name="enable-auto-inflate-on-a-namespace"></a>Automatikus felfújás engedélyezése névtéren

A standard szintű Event Hubs névtérben az alábbi módszerek egyikével engedélyezheti vagy letilthatja az automatikus felfújást:

- Az [Azure portal](https://portal.azure.com).
- Egy [Azure Resource Manager-sablon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)

> [!NOTE]
> Az alapszintű eseményközpontok névterei nem támogatják az automatikus felfújást.

### <a name="enable-auto-inflate-through-the-portal"></a>Automatikus felfújás engedélyezése a portálon keresztül


#### <a name="enable-at-the-time-of-creation"></a>Engedélyezés a létrehozás időpontjában 
Az Automatikus felfújás funkciót **az Event Hubs névtér létrehozásakor**engedélyezheti:
 
![Automatikus felfújás engedélyezése az eseményközpont létrehozásakor](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Ha ez a beállítás engedélyezve van, az átviteli egységek segítségével kicsiben kezdhet, és a használati igények növekedésével felskálázható. Az infláció felső határa nem befolyásolja azonnal az árképzést, ami az óránként használt átviteli egységek számától függ.

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>Automatikus felfújás engedélyezése meglévő eseményközponthoz
Az Automatikus felfújás funkciót is engedélyezheti, és a következő utasítások segítségével módosíthatja a beállításokat: 
 
1. Az **Event Hubs Névtér** lapon válassza a **Letiltva** lehetőséget az **átviteli egységek automatikus felfújása**csoportban.  

    ![Átviteli egységek kijelölése az Event Hubs névtér lapján](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. A **Méretezési beállítások** lapon jelölje be az **Engedélyezés** jelölőnégyzetet (ha az automatikus skálázási funkció nincs engedélyezve).

    ![Válassza az Engedélyezés lehetőséget](./media/event-hubs-auto-inflate/scale-settings.png)
3. Adja meg az átviteli egységek **maximális** számát, vagy a görgetősáv segítségével állítsa be az értéket. 
4. (nem kötelező) Frissítse a lap tetején található átviteli egységek **minimális** számát. 


> [!NOTE]
> Amikor az automatikus felfújási konfigurációt az átviteli egységek növelésére alkalmazza, az Event Hubs szolgáltatás diagnosztikai naplókat bocsát ki, amelyek információt nyújtanak arról, hogy miért és mikor nőtt az átviteli forgalom. Ha engedélyezni szeretné egy eseményközpont diagnosztikai naplózását, válassza a **diagnosztikai beállítások at** a bal oldali menüben az Azure Portal On Event Hub lapján. További információ: [Diagnosztikai naplók beállítása egy Azure-eseményközponthoz](event-hubs-diagnostic-logs.md)című témakörben talál. 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Automatikus felfújás engedélyezése Azure Resource Manager-sablon használatával

Az Azure Resource Manager-sablon üzembe helyezése során engedélyezheti az automatikus felfújást. Állítsa például `isAutoInflateEnabled` **a** tulajdonságot `maximumThroughputUnits` igaz értékre, és állítsa 10-re. Példa:

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

A teljes sablon, tekintse meg az [Event Hubs létrehozása névtér, és engedélyezze felfújni](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) sablon taweb.For the complete template, see the Create Event Hubs namespace and enable inflate template on GitHub.


## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Eseményközpontok – áttekintés](event-hubs-what-is-event-hubs.md)

