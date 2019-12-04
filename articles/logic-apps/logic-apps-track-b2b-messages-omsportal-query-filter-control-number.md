---
title: Nyomkövetési lekérdezések létrehozása B2B-üzenetekhez
description: AS2-, X12-és EDIFACT-üzeneteket nyomon követő lekérdezések létrehozása az Azure Log Analytics for Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 36cf45aa0f7d46b62caa586d1939ec52e67b1a3e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792861"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Nyomkövetési lekérdezések létrehozása a B2B-üzenetekhez Azure Monitor naplókban Azure Logic Apps

Az [Azure monitor-naplókkal](../log-analytics/log-analytics-overview.md)nyomon követett AS2-, X12-vagy EDIFACT-üzenetek kereséséhez létrehozhat olyan lekérdezéseket, amelyek adott feltételek alapján szűrik a műveleteket. Például megkeresheti az üzeneteket egy adott adatcsere-vezérlőelem száma alapján.

> [!NOTE]
> Ez az oldal korábban ismertette a feladatok végrehajtásának lépéseit a Microsoft Operations Management Suite (OMS) használatával, amely a [2019 januári](../azure-monitor/platform/oms-portal-transition.md)kivonása esetén a lépéseket az Azure log Analytics helyett felváltja. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

* Diagnosztikai naplózással beállított logikai alkalmazás. Megtudhatja, [hogyan hozhat létre logikai alkalmazást](quickstart-create-first-logic-app-workflow.md) , és [hogyan állíthatja be a logikai alkalmazás naplózását](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Figyeléssel és naplózással beállított integrációs fiók. Ismerje meg, [hogyan hozhat létre integrációs fiókot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , és [hogyan állíthatja be a fiók figyelését és naplózását](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette meg, [tegye közzé a diagnosztikai információkat Azure monitor naplókat](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) , és [állítsa be az üzenetek nyomon követését Azure monitor naplókba](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Lekérdezések létrehozása szűrőkkel

Ha bizonyos tulajdonságok vagy értékek alapján szeretne üzeneteket keresni, létrehozhat szűrőket használó lekérdezéseket. 

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe keresse meg a "log Analytics" kifejezést, és válassza a **log Analytics**lehetőséget.

   ![Log Analytics kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. A **log Analytics**alatt keresse meg és válassza ki log Analytics munkaterületét. 

   ![Log Analytics munkaterület kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. A munkaterület menü **általános**területén válassza a **naplók (klasszikus)** vagy a **naplók**lehetőséget. 

   Ez a példa bemutatja, hogyan használható a klasszikus naplók nézet. 
   Ha a **naplók megtekintése** lehetőségre kattint a **teljes log Analytics felület maximalizálása** szakaszban, a **naplók keresése és elemzése**területen a naplók **(klasszikus nézet)** olvashatók be. 

   ![Klasszikus naplók megtekintése](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. A lekérdezés szerkesztése mezőben kezdje el beírni a keresendő mezőnevet. A gépelés megkezdése után a lekérdezés-szerkesztő megjeleníti a lehetséges egyezéseket és a használható műveleteket. A lekérdezés létrehozása után válassza a **Futtatás** lehetőséget, vagy nyomja le az ENTER billentyűt.

   Ez a példa a **LogicAppB2B**való egyezéseket keres. 
   További információ az [Azure monitor-naplókban található adatkeresésről](../log-analytics/log-analytics-log-searches.md).

   ![A lekérdezési karakterlánc begépelésének megkezdése](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. A megtekinteni kívánt időkeret módosításához a bal oldali ablaktáblán válasszon az időtartam listából, vagy húzza a csúszkát. 

   ![Időkeret módosítása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Ha szűrőt szeretne hozzáadni a lekérdezéshez, válassza a **Hozzáadás**lehetőséget. 

   ![Szűrő hozzáadása a lekérdezéshez](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. A **szűrők hozzáadása**területen adja meg a keresett szűrő nevét. Ha megtalálta a szűrőt, válassza ki ezt a szűrőt. A bal oldali panelen válassza a **Hozzáadás** újra lehetőséget.

   Például itt egy másik lekérdezés, amely a **type = = "AzureDiagnostics"** eseményre keres, és a **event_record_messageProperties_interchangeControlNumber_s** szűrő kiválasztásával megkeresi az eredményeket a csomópont-vezérlőelem száma alapján.

   ![Szűrő értékének kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Miután kiválasztotta a **Hozzáadás**lehetőséget, a lekérdezés frissül a kiválasztott szűrési eseménnyel és értékkel. 
   Az előző eredmények szűrése már megtörtént. 

   A lekérdezés például megkeresi a **type = = "AzureDiagnostics" típust** , és a **event_record_messageProperties_interchangeControlNumber_s** szűrő használatával megkeresi az eredményeket egy adatcsere-vezérlőelem száma alapján.

   ![Szűrt eredmények](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Lekérdezés mentése

Ha a lekérdezést a **naplók (klasszikus)** nézetben szeretné menteni, kövesse az alábbi lépéseket:

1. A lekérdezésben a **naplók (klasszikus)** lapon válassza az **elemzés**lehetőséget. 

   ![Az "elemzés" kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. A lekérdezés eszköztárán válassza a **Mentés**lehetőséget.

   ![A „Mentés” elem kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Adja meg a lekérdezés részleteit, például adja meg a lekérdezés nevét, válassza a **lekérdezés**lehetőséget, és adja meg a kategória nevét. Ha elkészült, kattintson a **Mentés** gombra.

   ![A „Mentés” elem kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. A mentett lekérdezések megtekintéséhez lépjen vissza a lekérdezés oldalra. A lekérdezés eszköztáron válassza a **mentett keresések**lehetőséget.

   ![Válassza a "mentett keresések" lehetőséget.](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. A **mentett keresések**területen válassza ki a lekérdezést, így megtekintheti az eredményeket. 

   ![A lekérdezés kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Ha frissíteni szeretné a lekérdezést, hogy különböző eredményeket találjon, szerkessze a lekérdezést.

## <a name="find-and-run-saved-queries"></a>Mentett lekérdezések keresése és futtatása

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe keresse meg a "log Analytics" kifejezést, és válassza a **log Analytics**lehetőséget.

   ![Log Analytics kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. A **log Analytics**alatt keresse meg és válassza ki log Analytics munkaterületét. 

   ![Log Analytics munkaterület kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. A munkaterület menü **általános**területén válassza a **naplók (klasszikus)** vagy a **naplók**lehetőséget. 

   Ez a példa bemutatja, hogyan használható a klasszikus naplók nézet. 

1. A lekérdezési oldal megnyitása után a lekérdezés eszköztáron válassza a **mentett keresések**lehetőséget.

   ![Válassza a "mentett keresések" lehetőséget.](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. A **mentett keresések**területen válassza ki a lekérdezést, így megtekintheti az eredményeket. 

   ![A lekérdezés kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   A lekérdezés automatikusan fut, de ha a lekérdezés semmilyen okból nem fut, a lekérdezés-szerkesztőben válassza a **Futtatás**lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési sémák](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)