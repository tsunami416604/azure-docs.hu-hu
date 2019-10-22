---
title: B2B-üzenetek monitorozása Azure Monitor-Azure Logic Apps
description: Diagnosztikai naplózás beállítása AS2-, X12-és EDIFACT-üzenetekhez a Azure Logic Appsban Azure Monitor használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: e5c8e5f3d42f4e85406fcc7dd5a2f6602045c8ed
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680202"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Diagnosztikai naplózás beállítása a Azure Logic Appsban lévő B2B-üzenetekhez Azure Monitor használatával

Miután beállította a VÁLLALATKÖZI kommunikációt a kereskedelmi partnerek között az integrációs fiókban, ezek a partnerek egymással is cserélhetnek üzeneteket. Annak ellenőrzéséhez, hogy ez a kommunikáció a várt módon működik-e, figyelheti az AS2-, X12-és EDIFACT-üzeneteket, és beállíthatja a diagnosztikai naplózást az integrációs fiókhoz [Azure monitor naplók](../log-analytics/log-analytics-overview.md)használatával. Ez a szolgáltatás figyeli a Felhőbeli és a helyszíni környezeteket, segít megőrizni a rendelkezésre állást és a teljesítményt, és gyűjti a futtatókörnyezet részleteit és eseményeit a gazdagabb hibakereséshez. Ezeket az adattárakat más szolgáltatásokkal is használhatja, mint például az Azure Storage és az Azure Event Hubs.

