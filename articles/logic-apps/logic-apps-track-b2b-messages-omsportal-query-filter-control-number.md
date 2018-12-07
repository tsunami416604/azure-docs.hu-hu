---
title: A Log Analytics – Azure Logic Apps B2B-üzenetek nyomon követése lekérdezések létrehozása |} A Microsoft Docs
description: Létrehozhat olyan lekérdezéseket, AS2, X 12 és EDIFACT-üzeneteket az Azure Log Analytics az Azure Logic Apps nyomon követése
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 5cfab07e19e543b7a46fcce8f449a46395c144d6
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995318"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-log-analytics-for-azure-logic-apps"></a>B2B-üzenetek nyomon követése lekérdezések létrehozása az Azure Log Analytics az Azure Logic Apps

AS2 megkereséséhez X12 vagy EDIFACT üzeneteket, hogy követi nyomon a [Azure Log Analytics](../log-analytics/log-analytics-overview.md), meghatározott feltétel alapján műveleteket szűrő lekérdezéseket hozhat létre. Például egy adott adatcsere ellenőrzőszáma üzeneteket is megtalálhatja.

> [!NOTE]
> Ezen a lapon a fentiekben már említettük, az a Microsoft Operations Management Suite (OMS), amely a feladatok végrehajtásához szükséges lépéseket [kivonása a január 2019](../azure-monitor/platform/oms-portal-transition.md), hanem az Azure Log Analytics váltja fel ezeket a lépéseket. 

## <a name="prerequisites"></a>Előfeltételek

* Egy logikai alkalmazást, amely a diagnosztikai naplózás be van állítva. Ismerje meg, [Logic Apps-alkalmazás létrehozása](quickstart-create-first-logic-app-workflow.md) és [a logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Egy integrációs fiók, amely a figyelés és naplózás be van állítva. Ismerje meg, [egy integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [monitorozási és naplózási fiók beállítása](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette, [diagnosztikai adatok közzététele a Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) és [-üzenet követési a Log Analytics beállítása](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Lekérdezések létrehozása szűrőkkel

Egyes tulajdonságok és értékek alapján üzenetek megkereséséhez szűrőket használó lekérdezéseket is létrehozhat. 

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe, keresse meg a "log analytics", és válassza ki **Log Analytics**.

   ![Válassza ki a Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. A munkaterület menüjében alatt **általános**, ezek közül bármelyikre **naplók (klasszikus)** vagy **naplók**. 

   Ez a példa bemutatja, hogyan használható a klasszikus naplók megtekintése. 
   Ha úgy dönt, **megtekinthetők a naplófájlok** a a **maximalizálhatja a Log Analytics felhasználói élmény** szakasz **keresési és -naplók elemzése**, kap a **naplók (klasszikus nézetben)** . 

   ![Klasszikus naplók megtekintése](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. A lekérdezésben szerkesztheti a mezőbe, és kezdje el begépelni a keresni kívánt mező nevét. Amikor elkezdi beírni, a Lekérdezésszerkesztő jeleníti meg, a lehetséges egyezések és a műveletek is használhatja. Miután létrehozta a lekérdezést, válassza ki a **futtatása** vagy nyomja le az Enter billentyűt.

   Ebben a példában a keres egyezések **LogicAppB2B**. 
   Tudjon meg többet [adatok megkeresése a Log Analytics](../log-analytics/log-analytics-log-searches.md).

   ![Kezdje el begépelni a lekérdezési karakterlánc](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Meg szeretné tekinteni, a bal oldali ablaktáblán, az időkeret módosításához válassza ki a időtartama listából, vagy húzza a csúszkát. 

   ![Változás időkeretre](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. A lekérdezés ad hozzá egy szűrőt, válassza a **Hozzáadás**. 

   ![Lekérdezési szűrő hozzáadása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. A **szűrők hozzáadása**, adja meg a keresett szűrő nevét. Ha a szűrőt, válassza ki a szűrőnek. A bal oldali panelen válassza ki a **Hozzáadás** újra.

   Itt például van egy másik lekérdezést, a keresési **típus == "AzureDiagnostics"** események és talál eredményeket az adatcsere-ellenőrzőszám kiválasztásával a **event_record_messageProperties_ interchangeControlNumber_s** szűrőt.

   ![Válassza ki a szűrő értéke](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Miután kiválasztotta **Hozzáadás**, a lekérdezés frissül a kijelölt szűrő esemény és az értéket. 
   Az előző eredmények most túl vannak szűrve. 

   Például ez a lekérdezés keres **típus == "AzureDiagnostics"** eredmények használatával egy adatcsere ellenőrzőszáma alapján megkeresi a **event_record_messageProperties_interchangeControlNumber_s**szűrőt.

   ![Szűrt eredmények](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Lekérdezés mentése

A lekérdezés mentéséhez **naplók (klasszikus)** megtekintheti, kövesse az alábbi lépéseket:

1. A lekérdezés a a **naplók (klasszikus)** lapon a **Analytics**. 

   ![Válassza a "Analytics"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. A lekérdezés eszköztárán válassza **mentése**.

   ![A „Mentés” elem kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Például adja meg a lekérdezés adatait, adja meg a lekérdezés nevét, válassza ki **lekérdezés**, és adja meg a kategória nevét. Ha elkészült, kattintson a **Mentés** gombra.

   ![A „Mentés” elem kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Mentett lekérdezések megtekintéséhez lépjen vissza a lekérdezés lapon. A lekérdezés eszköztárán válassza **mentett keresések**.

   ![Válassza a "Mentett keresések"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. A **mentett keresések**, válassza ki a lekérdezést, így megtekintheti az eredményeket. 

   ![Válassza ki a lekérdezést](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Frissítse a lekérdezést, hogy a különböző eredményeket talál, módosítsa a lekérdezést.

## <a name="find-and-run-saved-queries"></a>Keresse meg és futtassa a mentett lekérdezések

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe, keresse meg a "log analytics", és válassza ki **Log Analytics**.

   ![Válassza ki a Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. A munkaterület menüjében alatt **általános**, ezek közül bármelyikre **naplók (klasszikus)** vagy **naplók**. 

   Ez a példa bemutatja, hogyan használható a klasszikus naplók megtekintése. 

1. Miután megnyílik a lekérdezés lapon, a lekérdezés eszköztárán válassza a **mentett keresések**.

   ![Válassza a "Mentett keresések"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. A **mentett keresések**, válassza ki a lekérdezést, így megtekintheti az eredményeket. 

   ![Válassza ki a lekérdezést](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   A lekérdezés fut automatikusan, de ha a lekérdezés nem fut a bármilyen okból, a lekérdezés-szerkesztőben válassza **futtatása**.

## <a name="next-steps"></a>További lépések

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)