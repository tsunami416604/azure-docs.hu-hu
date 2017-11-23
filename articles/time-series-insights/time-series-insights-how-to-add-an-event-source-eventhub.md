---
title: "Az Event Hubs eseményforrást hozzáadása Azure idő adatsorozat Insights |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan adhat egy eseményforrás, amely egy Eseményközpontot, az idő adatsorozat Insights-környezethez csatlakozik."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/21/2017
ms.openlocfilehash: c07c847784eb13c62e350e9c655e027e7df696a3
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Az Event Hubs eseményforrást felvétele idő adatsorozat Insights környezet

Ez a cikk ismerteti az Azure-portál használatával adja hozzá az eseményforrás, amely adatokat olvas az Eseményközpontban lévő idő adatsorozat Insights környezetébe.

## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy idő adatsorozat Insights környezetet. További információkért lásd: [Azure idő adatsorozat Insights környezet létrehozása](time-series-insights-get-started.md) 
- Létrehoz egy Eseményközpontot. Az Event Hubs további információkért lásd: [hozzon létre egy Event Hubs névtér és egy eseményközpontot, az Azure portál használatával](../event-hubs/event-hubs-create.md)
- Az Event Hubs küldési aktív üzenet események rendelkeznie kell. További információkért lásd: [események küldése az Azure Event Hubs használatával a .NET-keretrendszer](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Hozzon létre egy dedikált fogyasztói csoportot az Eseményközpont idő adatsorozat Insight környezetre való felhasználását. Minden alkalommal adatsorozat Insights eseményforrás saját dedikált fogyasztói csoportot, amelyek nincsenek megosztva, más fogyasztóval rendelkeznie kell. Ha több olvasók ugyanazt a felhasználói csoportban lévő események felhasználásához, minden olvasók valószínűleg tekintse meg a hibákat. Vegye figyelembe, hogy nincs-e is maximális hossza 20 felhasználói csoportot az Event Hubs egy. További információkért lásd: a [Event Hubs programozási útmutató](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Új esemény-forrás hozzáadása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő idő adatsorozat Insights környezetben. Kattintson a **összes erőforrás** a menüben, az Azure portál bal oldalán. Válassza ki az Azure Time Series Insights-környezetet.

3. Az a **környezet topológia** elemcsoportban kattintson **eseményforrások**.

   ![Esemény adatforrásokat + Hozzáadás](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Kattintson a **+ Hozzáadás**.

5. Adjon meg egy **Eseményforrás neve** egyedi idő adatsorozat Insights ebben a környezetben, mint **esemény-adatfolyam**.

   ![Töltse ki az első három paramétereket a képernyőn.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Válassza ki a **forrás** , **Eseményközpont**.

7. Válassza ki a megfelelő **beállítás importálása**. 
   - Válasszon **Eseményközpont használja az elérhető előfizetések** Ha már rendelkezik meglévő Eseményközpontban az egyik előfizetése. Ez a legegyszerűbb módszer.
   - Válasszon **adja meg az Event Hubs beállításokat manuálisan** mikor az Event Hubs nem külső az előfizetésekhez, vagy válassza a speciális beállításokat szeretne. 

8. Ha be van jelölve a **Eseményközpont használja az elérhető előfizetések** beállítást, a következő táblázat ismerteti, minden szükséges tulajdonság:

   ![Előfizetés és az Event hub-adatok](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Válassza ki az előfizetést, amelyben az eseményközpont létre lett hozva.
   | Service bus-névtér | Válassza ki a Service Bus-névtér, amely tartalmazza az Eseményközpontba.
   | Eseményközpont neve | Válassza ki az Eseményközpont nevét.
   | Eseményközpont házirend neve | Válassza ki a megosztott elérési házirendet, amely az Event Hub konfigurálása lapon is létrehozható. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **olvasási** engedélyek.
   | Event hub házirend kulcs | Előfordulhat, hogy a kulcs értéke előre kitölti.
   | Event hub fogyasztói csoportot | A rendszer kiolvassa az eseményeket az eseményközpontból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja. |
   | Esemény szerializálási formátum | JSON-ja jelenleg csak a rendelkezésre álló szerializálást. Az eseményüzeneteket a következő formátumban kell lennie, vagy nincsenek adatok olvashatók. |
   | Időbélyeg-tulajdonság neve | Ez az érték határozza meg, az üzenet adataihoz, az Event Hubs küldi üzenetformátuma tisztában kell. Ez az érték a **neve** az üzenet adataihoz, az esemény időbélyegzője használni kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen marad, a **esemény sorba helyezni időtartam** belül az esemény az esemény időbélyegzője forrás használatos. |


9. Ha be van jelölve a **adja meg az Event Hubs-beállítások manuális** beállítást, a következő táblázat ismerteti, minden szükséges tulajdonság:

   | Tulajdonság | Leírás |
   | --- | --- |
   | Előfizetés azonosítója | Az előfizetés, amelyben az eseményközpont létre lett hozva.
   | Erőforráscsoport | Az erőforráscsoport, amelyben az eseményközpont létre lett hozva.
   | Service bus-névtér | A Service Bus-névtér az üzenetküldési entitások készletének tárolója. Egy új Eseményközpont létrehozásakor egy Szolgáltatásbusz-névtér is létrejött.
   | Eseményközpont neve | Az Eseményközpont neve. Az eseményközpont létrehozásakor meg nevet is adott neki.
   | Eseményközpont házirend neve | A megosztott elérési házirendet, amely az Event Hub konfigurálása lapon is létrehozható. Minden megosztott elérési házirend rendelkezik egy nevet, hogy Ön meghatározott engedélyekkel és hozzáférési kulcsokkal. A megosztott hozzáférési házirend a eseményforrás *kell* rendelkezik **olvasási** engedélyek.
   | Event hub házirend kulcs | A Service Bus-névtér való hozzáférés hitelesítéséhez használt megosztott hozzáférési kulcs. Ide írja be az elsődleges vagy másodlagos kulcsot.
   | Event hub fogyasztói csoportot | A rendszer kiolvassa az eseményeket az eseményközpontból fogyasztói csoportot. Ajánlott az eseményforrás dedikált fogyasztói csoportot használja.
   | Esemény szerializálási formátum | JSON-ja jelenleg csak a rendelkezésre álló szerializálást. Az eseményüzeneteket a következő formátumban kell lennie, vagy nincsenek adatok olvashatók. |
   | Időbélyeg-tulajdonság neve | Ez az érték határozza meg, az üzenet adataihoz, az Event Hubs küldi üzenetformátuma tisztában kell. Ez az érték a **neve** az üzenet adataihoz, az esemény időbélyegzője használni kívánt az adott esemény tulajdonság. Az érték a kis-és nagybetűket. Ha üresen marad, a **esemény sorba helyezni időtartam** belül az esemény az esemény időbélyegzője forrás használatos. |


10. Válassza ki **létrehozása** hozzáadása az új esemény forrását.
   
   ![Kattintson a Létrehozás gombra](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Az eseményforrás létrehozása után a Time Series Insights automatikusan megkezdi az adatok streamelését a környezetbe.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Az Event Hubs egy felhasználói csoport hozzáadása
Alkalmazások az fogyasztói csoportok segítségével olvasnak be adatokat az Azure Event Hubs. Adjon meg egy dedikált fogyasztói csoportot idő adatsorozat Insights környezet csak, megbízhatóan adatokat olvasni az Eseményközpont által használható.

Az Event Hubs egy új felhasználói csoport hozzáadásához kövesse az alábbi lépéseket:
1. Keresse meg az Azure-portálon, és nyissa meg az Eseményközpont.

2. Az a **entitások** elemcsoportban válasszon **fogyasztói csoportok**.

   ![Az Event Hubs - egy felhasználói csoport hozzáadása](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Válassza ki **+ fogyasztói csoportot** egy új felhasználói csoport hozzáadása. 

4. Az a **fogyasztói csoportok** lapján adjon meg egy új egyedi **neve**.  Az azonos név használata, amikor új eseményforrás létrehozása a idő adatsorozat Insights környezetben.

5. Válassza ki **létrehozása** létrehozni az új fogyasztói csoportot.

## <a name="next-steps"></a>Következő lépések
- [Adja meg az adat-hozzáférési házirendjeit](time-series-insights-data-access.md) az adatok védelmét.
- [Események küldése](time-series-insights-send-events.md) esemény forrását.
- A környezet az elérni a [idő adatsorozat Insights explorer](https://insights.timeseries.azure.com).
