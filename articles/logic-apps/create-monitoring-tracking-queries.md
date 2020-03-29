---
title: Logikai alkalmazások lekérdezéseinek megtekintése és létrehozása az Azure Monitor naplóiban
description: Lekérdezések megtekintése és létrehozása az Azure Logic Apps naplóiban az Azure Monitor naplóiban
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908071"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Az Azure Logic Apps figyelőnaplóiban figyelési és nyomon követési lekérdezések megtekintése és létrehozása

Megtekintheti az alapul szolgáló lekérdezések, amelyek az eredményeket az Azure Monitor naplók és [lekérdezések,](../log-analytics/log-analytics-overview.md) amelyek szűrik az eredményeket az adott feltételek alapján. Megkeresheti például az üzeneteket egy adott csomópont-vezérlőszám alapján. A lekérdezések a [Kusto lekérdezési nyelvet](https://aka.ms/LogAnalyticsLanguageReference)használják, amelyet szerkeszthet, ha különböző eredményeket szeretne megtekinteni. További információt az [Azure Monitor naplólekérdezései című témakörben talál.](../azure-monitor/log-query/query-language.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ha nem rendelkezik Log Analytics-munkaterülettel, ismerje meg, [hogyan hozhat létre Log Analytics-munkaterületet.](../azure-monitor/learn/quick-create-workspace.md)

* Az Azure Monitor naplózásával beállított logikai alkalmazás, amely elküldi ezeket az adatokat a Log Analytics-munkaterületre. Ismerje [meg, hogyan állíthatja be az Azure Monitor naplóit a logikai alkalmazáshoz.](../logic-apps/monitor-logic-apps.md)

* Ha integrációs fiókot használ, győződjön meg arról, hogy beállította a fiókot az Azure Monitor naplózással, hogy ezeket az adatokat egy Log Analytics-munkaterületre küldje. Ismerje meg, hogyan állíthatja be az [Azure Monitor naplózását az integrációs fiókhoz.](../logic-apps/monitor-b2b-messages-log-analytics.md)

## <a name="view-queries-behind-results"></a>Eredmények mögötti lekérdezések megtekintése

Az eredményeket a munkaterület összegzésében megjelenő lekérdezés megtekintéséhez vagy szerkesztéséhez hajtsa végre az alábbi lépéseket:

1. Bármely eredménylapon, alul válassza az **Összes megjelenítése**lehetőséget.

   ![Az összes eredmény megtekintése](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Megnyílik a Naplók lap, és az előző eredményoldal mögötti lekérdezést jeleníti meg.

   ![Naplók lap – lekérdezési nézet](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. A **Naplók** lapon a következő beállításokat választhatja meg:

   * Ha a lekérdezés eredményeit táblaként szeretné megtekinteni, a lekérdezésszerkesztő alatt válassza a **Tábla**lehetőséget.

   * A lekérdezés módosításához frissítse a lekérdezési karakterláncot, és válassza a **Futtatás** lehetőséget a tábla eredményeinek megtekintéséhez.

## <a name="create-your-own-query"></a>Saját lekérdezés létrehozása

Ha adott tulajdonságok vagy értékek alapján szeretné megkeresni vagy szűrni az eredményeket, létrehozhatja saját lekérdezését üres lekérdezésből kiindulva, vagy használhat egy meglévő lekérdezést. További információ: [Első lépések a naplólekérdezésekkel az Azure Monitorban.](../azure-monitor/log-query/get-started-queries.md)

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki a Log Analytics-munkaterületet.

1. A munkaterület **menüáltalános**területén válassza a **Naplók**lehetőséget.

1. Kezdje üres lekérdezésből vagy bármely elérhető meglévő lekérdezésből.

   * Annak ellenőrzéséhez, hogy vannak-e elérhető kontrábok, a lekérdezés eszköztárán válassza a **Minták lekérdezések előzményei** > **History**lehetőséget, amely az előző lekérdezések ből származó lekérdezéseket jeleníti meg, vagy válassza a **Lekérdezéskezelő**lehetőséget, amely előre összeállított lekérdezéseket jelenít meg.

     Például a Logic Apps B2B megoldás biztosítja a következő előre összeállított lekérdezéseket:

     ![Kezdje a "Logic Apps B2B" megoldás előre összeállított lekérdezéseivel](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Üres lekérdezésből való kiinduláshoz a lekérdezésszerkesztőben kezdje el beírni a [kusto lekérdezési nyelvet](../azure-monitor/log-query/query-language.md) a lekérdezéshez.

     ![Kezdés üres lekérdezéssel](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>További lépések

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési sémák](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)