> [!NOTE]
> Előfordulhat, hogy a lap Microsoft Operations Management Suitera (OMS) mutató hivatkozásokat tartalmaz, amelyek kivonása a [2019 januárjában](../azure-monitor/platform/oms-portal-transition.md)történik, de a lépéseket az Azure log Analytics, ahol lehetséges, lecseréli. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Diagnosztikai naplózással beállított logikai alkalmazás. Megtudhatja, [hogyan hozhat létre logikai alkalmazást](quickstart-create-first-logic-app-workflow.md) , és [hogyan állíthatja be a logikai alkalmazás naplózását](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Az előző követelmények teljesítése után Log Analytics munkaterületre is szüksége lesz, amelyet a VÁLLALATKÖZI kommunikáció figyelésére és nyomon követésére használhat Azure Monitor naplókon keresztül. Ha nem rendelkezik Log Analytics munkaterülettel, megtudhatja, [hogyan hozhat létre log Analytics-munkaterületet](../azure-monitor/learn/quick-create-workspace.md).

* Egy integrációs fiók, amely a logikai alkalmazáshoz van társítva. Ismerje meg, [hogyan hozhat létre egy integrációs fiókot a logikai alkalmazásra mutató hivatkozással](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Diagnosztikai naplózás bekapcsolása

A naplózást közvetlenül az integrációs fiókjából vagy [a Azure monitor szolgáltatásból](#azure-monitor-service)is bekapcsolhatja. Azure Monitor az infrastruktúra-szintű adatok alapszintű figyelését teszi lehetővé. További információ a [Azure monitorról](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Naplózás bekapcsolása integrációs fiókból

1. A [Azure Portalban](https://portal.azure.com)keresse meg és válassza ki az integrációs fiókját. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.

   ![Az integrációs fiók megkereséséhez és kiválasztásához válassza a "diagnosztikai beállítások" lehetőséget.](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Most keresse meg és válassza ki az integrációs fiókját. A szűrőlisták területen válassza ki az integrációs fiókra vonatkozó értékeket.
Ha elkészült, válassza a **diagnosztikai beállítás hozzáadása**elemet.

   | Tulajdonság | Value (Díj) | Leírás | 
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Az integrációs fiókjához társított Azure-előfizetés | 
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport neve* > | Az integrációs fiókhoz tartozó Azure-erőforráscsoport | 
   | **Erőforrás típusa** | **Integrációs fiókok** | Az Azure-Erőforrás típusa, amelyen be szeretné kapcsolni a naplózást | 
   | **Erőforrás** | <*integráció – fiók neve* > | Annak az Azure-erőforrásnak a neve, amelyen be szeretné kapcsolni a naplózást | 
   ||||  

   Példa:

   ![Diagnosztika beállítása az integrációs fiókhoz](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Adja meg az új diagnosztikai beállítás nevét, és válassza ki a Log Analytics munkaterületet és a naplózni kívánt adatait.

   1. Válassza **a küldés log Analytics**lehetőséget. 

   1. A **log Analytics**területen válassza a **Konfigurálás**lehetőséget. 

   1. A **OMS-munkaterületek**területen válassza ki a naplózáshoz használni kívánt log Analytics munkaterületet. 

      > [!NOTE]
      > A OMS-munkaterületeket Log Analytics munkaterületek cserélik le. 

   1. A **napló**területen válassza ki a **IntegrationAccountTrackingEvents** kategóriát, majd kattintson a **Mentés**gombra.

   Példa: 

   ![Azure Monitor naplók beállítása, hogy diagnosztikai adatait egy naplóba küldje el](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Most [állítsa be a B2B-üzeneteinek nyomon követését Azure monitor naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>A naplózás bekapcsolása Azure Monitor

1. A [Azure Portal](https://portal.azure.com)az Azure főmenüjében válassza a **figyelő**elemet. A **Beállítások**területen válassza a **diagnosztikai beállítások**elemet. 

   ![Válassza a "Figyelés" > "diagnosztikai beállítások" > az integrációs fiókját](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Most keresse meg és válassza ki az integrációs fiókját. A szűrőlisták területen válassza ki az integrációs fiókra vonatkozó értékeket.
Ha elkészült, válassza a **diagnosztikai beállítás hozzáadása**elemet.

   | Tulajdonság | Value (Díj) | Leírás | 
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés-neve*> | Az integrációs fiókjához társított Azure-előfizetés | 
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport neve* > | Az integrációs fiókhoz tartozó Azure-erőforráscsoport | 
   | **Erőforrás típusa** | **Integrációs fiókok** | Az Azure-Erőforrás típusa, amelyen be szeretné kapcsolni a naplózást | 
   | **Erőforrás** | <*integráció – fiók neve* > | Annak az Azure-erőforrásnak a neve, amelyen be szeretné kapcsolni a naplózást | 
   ||||  

   Példa:

   ![Diagnosztika beállítása az integrációs fiókhoz](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Adja meg az új diagnosztikai beállítás nevét, és válassza ki a Log Analytics munkaterületet és a naplózni kívánt adatait.

   1. Válassza **a küldés log Analytics**lehetőséget. 

   1. A **log Analytics**területen válassza a **Konfigurálás**lehetőséget. 

   1. A **OMS-munkaterületek**területen válassza ki a naplózáshoz használni kívánt log Analytics munkaterületet. 

      > [!NOTE]
      > A OMS-munkaterületeket Log Analytics munkaterületek cserélik le. 

   1. A **napló**területen válassza ki a **IntegrationAccountTrackingEvents** kategóriát, majd kattintson a **Mentés**gombra.

   Példa: 

   ![Azure Monitor naplók beállítása, hogy diagnosztikai adatait egy naplóba küldje el](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Most [állítsa be a B2B-üzeneteinek nyomon követését Azure monitor naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Diagnosztikai adatkezelés más szolgáltatásokkal

A Azure Monitor naplókkal együtt kiterjesztheti a logikai alkalmazás diagnosztikai adatait más Azure-szolgáltatásokkal, például a következő módon: 

* [Azure Diagnostics naplók archiválása az Azure Storage-ban](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Stream Azure Diagnostics naplók az Azure-ba Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

Ezt követően valós idejű monitorozást érhet el a telemetria és az Analytics használatával más szolgáltatásokból, például a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) és az [Power bi](../azure-monitor/platform/powerbi.md). Példa:

* [Stream-adatok Event Hubsról Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Adatfolyamok elemzése Stream Analytics és valós idejű elemzési irányítópult létrehozása Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

A beállítani kívánt beállítások alapján győződjön meg arról, hogy először [létrehoz egy Azure Storage-fiókot](../storage/common/storage-create-storage-account.md) , vagy [létrehoz egy Azure Event hub](../event-hubs/event-hubs-create.md)-t. Ezután kiválaszthatja azokat a célhelyeket, amelyeken diagnosztikai adatküldést szeretne küldeni.
A megőrzési időszakok csak akkor érvényesek, ha a Storage-fiókot választja.

![Adatküldés az Azure Storage-fiókba vagy az Event hub-ba](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Támogatott követési sémák

Az Azure támogatja ezeket a követési sémákat, amelyek mindegyike rögzített sémákkal rendelkezik, kivéve az egyéni típust.

* [AS2-követési séma](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési séma](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* [B2B-üzenetek nyomon követése Azure Monitor-naplókban](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "B2B-üzenetek nyomon követése Azure Monitor-naplókban")
* [További információ a Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Tudnivalók a Enterprise Integration Pack")

