---
title: Az Azure Monitor naplóira – Azure Logic Apps B2B-üzenetek monitorozása |} A Microsoft Docs
description: Figyelheti az AS2, X 12 és EDIFACT-üzenetek az integrációs fiókok és az Azure Logic Apps, és állítsa be a diagnosztikai naplózás az Azure Monitor naplóira
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195166"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Az Azure Monitor-naplók az Azure Logic Apps B2B-üzenetek monitorozása

Telepítése után B2B kommunikációt az integrációs fiókban lévő kereskedelmi partnerek között, ezek a partnerek is alkalmazásközi egymással. Ellenőrizze, hogy ezt a kommunikációt a várt módon működik, AS2, X12, figyelheti és EDIFACT üzeneteket, és állítsa be a diagnosztikai naplózás az integrációs fiók a [naplózza az Azure Monitor](../log-analytics/log-analytics-overview.md). Ez a szolgáltatás figyeli a felhőbeli és helyszíni környezeteket, Súgó, a rendelkezésre állás és teljesítmény fenntartására és futásidejű részleteit, illetve gazdagabb hibakeresési eseményeket gyűjti. Más szolgáltatásokkal, például az Azure Storage és az Azure Event Hubs is használhatja ezeket az adatokat.

> [!NOTE]
> Ezen a lapon lehet, hogy továbbra is hivatkoznak, a Microsoft Operations Management Suite (OMS), amely [kivonása a január 2019](../azure-monitor/platform/oms-portal-transition.md), de váltja fel ezeket a lépéseket az Azure Log Analytics, ahol csak lehetséges. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Egy logikai alkalmazást, amely a diagnosztikai naplózás be van állítva. Ismerje meg, [Logic Apps-alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md) és [a logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Miután megfelel a fenti követelményeknek, Log Analytics-munkaterület, amelyek figyelését és követését a B2B-kommunikáció révén az Azure Monitor naplóira használhatók is szükséges. Ha nem rendelkezik a Log Analytics-munkaterületet, további [Log Analytics-munkaterület létrehozása](../azure-monitor/learn/quick-create-workspace.md).

* A logikai alkalmazáshoz kapcsolódó integrációs fiókot. Ismerje meg, [egy integrációs fiók létrehozása a logikai alkalmazás mutató hivatkozást tartalmazó](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Diagnosztikai naplózás bekapcsolása

Közvetlenül az integrációs fiókból vagy-naplózás bekapcsolása vagy [az Azure Monitor szolgáltatáson keresztül](#azure-monitor-service). Az Azure Monitor az infrastruktúra-szintű adatok egyszerű figyelését teszi lehetővé. Tudjon meg többet [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Kapcsolja be a naplózást az integrációs fiók

1. Az a [az Azure portal](https://portal.azure.com), keresse meg és válassza ki az integrációs fiókot. A **figyelés**válassza **diagnosztikai beállítások**.

   ![Keresse meg és válassza ki az integrációs fiók, jelölje be a "Diagnosztikai beállítások"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Most már keresse meg és válassza ki az integrációs fiókot. A szűrő-listák válassza ki az értékeket, amelyeket a alkalmazni az integrációs fiókba.
Ha elkészült, válassza ki a **diagnosztikai beállítás hozzáadása**.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Az integrációs fiók társított Azure-előfizetés | 
   | **Erőforráscsoport** | <*Azure-resource-group-name*> | Az Azure-erőforráscsoportot az integrációs fiók | 
   | **Erőforrás típusa** | **Integrációs fiókok** | Az Azure-beli erőforráshoz, ahol szeretné kapcsolni a naplózást a típus | 
   | **Erőforrás** | <*integration-account-name*> | Hol szeretné kapcsolni a naplózást az Azure-erőforrás neve | 
   ||||  

   Példa:

   ![Az integrációs fiók diagnosztika beállítása](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Adjon meg egy nevet az új diagnosztikai beállítás, és válassza ki a Log Analytics-munkaterületre és a naplózni kívánt adatokat.

   1. Válassza ki **Küldés a Log Analyticsnek**. 

   1. A **Log Analytics**válassza **konfigurálása**. 

   1. A **OMS-munkaterületek**, válassza ki a használandó naplózási Log Analytics-munkaterületen. 

      > [!NOTE]
      > OMS-munkaterületet a Log Analytics-munkaterületek cserélik. 

   1. Alatt **Log**, jelölje be a **IntegrationAccountTrackingEvents** kategória, és válassza a **mentése**.

   Példa: 

   ![Állítsa be a Azure Monitor naplóira, így diagnosztikai adatokat tudjon továbbítani a napló](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Most már [állítsa be az Azure Monitor naplóira a B2B-üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Kapcsolja be a naplózást az Azure monitoron keresztül

1. Az a [az Azure portal](https://portal.azure.com), az Azure fő menüjéből válassza **figyelő**. A **beállítások**válassza **diagnosztikai beállítások**. 

   ![Válassza ki a "Figyelés" > "Diagnosztikai beállítások" > az integrációs fiók](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Most már keresse meg és válassza ki az integrációs fiókot. A szűrő-listák válassza ki az értékeket, amelyeket a alkalmazni az integrációs fiókba.
Ha elkészült, válassza ki a **diagnosztikai beállítás hozzáadása**.

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Az integrációs fiók társított Azure-előfizetés | 
   | **Erőforráscsoport** | <*Azure-resource-group-name*> | Az Azure-erőforráscsoportot az integrációs fiók | 
   | **Erőforrás típusa** | **Integrációs fiókok** | Az Azure-beli erőforráshoz, ahol szeretné kapcsolni a naplózást a típus | 
   | **Erőforrás** | <*integration-account-name*> | Hol szeretné kapcsolni a naplózást az Azure-erőforrás neve | 
   ||||  

   Példa:

   ![Az integrációs fiók diagnosztika beállítása](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Adjon meg egy nevet az új diagnosztikai beállítás, és válassza ki a Log Analytics-munkaterületre és a naplózni kívánt adatokat.

   1. Válassza ki **Küldés a Log Analyticsnek**. 

   1. A **Log Analytics**válassza **konfigurálása**. 

   1. A **OMS-munkaterületek**, válassza ki a használandó naplózási Log Analytics-munkaterületen. 

      > [!NOTE]
      > OMS-munkaterületet a Log Analytics-munkaterületek cserélik. 

   1. Alatt **Log**, jelölje be a **IntegrationAccountTrackingEvents** kategória, és válassza a **mentése**.

   Példa: 

   ![Állítsa be a Azure Monitor naplóira, így diagnosztikai adatokat tudjon továbbítani a napló](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Most már [állítsa be az Azure Monitor naplóira a B2B-üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Diagnosztikai adatok felhasználhatók a más szolgáltatásokkal

Az Azure Monitor naplóira, valamint bővítheti, hogyan használhatja a logikai alkalmazás diagnosztikai adatok Azure-szolgáltatásokat, például: 

* [Azure Diagnostics-naplók az Azure Storage-archívum](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream Azure-beli diagnosztikai naplók az Azure Event hubs szolgáltatásba](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Ezután get valós idejű figyelést végezni a telemetriai adatokat és elemzéseket tesz további szolgáltatásokkal, például [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) és [Power BI](../azure-monitor/platform/powerbi.md). Példa:

* [Stream-adatok az Event Hubsból a Stream Analytics szolgáltatáshoz](../stream-analytics/stream-analytics-define-inputs.md)
* [A Stream Analytics streamelési adatok elemzéséhez, és a egy valós idejű elemzési irányítópult létrehozása a Power bi-ban](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Alapján a kívánt készletet be, ügyeljen arra, hogy Ön első [hozzon létre egy Azure storage-fiók](../storage/common/storage-create-storage-account.md) vagy [az Azure event hub létrehozása](../event-hubs/event-hubs-create.md). Ezután válassza ki a helyre, ahová a diagnosztikai adatok küldése.
Hosszabb megőrzési időszakok a alkalmazni: csak akkor, ha úgy dönt, hogy a storage-fiókot.

![Adatok küldése az Azure storage-fiók vagy eseményközpont](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Támogatott sémák nyomon követése

Az Azure támogatja ezeket a schematypes, amelyek kijavítása kivéve az egyéni típus sémák nyomon követése.

* [AS2-követési séma](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési séma](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>További lépések

* [Az Azure Monitor naplóira B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "követése B2B-üzeneteken alapuló az Azure Monitor naplóira")
* [További információ az Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "megismerheti a vállalati integrációs csomag")

