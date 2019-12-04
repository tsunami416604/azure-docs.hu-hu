---
title: Logikai alkalmazások figyelése Azure Monitor
description: Elemzések és hibakeresési műveletek beolvasása a Logic app-futtatások hibakereséséhez és diagnosztizálásához Azure Monitor naplók használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: 305b50c86a468354f049fcc57fcb79b537e8dfed
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791893"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Elemzések és hibakeresési eredmények beolvasása a Logic apps számára Azure Monitor naplók használatával

A logikai alkalmazásokkal kapcsolatos további információk monitorozásához és részletes hibakereséséhez kapcsolja be [Azure monitor naplókat](../log-analytics/log-analytics-overview.md) a logikai alkalmazás létrehozásakor. Azure Monitor naplók diagnosztikai naplózást és figyelést biztosítanak a logikai alkalmazásaihoz, amikor a Azure Portal telepíti a Logic Apps felügyeleti megoldást. Ezzel a megoldással a logikai alkalmazáshoz kapcsolódó összesített információk is rendelkezésre állnak konkrét részletekkel, például az állapottal, a végrehajtás idejével, az újraküldési állapottal és a korrelációs azonosítókkal. Ez a cikk bemutatja, hogyan lehet bekapcsolni Azure Monitor naplókat, így megtekintheti a logikai alkalmazás futtatásának futtatókörnyezeti eseményeit és adatait.

