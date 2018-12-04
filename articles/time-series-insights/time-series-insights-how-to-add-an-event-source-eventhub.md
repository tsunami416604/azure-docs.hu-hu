---
title: Event Hub-eseményforrás hozzáadása Azure Time Series Insights |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan adhat hozzá egy eseményforrás, amely egy Eseményközpontot, az a Time Series Insights-környezethez csatlakozik.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 93076a8b0d11e05b86c20868d4bc23f4826f107f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832445"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Event Hub-eseményforrás hozzáadása a Time Series Insights-környezet

Ez a cikk ismerteti, amely adatokat olvas egy eseményközpontból a Time Series Insights-környezetbe eseményforrás hozzáadása az Azure portal használatával.

> [!NOTE]
> Ezek az utasítások mindkét Time Series Insights általánosan érvényesek, és a Time Series Insights frissítése privát előzetes verziójú környezetekben.

## <a name="prerequisites"></a>Előfeltételek

- Egy Time Series Insights környezetet hozhat létre. További információkért lásd: [Azure Time Series Insights-környezet létrehozása](./time-series-insights-update-create-environment.md).
- Eseményközpont létrehozása. Az Event Hubs további információkért lásd: [hozzon létre egy Event Hubs-névtér és a egy eseményközpontot, az Azure portal használatával](../event-hubs/event-hubs-create.md).
- Az Event Hubs küld a rendszer aktív üzenetek esemény rendelkeznie kell. További információkért lásd: [események küldése az Azure Event Hubsba a .NET-keretrendszer használatával](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Az Event Hubs a Time Series Insight-környezethez való felhasználásához hozzon létre egy dedikált fogyasztói csoportot. Minden egyes Time Series Insights-eseményforrás rendelkeznie kell a saját dedikált fogyasztói csoportot, amelyek nincsenek megosztva, bármely más fogyasztók számára. Ha több olvasók ugyanabban a fogyasztói csoportban lévő események felhasználásához, minden olvasók valószínűleg hibák. Vegye figyelembe, hogy nincs-e is legfeljebb 20 felhasználói csoportot egy Eseményközpontba. További információkért lásd: a [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Az Eseményközpont fogyasztói csoport hozzáadása

Fogyasztói csoportok adatok kérhetők le az Azure Event Hubs alkalmazások használják. Adja meg a Time Series Insights környezetet csak, megbízhatóan adatokat olvasni az Event Hub által használt, dedikált fogyasztói csoportot.

Az Event Hubs egy új felhasználói csoport hozzáadásához kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg és nyissa meg az Eseményközpont.

1. Alatt a **entitások** szakaszban kattintson **fogyasztói csoportok**.

   ![Event Hub - fogyasztói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

1. Válassza ki **+ fogyasztói csoportot** egy új felhasználói csoport hozzáadása. 

1. Az a **fogyasztói csoportok** területén adjon meg egy új egyedi **neve**.  Használja ezt a nevet, egy új eseményforrás létrehozása a Time Series Insights-környezet esetén.

1. Válassza ki **létrehozás** az új felhasználói csoport létrehozásához.

## <a name="add-a-new-event-source"></a>Adjon hozzá egy új esemény forrása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetbe. Kattintson a **összes erőforrás** az Azure portal bal oldali menüben. Válassza ki az Azure Time Series Insights-környezetet.

1. Alatt a **környezeti topológia** szakaszban kattintson **eseményforrások**.

   ![Esemény adatforrásokat + hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

1. Kattintson a **+Hozzáadás** gombra.

1. Adjon meg egy **eseményforrás nevének** egyedi a Time Series Insights-környezethez, mint például **esemény-adatfolyam**.

   ![Adja meg az első három paramétereket a képernyőn.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

1. Válassza ki a **forrás** , **Eseményközpont**.

1. Válassza ki a megfelelő **importálási beállítás**.
   - Válasszon **Eseményközpontot használja a rendelkezésre álló előfizetések** Ha már rendelkezik egy meglévő Eseményközponton az előfizetések egyike. Ez a legegyszerűbb megközelítés.
   - Válasszon **adja meg az Eseményközpont-beállítások manuális** mikor az Event Hubs külső az előfizetésekhez, vagy adja meg a speciális beállításokat szeretné.

1. Ha be van jelölve a **Eseményközpontot használja a rendelkezésre álló előfizetések** beállítást, az alábbi táblázat azt ismerteti, minden kötelező tulajdonság:

   ![Előfizetés és az Event hub-adatok](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetési azonosító | Válassza ki az előfizetést, amelyben az eseményközpont létre lett hozva.
   | Service Bus-névtér | Válassza ki a Service Bus-névtér, amely tartalmazza az Eseményközpontba.
   | Event Hubs neve | Válassza ki az Event Hubs nevét.
   | Eseményközpont szabályzatának neve | Válassza ki a megosztott elérési házirendet, amely az Event Hub konfigurálása lapon hozható létre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **olvasási** engedélyeket.
   | Eseményközpont házirendjének kulcsa | Előfordulhat, hogy előre kitölti a kulcs értékét.
   | Eseményközpontbeli fogyasztói csoport | A fogyasztói csoportot, olvassa az eseményeket az eseményközpontból. Azt javasoljuk, használjon dedikált fogyasztói csoportot az eseményforrás a. |
   | Eseményszerializációs formátum | JSON-ja jelenleg csak akkor érhető el szerializálásának. Az eseményt üzenetek a következő formátumban kell lennie, vagy az adatok nem olvashatók. |
   | Időbélyegző-tulajdonság neve | Ez az érték határozza meg, az üzenet formátuma az Event Hubs-eseményközpontba küldött üzenet adatok megismerése kell. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** belül az eseményt az esemény időbélyegzője forrás szolgál. |

1. Ha be van jelölve a **adja meg az Eseményközpont-beállítások manuális** beállítást, az alábbi táblázat azt ismerteti, minden kötelező tulajdonság:

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Az előfizetés, amelyben az eseményközpont létre lett hozva.
   | Erőforráscsoport | Az erőforráscsoport, amelyben az eseményközpont létre lett hozva.
   | Service Bus-névtér | Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Service Bus-névtér is létrejött.
   | Event Hubs neve | Az Eseményközpont neve. Az eseményközpont létrehozásakor azt is neki egy egyedi nevet.
   | Eseményközpont szabályzatának neve | A megosztott elérési házirendet, amely az Event Hub konfigurálása lapon hozható létre. Minden megosztott elérési házirend neve, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsok van. A megosztott hozzáférési szabályzatot az eseményforrás *kell* rendelkezik **olvasási** engedélyeket.
   | Eseményközpont házirendjének kulcsa | A közös hozzáférési kulcs segítségével hitelesíti a hozzáférést a Service Bus-névteret. Ide írja be az elsődleges vagy másodlagos kulcsot.
   | Eseményközpontbeli fogyasztói csoport | A fogyasztói csoportot, olvassa az eseményeket az eseményközpontból. Azt javasoljuk, használjon dedikált fogyasztói csoportot az eseményforrás a.
   | Eseményszerializációs formátum | JSON-ja jelenleg csak akkor érhető el szerializálásának. Az eseményt üzenetek a következő formátumban kell lennie, vagy az adatok nem olvashatók. |
   | Időbélyegző-tulajdonság neve | Ez az érték határozza meg, az üzenet formátuma az Event Hubs-eseményközpontba küldött üzenet adatok megismerése kell. Ez az érték a **neve** az állapotüzenet-adatokat használja, mint az esemény időbélyegzője kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen hagyja, a **eseményt sorba idő** belül az eseményt az esemény időbélyegzője forrás szolgál. |

1. A dedikált TSI fogyasztói csoport neve, amely az eseményközpont adott hozzá.

1. Válassza ki **létrehozás** hozzáadni az új esemény forrását.

   ![Kattintson a Létrehozás gombra](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Az eseményforrás létrehozása után a Time Series Insights automatikusan megkezdi az adatok streamelését a környezetbe.

## <a name="next-steps"></a>További lépések

[Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) védi az adatait.

[Események küldése](time-series-insights-send-events.md) esemény forrását.

A környezet eléréséhez a [Time Series Insights explorer](https://insights.timeseries.azure.com).
