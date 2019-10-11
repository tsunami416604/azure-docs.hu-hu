---
title: Event Hubs eseményforrás hozzáadása a Azure Time Series Insightshoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adható hozzá az Azure Event Hubshoz csatlakoztatott eseményforrás a Time Series Insights-környezethez.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: aaddfb19889e31bb8e0d52d1df2d6b034b6e7f6b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274355"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Event hub-eseményforrás hozzáadása a Time Series Insights-környezethez

Ez a cikk azt ismerteti, hogyan használható a Azure Portal egy olyan eseményforrás hozzáadásához, amely adatokat olvas be az Azure Event Hubsból a Azure Time Series Insights környezetbe.

> [!NOTE]
> A cikkben ismertetett lépések a Time Series Insights GA és a Time Series Insights előnézeti környezetekre egyaránt érvényesek.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy Time Series Insights környezetet a [Azure Time Series Insights-környezet létrehozása](./time-series-insights-update-create-environment.md)című témakörben leírtak szerint.
- Eseményközpont létrehozása. Lásd: [Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](../event-hubs/event-hubs-create.md).
- Az Event hub-nak aktív üzenetküldési eseményekkel kell rendelkeznie. Ismerje meg, hogyan [küldhet eseményeket az Azure Event Hubs a .NET-keretrendszer használatával](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Hozzon létre egy dedikált fogyasztói csoportot az Event hub-ban, amelyet a Time Series Insights-környezet képes használni. Minden Time Series Insights eseményforrás saját dedikált fogyasztói csoportjának kell lennie, amely nincs más fogyasztóval megosztva. Ha több olvasó is felhasználja az azonos fogyasztói csoportból származó eseményeket, az összes olvasó valószínűleg hibákat fog látni. Az Event hub-ban legfeljebb 20 fogyasztói csoport lehet. Részletekért lásd: [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Fogyasztói csoport hozzáadása az Event hub-hoz

Az alkalmazások fogyasztói csoportokat használnak az adatok Azure-Event Hubsból való lekéréséhez. Az Event hub adatainak megbízható beolvasásához adjon meg egy dedikált fogyasztói csoportot, amelyet csak ez a Time Series Insights-környezet használ.

Új fogyasztói csoport hozzáadása az Event hub-ban:

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az Event hub-t az Event hub névterében.

    [@no__t – 1Nyissuk meg az Event hub-névteret](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Az **entitások**területen válassza a **fogyasztói csoportok**lehetőséget, majd válassza a **fogyasztói csoport**elemet.

   [![Event hub – fogyasztói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. A **fogyasztói csoportok** lapon adjon meg egy új egyedi értéket a **név**mezőben.  Akkor használja ezt a nevet, amikor új eseményforrás hoz létre a Time Series Insights környezetben.

1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-new-event-source"></a>Új eseményforrás hozzáadása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetét. A bal oldali menüben válassza a **minden erőforrás**lehetőséget, majd válassza ki a Time Series Insights-környezetet.

1. A **környezeti topológia**területen válassza az **eseményforrás**lehetőséget, majd kattintson a **Hozzáadás**gombra.

   [@no__t – 1Under az események forrásaként válassza a Hozzáadás gombot](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Adjon meg egy értéket az **eseményforrás neve** számára, amely egyedi a Time Series Insights környezetben, például: **Event-stream**.

1. A **forrás**mezőben válassza az **Event hub**elemet.

1. Válassza ki a megfelelő értékeket az **importálási beállításhoz**:

   * Ha az egyik előfizetésében van egy meglévő Event hub, válassza az **Event hub használata az elérhető előfizetések közül**lehetőséget. Ez a lehetőség a legegyszerűbb megközelítés.

     [@no__t – 1. eseményforrás-importálási beállítás](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  A következő táblázat az **Event hub elérhető előfizetések** lehetőségből való használatának kötelező tulajdonságait ismerteti:

       [@no__t – a 1Subscription és az Event hub részletei](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés | Az előfizetés, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Event Hubs-névtér | Az Event hub-névtér a kívánt Event hub-példányhoz tartozik. |
       | Eseményközpont neve | A kívánt Event hub-példány neve. |
       | Event hub-házirend értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott elérési szabályzatot az Event hub configure (esemény központ **konfigurálása** ) lapon lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési házirendjének **olvasási** engedélyekkel *kell* rendelkezniük. |
       | Event hub-házirend kulcsa | Előre kitöltve a kiválasztott Event hub-házirend értékéről. |

    * Ha az Event hub kívül esik az előfizetéseken, vagy ha a speciális beállításokat szeretné kiválasztani, válassza az **Event hub-beállítások manuális megadása**lehetőséget.

       Az alábbi táblázat ismerteti az **Event hub-beállítások manuális** megadása beállítás szükséges tulajdonságait:
 
       | Tulajdonság | Leírás |
       | --- | --- |
       | Subscription ID (Előfizetés azonosítója) | Az előfizetés, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Erőforráscsoport | Az az erőforráscsoport, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Event Hubs-névtér | Az Event hub-névtér a kívánt Event hub-példányhoz tartozik. |
       | Eseményközpont neve | A kívánt Event hub-példány neve. |
       | Event hub-házirend értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott elérési szabályzatot az Event hub configure (esemény központ **konfigurálása** ) lapon lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. Az eseményforrás megosztott hozzáférési házirendjének **olvasási** engedélyekkel *kell* rendelkezniük. |
       | Event hub-házirend kulcsa | A Service Bus névtér hozzáférésének hitelesítéséhez használt megosztott elérési kulcs. Itt adhatja meg az elsődleges vagy a másodlagos kulcsot. |

    * Mindkét beállítás a következő konfigurációs beállításokat használja:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Event hub fogyasztói csoport | Az Event hub eseményeinek olvasására szolgáló fogyasztói csoport. Javasoljuk, hogy használjon egy dedikált fogyasztói csoportot az eseményforrás számára. |
       | Esemény szerializálási formátuma | Jelenleg a JSON az egyetlen elérhető szerializálási formátum. Az esemény üzeneteinek ebben a formátumban kell lenniük, vagy nem olvashatók be az adatforrások. |
       | Időbélyeg-tulajdonság neve | Az érték meghatározásához meg kell ismernie az Event hub számára küldött üzenet-adatformátumot. Ez az érték annak az üzenetnek a **neve** , amelyet az esemény időbélyegzőként használni kíván. Az érték megkülönbözteti a kis-és nagybetűket. Ha üresen hagyja, a rendszer az eseményforrás **sorba helyezni időt** használja az esemény időbélyegzője. |

1. Adja hozzá az Event hub-hoz hozzáadott dedikált Time Series Insights fogyasztói csoport nevét.

1. Kattintson a **Létrehozás** gombra.

   Az eseményforrás létrehozása után Time Series Insights automatikusan megkezdi az adatátvitelt a környezetbe.

## <a name="next-steps"></a>Következő lépések

* [Adathozzáférési szabályzatok definiálása](time-series-insights-data-access.md) az adatvédelme érdekében.

* [Események küldése](time-series-insights-send-events.md) az esemény forrásának.

* A környezet elérése a [Time Series Insights Explorerben](https://insights.timeseries.azure.com).
