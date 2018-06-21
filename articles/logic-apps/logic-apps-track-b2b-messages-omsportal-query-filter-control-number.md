---
title: Log Analytics - Azure Logic Apps B2B üzenetek lekérdezés |} Microsoft Docs
description: A Naplóelemzési követése AS2, X 12 és EDIFACT üzeneteinek lekérdezések létrehozása
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 48cca9919bd09906bdcc3faaaef186ec109c9169
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294309"
---
# <a name="query-for-as2-x12-and-edifact-messages-in-log-analytics"></a>Az AS2, X 12 és EDIFACT üzenetek Naplóelemzési lekérdezés

Az AS2 megkereséséhez X12 vagy EDIFACT-üzenetek, hogy követi nyomon a [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md), a megadott feltételek alapján műveletek szűrő lekérdezéseket hozhat létre. Például egy adott interchange ellenőrző szám alapján is megtalálhatja.

## <a name="requirements"></a>Követelmények

* Egy logikai alkalmazást a diagnosztikai naplózás be van állítva. Ismerje meg, [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) és [adott logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Integráció fiók be van állítva a figyelés és naplózás. Ismerje meg, [integrációs fiók létrehozása](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) és [figyelés és naplózás fiók beállításával](../logic-apps/logic-apps-monitor-b2b-message.md).

* Ha még nem tette, [diagnosztikai adatok közzétételére Naplóelemzési](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) és [állítsa be a Naplóelemzési nyomkövetési üzenet](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

> [!NOTE]
> Miután teljesítette az előző követelményeknek, rendelkeznie kell a Naplóelemzési munkaterület. A Naplóelemzési B2B kommunikáció követési ugyanazon a munkaterületen kell használnia. 
>  
> Ha még nem rendelkezik a Naplóelemzési munkaterület, [a Naplóelemzési munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md).

## <a name="create-message-queries-with-filters-in-log-analytics"></a>A Naplóelemzési szűrőkkel állapotüzenet-lekérdezések létrehozása

Ez a példa bemutatja, hogyan található üzenetek az adatcsere ellenőrző szám alapján.

> [!TIP] 
> Ha ismeri a Naplóelemzési munkaterület nevét, keresse fel a munkaterület kezdőlapját (`https://{your-workspace-name}.portal.mms.microsoft.com`), 4. lépés: Indítsa el. Ellenkező esetben kezdjék 1. lépés.

1. Az a [Azure-portálon](https://portal.azure.com), válassza a **minden szolgáltatás**. Keresse meg a "naplóelemzési", és válassza a **Naplóelemzési** itt látható módon:

   ![A Naplóelemzési keresése](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/browseloganalytics.png)

2. A **Naplóelemzési**, található, és válassza ki a Naplóelemzési munkaterület.

   ![A Naplóelemzési munkaterület kiválasztása](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/selectla.png)

3. A **felügyeleti**, válassza a **naplófájl-keresési**.

   ![Válasszon helye keresése](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/azure-portal-page.png)

4. A keresési mezőbe, írja be egy mező található, és nyomja le az ENTER kívánt **Enter**. Amikor elkezdi beírni, Log Analyticshez megjeleníti a lehetséges találatok és műveletek közül választhat. További információ [adatok megkeresése a Naplóelemzési](../log-analytics/log-analytics-log-searches.md).

   Ez a példa eseményeket keres **típus = AzureDiagnostics**.

   ![Kezdje beírni a lekérdezési karakterlánc](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-start-query.png)

5. A bal oldali sávon válassza ki a megtekinteni kívánt időkeretet. Adjon hozzá egy szűrőt a lekérdezést, válassza a **+ Hozzáadás**.

   ![Szűrő felvétele lekérdezés](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query1.png)

6. A **szűrők hozzáadása**, így megtalálja a kívánt szűrőt, adja meg a szűrő nevét. Válassza ki a szűrőt, és válassza a **+ Hozzáadás**.

   Interchange ellenőrző szám megkereséséhez ebben a példában keres rá a "csomópont" szót, majd kiválasztja **event_record_messageProperties_interchangeControlNumber_s** a szűrőként.

   ![Válassza ki a szűrő](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-add-filter.png)

7. A bal oldali sávon, válassza ki a szűrő értéket használja, és válassza a kívánt **alkalmaz**.

   Ez a példa azt szeretnénk, ha üzenetekhez interchange ellenőrző szám választja ki.

   ![Adja meg a szűrő értéket](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-select-filter-value.png)

8. Térjen vissza a lekérdezést, amely éppen összeállításakor. A lekérdezés a kijelölt szűrő esemény és értékű frissítve lett. Az előző eredmények most túl szűrve.

    ![Térjen vissza a lekérdezés szűrt eredményekkel](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-your-query-for-future-use"></a>Jövőbeli használatra a lekérdezés mentése

1. A lekérdezés a a **naplófájl-keresési** lapon, válassza ki **mentése**. Nevezze el a lekérdezést, válasszon egy kategóriát, és válassza a **mentése**.

   ![A lekérdezés adjon egy nevet és a kategória](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-save.png)

2. A lekérdezés megtekintéséhez válassza **Kedvencek**.

   ![Válassza ki a "Kedvencek"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-query-favorites.png)

3. A **mentett keresések**, válassza ki a lekérdezést, hogy az eredmények megtekinthetők. A lekérdezés, eltérő eredményeket található frissítéséhez szerkessze a lekérdezést.

   ![Válassza ki a lekérdezés](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="find-and-run-saved-queries-in-log-analytics"></a>Keresse meg és Naplóelemzési lekérdezések futtatása

1. Nyissa meg a Naplóelemzési munkaterület kezdőlap (`https://{your-workspace-name}.portal.mms.microsoft.com`), és válassza a **naplófájl-keresési**.

   ![A Naplóelemzési kezdőlapján válassza a "Naplófájl-keresési"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch.png)

   – vagy –

   ![A menüben válassza a "Naplófájl-keresési"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/logsearch-2.png)

2. Az a **naplófájl-keresési** kezdőlapját, válassza a **Kedvencek**.

   ![Válassza ki a "Kedvencek"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-favorites.png)

3. A **mentett keresések**, válassza ki a lekérdezést, hogy az eredmények megtekinthetők. A lekérdezés, eltérő eredményeket található frissítéséhez szerkessze a lekérdezést.

   ![Válassza ki a lekérdezés](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/oms-log-search-find-favorites.png)

## <a name="next-steps"></a>További lépések

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési sémák](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)