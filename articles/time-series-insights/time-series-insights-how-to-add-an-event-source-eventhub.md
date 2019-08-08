---
title: Az Event Hubs eseményforrás hozzáadása Azure Time Series Insights |} A Microsoft Docs
description: Ez a cikk ismerteti, amely a Time Series Insights-környezethez csatlakozik az Azure Event Hubs eseményforrás hozzáadása.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8403c8fbc4faf35e0ccd3c87347e88a46f0769ff
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854476"
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

1. Az Azure Portalon keresse meg és nyissa meg az eseményközpont.

1. Alatt **entitások**válassza **fogyasztói csoportok**, majd válassza ki **fogyasztói csoportot**.

   [![Event hub – fogyasztói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. Az a **fogyasztói csoportok** területén adjon meg egy új egyedi értéket a **neve**.  Ezt a nevet használja, a Time Series Insights-környezet létrehozásakor egy új esemény forrását.

1. Kattintson a **Létrehozás** gombra.

## <a name="add-a-new-event-source"></a>Adjon hozzá egy új esemény forrása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetbe. A bal oldali menüben válassza ki a **összes erőforrás**, majd válassza ki a Time Series Insights-környezet.

1. Alatt **környezeti topológia**válassza **eseményforrások**, majd válassza ki **Hozzáadás**.

   [![Az eseményforrás területen válassza a Hozzáadás gombot](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Adjon meg egy értéket a **eseményforrás nevének** egyedi a Time Series Insights-környezethez, ez például **esemény-adatfolyam**.

1. A **forrás**válassza **Eseményközpont**.

1. Válassza ki a megfelelő értékeket a **importálási beállítás**:
   - Ha rendelkezik egy meglévő eseményközpontban az előfizetések, válassza ki a **Eseményközpontot használja a rendelkezésre álló előfizetések**. Ez a lehetőség akkor a legegyszerűbb megközelítés.

       [![Az új esemény forrása ablaktáblán adja meg az első három paraméter értékét.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)


       [![Az előfizetés és az Event hub részletei](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

     A következő táblázat ismerteti a szükséges tulajdonságokat a **Eseményközpontot használja a rendelkezésre álló előfizetések** lehetőséget:

     | Tulajdonság | Leírás |
     | --- | --- |
     | Előfizetés azonosítója | Válassza ki az előfizetést, amelyben az eseményközpont létre lett hozva.
     | Service Bus-névtér | Válassza ki az Azure Service Bus-névtér, amely tartalmazza az eseményközpontba.
     | Event Hubs neve | Válassza ki az event hubs nevét.
     | Eseményközpont szabályzatának neve | Válassza ki a megosztott elérési házirendet. A megosztott elérési házirendet is létrehozhat az eseményközpontban **konfigurálása** fülre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **olvasási** engedélyeket.
     | Eseményközpont házirendjének kulcsa | Előfordulhat, hogy előre kitölti a kulcs értékét.
     | Eseményközpontbeli fogyasztói csoport | A fogyasztói csoportot, amely eseményeket olvas az eseményközpontból. Kifejezetten ajánljuk, hogy használjon dedikált fogyasztói csoportot az eseményforrást. |
     | Eseményszerializációs formátum | JSON-ban jelenleg csak akkor érhető el szerializálási formátum. Az esemény üzeneteinek ebben a formátumban kell lenniük, vagy nem olvashatók be az adatforrások. |
     | Időbélyegző-tulajdonság neve | Az érték meghatározásához kell megérteni az állapotüzenet-adatokat az eseményközpontnak küldött üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** az a Eseménynapló timestamp forrás szolgál. |

    - Ha az event hubs külső az előfizetésekhez, vagy ha azt szeretné, válassza ki a speciális beállításokat, jelölje be **adja meg az Eseményközpont-beállítások manuális**.

      A következő táblázat ismerteti a szükséges tulajdonságokat a **adja meg az Eseményközpont-beállítások manuális** lehetőséget:
 
      | Tulajdonság | Leírás |
      | --- | --- |
      | Előfizetés azonosítója | Az előfizetés, amelyben az eseményközpont létre lett hozva.
      | Erőforráscsoport | Az erőforráscsoport, amelyben az eseményközpont létre lett hozva.
      | Service Bus-névtér | Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új eseményközpont létrehozásakor egy Service Bus-névtér is létrejött.
      | Event Hubs neve | Az eseményközpont neve. Az eseményközpont létrehozásakor azt is neki egy egyedi nevet.
      | Eseményközpont szabályzatának neve | A megosztott elérési házirendet. Az eseményközpont megosztott elérési házirendet is létrehozhat **konfigurálása** fülre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **olvasási** engedélyeket.
      | Eseményközpont házirendjének kulcsa | A közös hozzáférési kulcs, amellyel hitelesíti a hozzáférést a Service Bus-névteret. Itt adhatja meg az elsődleges vagy másodlagos kulcsot.
      | Eseményközpontbeli fogyasztói csoport | A fogyasztói csoportot, amely eseményeket olvas az eseményközpontból. Kifejezetten ajánljuk, hogy használjon dedikált fogyasztói csoportot az eseményforrást.
      | Eseményszerializációs formátum | JSON-ban jelenleg csak akkor érhető el szerializálási formátum. Az esemény üzeneteinek ebben a formátumban kell lenniük, vagy nem olvashatók be az adatforrások. |
      | Időbélyegző-tulajdonság neve | Az érték meghatározásához kell megérteni az állapotüzenet-adatokat az eseményközpontnak küldött üzenet formátumát. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** az a Eseménynapló timestamp forrás szolgál. |

1. A dedikált Time Series Insights fogyasztói csoport neve, amely az eseményközpont adott hozzá.

1. Kattintson a **Létrehozás** gombra.

   [![Létrehozás kiválasztása](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   Az eseményforrás létrehozása után a Time Series Insights automatikusan elindul, streamelési adatok a környezetben.

## <a name="next-steps"></a>További lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) védi az adatait.

* [Események küldése](time-series-insights-send-events.md) esemény forrását.

* A környezet eléréséhez a [Time Series Insights explorer](https://insights.timeseries.azure.com).
