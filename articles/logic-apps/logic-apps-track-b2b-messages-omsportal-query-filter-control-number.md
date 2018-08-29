---
title: A Log Analytics – Azure Logic Apps B2B-üzenetek lekérdezéséhez |} A Microsoft Docs
description: Létrehozhat olyan lekérdezéseket, AS2, X 12 és EDIFACT-üzenetek a Log Analytics használatával az Azure Logic Apps nyomon követése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: baccd255fc2812eae0de3a98dfcef3dcbc7e1b46
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124270"
---
# <a name="create-queries-for-tracking-as2-x12-and-edifact-messages-in-log-analytics-for-azure-logic-apps"></a>A lekérdezések nyomon követésére AS2, X 12 és EDIFACT-üzenetek a Log Analytics az Azure Logic Apps létrehozása

Az AS2 megkereséséhez X12 vagy EDIFACT üzeneteket, hogy követi nyomon a [Azure Log Analytics](../log-analytics/log-analytics-overview.md), meghatározott feltétel alapján műveleteket szűrő lekérdezéseket hozhat létre. Például egy adott adatcsere ellenőrzőszáma üzeneteket is megtalálhatja.

## <a name="requirements"></a>Követelmények

* Egy logikai alkalmazást, amely a diagnosztikai naplózás be van állítva. Ismerje meg, [Logic Apps-alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) és [a logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Egy integrációs fiók, amely a figyelés és naplózás be van állítva. Ismerje meg, [egy integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [monitorozási és naplózási fiók beállítása](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette, [diagnosztikai adatok közzététele a Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) és [-üzenet követési a Log Analytics beállítása](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Miután teljesítette az előbbi követelményeknek, rendelkeznie kell egy Log Analytics-munkaterületnek. A B2B-kommunikáció a Log Analytics követési használjon ugyanazon a munkaterületen. 
>  
> Ha nem rendelkezik a Log Analytics-munkaterületet, további [Log Analytics-munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>Állapotüzenet-lekérdezések létrehozása a Log Analytics-szűrők

Ez a példa bemutatja, hogyan találhatja meg az adatcsere ellenőrzőszáma üzeneteket.

> [!TIP] 
> Ha ismeri a Log Analytics-munkaterület neve, nyissa meg a munkaterület kezdőlap (`https://{your-workspace-name}.portal.mms.microsoft.com`), és 4. lépés: Indítsa el. Ellenkező esetben kezdje: 1. lépés.

1. Az a [az Azure portal](https://portal.azure.com), válassza a **minden szolgáltatás**. Keresse meg a "log analytics", és válassza a **Log Analytics** itt látható módon:

   ![Keresse meg a Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre.

   ![Válassza ki a Log Analytics-munkaterület](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. A **felügyeleti**, válassza a **naplóbeli keresés**.

   ![Válasszon Lo keresése](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. A keresőmezőbe, írja be a megkereséséhez, majd nyomja meg a kívánt mező **Enter**. Amikor elkezdi beírni, Log Analytics megjeleníti a lehetséges egyezések és használható műveletek. Tudjon meg többet [adatok megkeresése a Log Analytics](../log-analytics/log-analytics-log-searches.md).

   Ebben a példában az eseményeket keres **típus = AzureDiagnostics**.

   ![Kezdje el begépelni a lekérdezési karakterlánc](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. A bal oldali sávon válassza ki a megtekinteni kívánt időkeretet. A lekérdezés ad hozzá egy szűrőt, válassza a **+ Hozzáadás**.

   ![Lekérdezési szűrő hozzáadása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. A **szűrők hozzáadása**, adja meg a szűrő nevét, így megtalálhatja a kívánt szűrőt. Válassza ki a szűrőt, és válassza a **+ Hozzáadás**.

   Keresse meg az adatcsere-ellenőrzőszám, ebben a példában "adatcsere" szóra keres, és kiválasztja **event_record_messageProperties_interchangeControlNumber_s** a szűrőként.

   ![Válassza ki a szűrő](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. A bal oldali sávon válassza ki a szűrő értéket használja, és válassza a kívánt **alkalmaz**.

   Ebben a példában az adatcsere ellenőrzőszáma szeretnénk az üzeneteket választja ki.

   ![Válassza ki a szűrő értéke](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Most már térjen vissza a lekérdezést, amely fejleszt. A lekérdezés a kijelölt szűrő esemény és érték frissítve lett. Az előző eredmények most túl vannak szűrve.

    ![Térjen vissza a lekérdezést a szűrt eredmények](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>A későbbi használat céljából lekérdezés mentése

1. A lekérdezés a a **naplóbeli keresés** lapon a **mentése**. Adja meg a lekérdezés nevét, válasszon egy kategóriát, és válassza a **mentése**.

   ![A lekérdezést adjon meg egy nevet és a kategória](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. A lekérdezés megtekintéséhez válassza **Kedvencek**.

   ![Válassza a "Kedvencek"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. A **mentett keresések**, válassza ki a lekérdezést, hogy az eredmények megtekinthetők. Frissítse a lekérdezést, hogy a különböző eredményeket talál, módosítsa a lekérdezést.

   ![Válassza ki a lekérdezést](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Keresse meg és a mentett lekérdezéseket futtathat a Log Analyticsben

1. Nyissa meg a Log Analytics munkaterület kezdőlapját (`https://{your-workspace-name}.portal.mms.microsoft.com`), és válassza a **naplóbeli keresés**.

   ![A Log Analytics kezdőlapján válassza a "Log Search"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   – vagy –

   ![A menüben válassza a "Log Search"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Az a **naplóbeli keresés** kezdőlapját, válassza a **Kedvencek**.

   ![Válassza a "Kedvencek"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. A **mentett keresések**, válassza ki a lekérdezést, hogy az eredmények megtekinthetők. Frissítse a lekérdezést, hogy a különböző eredményeket talál, módosítsa a lekérdezést.

   ![Válassza ki a lekérdezést](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>További lépések

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)