---
title: B2B üzenetek megfigyelése, és állítsa be a naplózás – Azure Logic Apps |} A Microsoft Docs
description: A figyelő AS2, X 12 és EDIFACT-üzenetek. Az integrációs fiók az Azure Logic Apps-alkalmazások diagnosztikai célú naplózásának beállítása.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 07/21/2017
ms.openlocfilehash: decc0271ae0e7c359f72648d7c4d0076892285da
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125175"
---
# <a name="monitor-b2b-messages-and-set-up-logging-for-integration-accounts-in-azure-logic-apps"></a>B2B üzenetek megfigyelése és integrációs fiókok az Azure Logic Apps-naplózás beállítása

Miután beállította a B2B-kommunikáció két közötti üzleti folyamatokkal vagy az integrációs fiók használatával fut ezeket az entitásokat is tudjon cserélni egymással üzeneteket. Ezt a kommunikációt a várt módon működik, AS2, X12, monitorozásának beállításához és EDIFACT-üzenetek, és diagnosztikai célú naplózásának révén az integrációs fiók megerősítéséhez a [Azure Log Analytics](../log-analytics/log-analytics-overview.md) szolgáltatás. Ez a szolgáltatás figyeli a felhőbeli és helyi környezetekben, így a rendelkezésre állás és a teljesítményük fenntartása, és is a modul részleteit, illetve gazdagabb hibakeresési eseményeket gyűjti. Emellett [a diagnosztikai adatok felhasználhatók a más szolgáltatásokkal](#extend-diagnostic-data), mint például az Azure Storage és az Azure Event Hubs.

## <a name="requirements"></a>Követelmények

* Egy logikai alkalmazást, amely a diagnosztikai naplózás be van állítva. Ismerje meg, [a logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Miután teljesítette ezt a követelményt, egy Log Analytics-munkaterületnek kell rendelkeznie. Log Analytics-munkaterületen üzembe helyezésekor meg az integrációs fiók naplózás használjon. Ha nem rendelkezik a Log Analytics-munkaterületet, további [Log Analytics-munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md).

* A logikai alkalmazáshoz kapcsolódó integrációs fiókot. Ismerje meg, [egy integrációs fiók létrehozása a logikai alkalmazás mutató hivatkozást tartalmazó](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Az integrációs fiók naplózás diagnosztika bekapcsolása

Közvetlenül az integrációs fiókból vagy-naplózás bekapcsolása vagy [az Azure Monitor szolgáltatáson keresztül](#azure-monitor-service). Az Azure Monitor az infrastruktúra-szintű adatok egyszerű figyelését teszi lehetővé. Tudjon meg többet [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Az integrációs fiók közvetlenül a naplózás diagnosztika bekapcsolása

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki az integrációs fiókot. A **figyelés**, válassza a **diagnosztikai naplók** itt látható módon:

   ![Keresse meg és válassza ki az integrációs fiókot, válassza a "Diagnosztikai naplók"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Miután kiválasztotta az integrációs fiók, akkor a következő értékek automatikusan ki van jelölve. Ha ezekkel az értékekkel helyesek, válassza a **diagnosztika bekapcsolása**. Ellenkező esetben válassza ki a kívánt értékekre:

   1. A **előfizetés**, válassza ki az Azure-előfizetést, amelyet az integrációs fiókban.
   2. A **erőforráscsoport**, válassza ki az erőforráscsoportot, amelyet az integrációs fiókban.
   3. A **erőforrástípus**válassza **integrációs fiókok**. 
   4. A **erőforrás**, válassza ki az integrációs fiókot. 
   5. Válasszon **diagnosztika bekapcsolása**.

   ![Az integrációs fiók diagnosztika beállítása](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Alatt **diagnosztikai beállítások**, majd **állapota**, válassza a **a**.

   ![Kapcsolja be az Azure Diagnostics](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Most válassza ki a Log Analytics-munkaterületet és az adatokat, a naplózáshoz látható módon:

   1. Válassza ki **Küldés a Log Analyticsnek**. 
   2. A **Log Analytics**, válassza a **konfigurálása**. 
   3. A **OMS-munkaterületek**, válassza ki a naplózás használata a Log Analytics-munkaterületet.
   4. A **Log**, jelölje be a **IntegrationAccountTrackingEvents** kategória.
   5. Válassza a **Mentés** elemet.

   ![Így naplóba elküldheti a diagnosztikai adatok Log Analytics beállítása](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Most már [állítsa be a Log Analytics a B2B-üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Kapcsolja be a diagnosztikai naplózás az Azure monitoron keresztül

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **figyelő**, **diagnosztikai naplók**. Itt látható módon válassza ki az integrációs fiók:

   ![Válassza a "Figyelés", "A diagnosztikai naplók", válassza ki az integrációs fiók](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Miután kiválasztotta az integrációs fiók, akkor a következő értékek automatikusan ki van jelölve. Ha ezekkel az értékekkel helyesek, válassza a **diagnosztika bekapcsolása**. Ellenkező esetben válassza ki a kívánt értékekre:

   1. A **előfizetés**, válassza ki az Azure-előfizetést, amelyet az integrációs fiókban.
   2. A **erőforráscsoport**, válassza ki az erőforráscsoportot, amelyet az integrációs fiókban.
   3. A **erőforrástípus**válassza **integrációs fiókok**.
   4. A **erőforrás**, válassza ki az integrációs fiókot.
   5. Válasszon **diagnosztika bekapcsolása**.

   ![Az integrációs fiók diagnosztika beállítása](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. A **diagnosztikai beállítások**, válassza a **a**.

   ![Kapcsolja be az Azure Diagnostics](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Most válassza ki a Log Analytics munkaterület és az esemény naplózásának kategóriája látható módon:

   1. Válassza ki **Küldés a Log Analyticsnek**. 
   2. A **Log Analytics**, válassza a **konfigurálása**. 
   3. A **OMS-munkaterületek**, válassza ki a naplózás használata a Log Analytics-munkaterületet.
   4. A **Log**, jelölje be a **IntegrationAccountTrackingEvents** kategória.
   5. Ha elkészült, kattintson a **Mentés** gombra.

   ![Így naplóba elküldheti a diagnosztikai adatok Log Analytics beállítása](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Most már [állítsa be a Log Analytics a B2B-üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Hogyan és hol diagnosztikai adatok felhasználhatók más szolgáltatásokkal

Az Azure Log Analytics, valamint bővítheti, hogyan használhatja a logikai alkalmazás diagnosztikai adatok Azure-szolgáltatásokat, például: 

* [Azure Diagnostics-naplók az Azure Storage-archívum](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Stream Azure-beli diagnosztikai naplók az Azure Event hubs szolgáltatásba](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Ezután get valós idejű figyelést végezni a telemetriai adatokat és elemzéseket tesz további szolgáltatásokkal, például [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) és [Power BI](../log-analytics/log-analytics-powerbi.md). Példa:

* [Stream-adatok az Event Hubsból a Stream Analytics szolgáltatáshoz](../stream-analytics/stream-analytics-define-inputs.md)
* [A Stream Analytics streamelési adatok elemzéséhez, és a egy valós idejű elemzési irányítópult létrehozása a Power bi-ban](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Alapján állítsa be a kívánt beállításokat, győződjön meg arról, hogy Ön első [hozzon létre egy Azure storage-fiók](../storage/common/storage-create-storage-account.md) vagy [az Azure event hub létrehozása](../event-hubs/event-hubs-create.md). Ezután válassza ki a beállításokat, amennyiben meg szeretné-e diagnosztikai adatok küldése:

![Adatok küldése az Azure storage-fiók vagy eseményközpont](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Hosszabb megőrzési időszakok a alkalmazni: csak akkor, ha úgy dönt, hogy a storage-fiókot.

## <a name="supported-tracking-schemas"></a>Támogatott sémák nyomon követése

Az Azure támogatja ezeket a schematypes, amelyek kijavítása kivéve az egyéni típus sémák nyomon követése.

* [AS2-követési séma](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési séma](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* [A Log Analytics B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "követése B2B-üzeneteken alapuló az OMS-ben")
* [További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")

