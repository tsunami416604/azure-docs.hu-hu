---
title: Event Hubs eseményforrás hozzáadása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá Azure Event Hubs eseményforrás a Time Series Insights-környezethez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 421a4635a80c5a7a45fb14bf900c205a06789279
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012623"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Event hub-eseményforrás hozzáadása a Time Series Insights-környezethez

Ez a cikk ismerteti, amely beolvassa az adatokat az Azure Event Hubsból az Azure Time Series Insights-környezethez eseményforrás hozzáadása az Azure portal használatával.

> [!NOTE]
> A cikkben ismertetett lépések a Time Series Insights GA és a Time Series Insights előnézeti környezetekre egyaránt érvényesek.

## <a name="prerequisites"></a>Előfeltételek

- Hozzon létre egy Time Series Insights környezetet a [Azure Time Series Insights-környezet létrehozása](./time-series-insights-update-create-environment.md)című témakörben leírtak szerint.
- Eseményközpont létrehozása. Lásd: [Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](../event-hubs/event-hubs-create.md).
- Az event hubs neki küldött aktív üzenetek esemény kell rendelkeznie. Ismerje meg, hogyan [küldhet eseményeket az Azure Event Hubs a .NET-keretrendszer használatával](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Hozzon létre dedikált fogyasztói csoportot, amelyet a Time Series Insights-környezet a felhasználhat az eseményközpontban. Minden egyes Time Series Insights-eseményforrás rendelkeznie kell a saját dedikált fogyasztói csoportot, amely bármely más fogyasztók nincs megosztva. Ha több olvasók ugyanabban a fogyasztói csoportban lévő események felhasználásához, minden olvasók valószínűleg hibák. Az event hubs / 20 felhasználói csoportot korlátozva van. További információkért lásd: a [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Az eseményközpont fogyasztói csoport hozzáadása

Alkalmazások fogyasztói csoportok segítségével az Azure Event Hubsból a lekéréses az adatokat. Az Event hub adatainak megbízható beolvasásához adjon meg egy dedikált fogyasztói csoportot, amelyet csak ez a Time Series Insights-környezet használ.

Az event hubs egy új felhasználói csoport hozzáadása:

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az Event hub-t az Event hub névterében.

    [![az Event hub-névtér megnyitása](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Alatt **entitások**válassza **fogyasztói csoportok**, majd válassza ki **fogyasztói csoportot**.

   [![Event hub – fogyasztói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Az a **fogyasztói csoportok** területén adjon meg egy új egyedi értéket a **neve**.  Ezt a nevet használja, a Time Series Insights-környezet létrehozásakor egy új esemény forrását.

1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-new-event-source"></a>Adjon hozzá egy új esemény forrása

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetbe. A bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki a Time Series Insights-környezet.

1. Alatt **környezeti topológia**válassza **eseményforrások**, majd válassza ki **Hozzáadás**.

   [![az eseményforrás területen válassza a Hozzáadás gombot](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Adjon meg egy értéket a **eseményforrás nevének** egyedi a Time Series Insights-környezethez, ez például **esemény-adatfolyam**.

1. A **forrás**válassza **Eseményközpont**.

1. Válassza ki a megfelelő értékeket a **importálási beállítás**:

   * Ha rendelkezik egy meglévő eseményközpontban az előfizetések, válassza ki a **Eseményközpontot használja a rendelkezésre álló előfizetések**. Ez a lehetőség akkor a legegyszerűbb megközelítés.

     [![válasszon egy eseményforrás-importálási lehetőséget](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  A következő táblázat ismerteti a szükséges tulajdonságokat a **Eseményközpontot használja a rendelkezésre álló előfizetések** lehetőséget:

       [az előfizetés és az Event hub ![részletei](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetést | Az előfizetés, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Event Hubs-névtér | Az Event hub-névtér a kívánt Event hub-példányhoz tartozik. |
       | Eseményközpont neve | A kívánt Event hub-példány neve. |
       | Event hub-házirend értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott elérési szabályzatot az Event hub configure (esemény központ **konfigurálása** ) lapon lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **olvasási** engedélyeket. |
       | Eseményközpont házirendjének kulcsa | Előre kitöltve a kiválasztott Event hub-házirend értékéről. |

    * Ha az event hubs külső az előfizetésekhez, vagy ha azt szeretné, válassza ki a speciális beállításokat, jelölje be **adja meg az Eseményközpont-beállítások manuális**.

       A következő táblázat ismerteti a szükséges tulajdonságokat a **adja meg az Eseményközpont-beállítások manuális** lehetőséget:
 
       | Tulajdonság | Leírás |
       | --- | --- |
       | Előfizetés azonosítója | Az előfizetés, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Erőforráscsoport | Az az erőforráscsoport, amelyhez a kívánt Event hub-példány és névtér tartozik. |
       | Event Hubs-névtér | Az Event hub-névtér a kívánt Event hub-példányhoz tartozik. |
       | Eseményközpont neve | A kívánt Event hub-példány neve. |
       | Event hub-házirend értéke | Válassza ki a kívánt megosztott hozzáférési szabályzatot. A megosztott elérési szabályzatot az Event hub configure (esemény központ **konfigurálása** ) lapon lehet létrehozni. Minden megosztott hozzáférési házirend rendelkezik egy névvel, a beállított engedélyekkel és a hozzáférési kulcsokkal. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **olvasási** engedélyeket. |
       | Eseményközpont házirendjének kulcsa | A közös hozzáférési kulcs, amellyel hitelesíti a hozzáférést a Service Bus-névteret. Itt adhatja meg az elsődleges vagy másodlagos kulcsot. |

    * Mindkét beállítás a következő konfigurációs beállításokat használja:

       | Tulajdonság | Leírás |
       | --- | --- |
       | Event hub fogyasztói csoport | A fogyasztói csoportot, amely eseményeket olvas az eseményközpontból. Kifejezetten ajánljuk, hogy használjon dedikált fogyasztói csoportot az eseményforrást. |
       | Eseményszerializációs formátum | JSON-ban jelenleg csak akkor érhető el szerializálási formátum. Az esemény üzeneteinek ebben a formátumban kell lenniük, vagy nem olvashatók be az adatforrások. |
       | Időbélyegző-tulajdonság neve | Az érték meghatározásához kell megérteni az állapotüzenet-adatokat az eseményközpontnak küldött üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** az a Eseménynapló timestamp forrás szolgál. |

1. A dedikált Time Series Insights fogyasztói csoport neve, amely az eseményközpont adott hozzá.

1. Kattintson a **Létrehozás** gombra.

   Az eseményforrás létrehozása után Time Series Insights automatikusan megkezdi az adatátvitelt a környezetbe.

## <a name="next-steps"></a>Következő lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) védi az adatait.

* [Események küldése](time-series-insights-send-events.md) esemény forrását.

* A környezet eléréséhez a [Time Series Insights explorer](https://insights.timeseries.azure.com).