Ez a témakör bemutatja, hogyan állíthat be Azure Monitor naplókat a logikai alkalmazás létrehozásakor. Ha be szeretné kapcsolni Azure Monitor naplókat a meglévő logikai alkalmazásokhoz, az alábbi lépésekkel [bekapcsolhatja a diagnosztikai naplózást, és elküldheti a Logic app Runtime-adataikat Azure monitor naplókba](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Ez az oldal korábban ismertette a feladatok végrehajtásának lépéseit a Microsoft Operations Management Suite (OMS), amely a [2019 januárjában](../azure-monitor/platform/oms-portal-transition.md)megszűnt, és lecseréli ezeket a lépéseket [Azure monitor naplókra](../azure-monitor/platform/data-platform-logs.md), amelyek leváltották a log Analytics kifejezést. A naplózási adatokat a rendszer továbbra is egy Log Analytics munkaterületen tárolja, és ugyanazokat a Log Analytics szolgáltatást gyűjti és elemzi. További információ: [Azure monitor terminológiai változások](../azure-monitor/terminology.md).

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt Log Analytics munkaterületre van szükség. Megtudhatja [, hogyan hozhat létre log Analytics munkaterületet](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Új logikai alkalmazások naplózásának bekapcsolása

1. A [Azure Portalban](https://portal.azure.com)hozza létre a logikai alkalmazást. Az Azure fő menüjében válassza az **erőforrás létrehozása** > **integráció** > **logikai alkalmazás**lehetőséget.

   ![Új logikai alkalmazás létrehozása](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. A **logikai alkalmazás**területen hajtsa végre az alábbi lépéseket:

   1. Adja meg a logikai alkalmazás nevét, és válassza ki az Azure-előfizetését.

   1. Hozzon létre vagy válasszon ki egy Azure-erőforráscsoportot. Válassza ki a logikai alkalmazás helyét.

   1. A **log Analytics**alatt válassza **a**be lehetőséget.

   1. A **log Analytics munkaterület** listából válassza ki azt a munkaterületet, ahová el szeretné küldeni a logikai alkalmazás futtatását.

      ![Logikai alkalmazás adatainak megadása](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Miután befejezte ezt a lépést, az Azure létrehozza a logikai alkalmazást, amely most már társítva van a Log Analytics munkaterülethez. Emellett ez a lépés automatikusan telepíti a Logic Apps felügyeleti megoldást a munkaterületen.

   1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A logikai alkalmazás futtatásának megtekintéséhez [folytassa a következő lépésekkel](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Logic Apps felügyeleti megoldás telepítése

Ha már beállította Azure Monitor naplókat a logikai alkalmazás létrehozásakor, hagyja ki ezt a lépést. Már telepítve van a Logic Apps felügyeleti megoldás.

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe keresse meg a "log Analytics-munkaterületek" elemet, és válassza a **log Analytics munkaterületek**lehetőséget.

   !["Log Analytics munkaterületek" kiválasztása](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. A **log Analytics munkaterületek**területen válassza ki a munkaterületet.

   ![Log Analytics munkaterület kiválasztása](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Az Áttekintés ablaktáblán, az **első lépések a Log Analytics** > a **figyelési megoldások konfigurálása**területen válassza a **megoldások megtekintése**lehetőséget.

   ![Válassza a "megoldások megtekintése" lehetőséget.](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Az **Áttekintés**területen válassza a **Hozzáadás**lehetőséget.

   ![Válassza a "Hozzáadás" lehetőséget.](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. A **piactér** megnyitása után a keresőmezőbe írja be a "Logic apps Management" kifejezést, és válassza a **Logic apps felügyelet**lehetőséget.

   ![Válassza a "Logic Apps felügyelet" lehetőséget.](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. A megoldás leírása panelen válassza a **Létrehozás**lehetőséget.

   ![A "létrehozás" lehetőség kiválasztása a "Logic Apps Management" számára](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Tekintse át és erősítse meg a Log Analytics munkaterületet, ahol telepíteni szeretné a megoldást, majd válassza a **Létrehozás** újra lehetőséget.

   ![A "létrehozás" lehetőség kiválasztása a "Logic Apps Management" számára](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Miután az Azure üzembe helyezte a megoldást az Log Analytics munkaterületet tartalmazó Azure-erőforráscsoport számára, a megoldás megjelenik a munkaterület összefoglalás paneljén.

   ![Munkaterület összegzése panel](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Logikai alkalmazás futtatási adatainak megtekintése

A logikai alkalmazás futtatása után megtekintheti a futtatások állapotát és darabszámát a **Logic apps felügyeleti** csempén.

1. Lépjen a Log Analytics munkaterületre, és válassza a **munkaterület összefoglalása** > **Logic apps felügyelet**lehetőséget.

   ![Logikai alkalmazás futtatásának állapota és darabszáma](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   Itt a logikai alkalmazás futtatását név vagy végrehajtási állapot szerint csoportosítjuk. Ezen a lapon a logikai alkalmazás futtatásához kapcsolódó műveletek és eseményindítók hibáinak részleteit is láthatja.

   ![A logikai alkalmazás futtatási állapotának összegzése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Egy adott logikai alkalmazás vagy állapot összes futtatásának megtekintéséhez válassza ki a logikai alkalmazás vagy az állapot sorát.

   Az alábbi példa egy adott logikai alkalmazás összes futtatását mutatja be:

   ![Logikai alkalmazás futtatásának és állapotának megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Ezen az oldalon speciális beállítások érhetők el: 

   * **Követett tulajdonságok** oszlop: egy olyan logikai alkalmazás esetében, amelyben a követett tulajdonságok a műveletek szerint vannak csoportosítva, a tulajdonságok ezen az oszlopon tekinthetők meg. A követett tulajdonságok megtekintéséhez válassza a **nézet**lehetőséget. A követett tulajdonságok kereséséhez használja az oszlop szűrőt.

      ![Logikai alkalmazás nyomon követett tulajdonságainak megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Minden újonnan hozzáadott nyomon követett tulajdonság 10-15 percet is igénybe vehet, mielőtt első alkalommal megjelenjenek. Ismerje meg [, hogyan adhat hozzá nyomon követett tulajdonságokat a logikai alkalmazáshoz](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Újraküldés**: egy vagy több olyan logikai alkalmazás újraküldését végezheti el, amely sikertelen, sikeres vagy még fut. Jelölje be az újraküldeni kívánt futtatásokhoz tartozó jelölőnégyzeteket, majd kattintson az **Újraküldés**elemre.

     ![Logikai alkalmazás futtatásának újraküldése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Az eredmények szűréséhez ügyféloldali és Kiszolgálóoldali szűrés is végezhető.

   * **Ügyféloldali szűrő**: minden oszlopnál válassza ki a kívánt szűrőket, például:

     ![Példa oszlop szűrői](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Kiszolgálóoldali szűrő**: adott időablak kiválasztásához vagy a megjelenő futtatások számának korlátozásához használja az oldal tetején található hatókör-vezérlőelemet. Alapértelmezés szerint egyszerre csak 1 000 rekord jelenik meg.

     ![Az időablak módosítása](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Egy adott Futtatás összes műveletének és részletes adatainak megtekintéséhez válassza ki a logikai alkalmazás futtatásának sorát.

   Az alábbi példa egy adott logikai alkalmazás futtatásának összes műveletét és eseményindítóját mutatja be:

   ![Logikai alkalmazás futtatási műveleteinek megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. Bármely találati oldalon megtekintheti az eredmények mögötti lekérdezést, vagy az összes eredmény megjelenítéséhez válassza az **összes megtekintése**lehetőséget, amely megnyitja a **naplók** lapot.

   ![Az összes eredmény megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   A **naplók** lapon a következő lehetőségek közül választhat:

   * A lekérdezés eredményeinek megtekintéséhez kattintson a **tábla**elemre.

   * A lekérdezések a [Kusto lekérdezési nyelvét](https://aka.ms/LogAnalyticsLanguageReference)használják, amelyet szerkeszthet, ha különböző eredményeket szeretne megtekinteni. A lekérdezés módosításához frissítse a lekérdezési karakterláncot, és válassza a **Futtatás** lehetőséget a tábla eredményeinek megtekintéséhez. 

     ![Log Analytics – lekérdezés nézet](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Következő lépések

* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)