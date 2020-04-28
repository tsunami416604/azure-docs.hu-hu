---
title: Állapot figyelése, előzmények megtekintése és riasztások beállítása
description: A logikai alkalmazások hibakeresése a futtatási állapot ellenőrzésével, az aktiválási előzmények áttekintésével és a riasztások engedélyezésével Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76907772"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>A Futtatás állapotának figyelése, az aktiválási előzmények áttekintése és a Azure Logic Apps riasztások beállítása

[A logikai alkalmazás létrehozása és futtatása](../logic-apps/quickstart-create-first-logic-app-workflow.md)után megtekintheti a logikai alkalmazás futtatási állapotát, [futtathatja az előzményeket](#review-runs-history), az [aktiválási előzményeket](#review-trigger-history)és a teljesítményt. Ha értesítéseket szeretne kapni a hibákról vagy egyéb lehetséges problémákról, állítson be [riasztásokat](#add-azure-alerts). Létrehozhat például egy olyan riasztást, amely észleli a következőt: "Ha ötnél több futtatása meghiúsul egy órán belül."

A valós idejű események figyelése és a szélesebb körű hibakeresés érdekében [Azure monitor naplók](../azure-monitor/overview.md)használatával állítsa be a logikai alkalmazás diagnosztikai naplózását. Ez az Azure-szolgáltatás segít a felhő és a helyszíni környezetek monitorozásában, így könnyebben megtarthatja a rendelkezésre állást és a teljesítményt. Ezután megkeresheti és megtekintheti az eseményeket, például az események aktiválását, az események futtatását és a műveleti eseményeket. Ha ezeket az információkat [Azure monitor-naplókban](../azure-monitor/platform/data-platform-logs.md)tárolja, létrehozhat olyan [napló-lekérdezéseket](../azure-monitor/log-query/log-query-overview.md) , amelyek segítségével megkeresheti és elemezheti ezeket az adatokat. Ezt a diagnosztikai szolgáltatást más Azure-szolgáltatásokkal, például az Azure Storage-val és az Azure Event Hubs is használhatja. További információ: [Logic Apps-alkalmazások figyelése Azure monitor használatával](../logic-apps/monitor-logic-apps-log-analytics.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Futtatási előzmények áttekintése

Minden alkalommal, amikor az eseményindító egy elemhez vagy eseményhez tüzet hoz létre, a Logic Apps motor létrehoz és futtat egy külön munkafolyamat-példányt minden elemhez vagy eseményhez. Alapértelmezés szerint minden munkafolyamat-példány párhuzamosan fut, így a Futtatás elindítása előtt egyetlen munkafolyamat sem vár. Áttekintheti, hogy mi történt a Futtatás során, beleértve a munkafolyamat egyes lépéseinek állapotát, valamint az egyes lépések bemeneteit és kimeneteit is.

1. A [Azure Portalban](https://portal.azure.com)keresse meg és nyissa meg a logikai alkalmazást a Logic app Designerben.

   A logikai alkalmazás megkereséséhez írja be `logic apps`a következőt a fő Azure-Keresés mezőbe, majd válassza a **Logic apps**lehetőséget.

   ![A "Logic Apps" szolgáltatás megkeresése és kiválasztása](./media/monitor-logic-apps/find-your-logic-app.png)

   A Azure Portal megjeleníti az Azure-előfizetésekhez társított összes logikai alkalmazást. A listát a név, az előfizetés, az erőforráscsoport, a hely stb. alapján szűrheti.

   ![Előfizetésekhez társított logikai alkalmazások megtekintése](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Válassza ki a logikai alkalmazást, majd válassza az **Áttekintés**lehetőséget.

   Az Áttekintés ablaktábla **futtatási előzmények**területén a logikai alkalmazás összes korábbi, aktuális és minden várakozási futtatása megjelenik. Ha a lista számos futtatást mutat, és nem találja a kívánt bejegyzést, próbálja meg szűrni a listát. Ha nem találja a várt adatok valamelyikét, próbálja meg a **frissítés** elemet kiválasztani az eszköztáron.

   ![Áttekintés, futtatási előzmények és egyéb Logic app-információk](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Egy logikai alkalmazás futtatásának lehetséges állapota:

   | status | Leírás |
   |--------|-------------|
   | **Lemondva** | A munkafolyamat futott, de megszakítási kérelmet kapott |
   | **Sikertelen** | Legalább egy művelet meghiúsult, és a munkafolyamatban nem lettek beállítva újabb műveletek a hiba kezelésére. |
   | **Fut** | A munkafolyamat jelenleg fut. <p>Ez az állapot a szabályozott munkafolyamatok esetében vagy a jelenlegi díjszabási csomag miatt is megjelenhet. További információkért tekintse meg a [díjszabási oldalon található műveletek korlátait](https://azure.microsoft.com/pricing/details/logic-apps/). Ha beállítja a [diagnosztikai naplózást](../logic-apps/monitor-logic-apps.md), információt kaphat az esetlegesen előforduló szabályozási eseményekről. |
   | **Sikeres** | Az összes művelet sikeres volt. <p>**Megjegyzés**: Ha valamilyen hiba történt egy adott művelet során, a munkafolyamat egy későbbi művelete ezt a hibát kezelte. |
   | **Várakozás** | A munkafolyamat nem indult el vagy szünetel, például egy még futó korábbi munkafolyamat miatt. |
   |||

1. Egy adott Futtatás lépéseinek és egyéb információinak áttekintéséhez a futtatási **Előzmények**területen válassza ki a Futtatás elemet.

   ![Válassza ki a kívánt futtatást a felülvizsgálathoz](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   A **logikai alkalmazás futtatása** ablaktáblán a kiválasztott Futtatás minden lépése látható, az egyes lépések futtatási állapota, valamint az egyes lépések futtatásához szükséges idő, például:

   ![Az adott Futtatás minden művelete](./media/monitor-logic-apps/logic-app-run-pane.png)

   Ezen információk listázásához a **logikai alkalmazás futtatása** eszköztáron válassza a **Futtatás részletek**lehetőséget.

   ![Az eszköztáron válassza a "részletek futtatása" lehetőséget.](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   A Futtatás részletei nézet az egyes lépéseket, azok állapotát és egyéb információkat jeleníti meg.

   ![Tekintse át a Futtatás egyes lépéseinek részleteit](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Lekérheti például a Futtatás **korrelációs azonosító** tulajdonságát, amelyre szükség lehet, ha a [Logic apps REST API](https://docs.microsoft.com/rest/api/logic)használja.

1. Ha további információra van szükség egy adott lépésről, válassza az egyik lehetőséget:

   * A **logikai alkalmazás futtatása** ablaktáblán válassza ki a lépést, hogy az alakzat kibontva legyen. Mostantól megtekintheti például a bemenetek, a kimenetek és az adott lépésben történt hibák adatait, például:

     ![A logikai alkalmazás futtatási paneljén tekintse meg a sikertelen lépést](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * A **logikai alkalmazás futtatásának részletei** ablaktáblán válassza ki a kívánt lépést.

     ![A Futtatás részletei panelen tekintse meg a sikertelen lépést](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Most már megtekintheti az adott lépéshez tartozó adatokat, például a bemeneteket és kimeneteket, például:

   > [!NOTE]
   > Az összes futásidejű adat és esemény titkosítva van a Logic Apps szolgáltatáson belül. Csak akkor lesznek visszafejtve, ha egy felhasználó az adott adatmegjelenítést kéri. A [futtatási előzményekben elrejtheti a bemeneteket és kimeneteket,](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) vagy vezérelheti az adatok felhasználói hozzáférését az [Azure SZEREPKÖRALAPÚ Access Control (RBAC)](../role-based-access-control/overview.md)használatával.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Triggerek előzményeinek áttekintése

Az egyes logikai alkalmazások futtatása triggerrel kezdődik. Az aktiválási előzmények felsorolja az összes olyan triggert, amelyet a logikai alkalmazás elkészített, valamint az egyes triggerekhez tartozó bemenetekkel és kimenetekkel kapcsolatos információkat.

1. A [Azure Portalban](https://portal.azure.com)keresse meg és nyissa meg a logikai alkalmazást a Logic app Designerben.

   A logikai alkalmazás megkereséséhez írja be `logic apps`a következőt a fő Azure-Keresés mezőbe, majd válassza a **Logic apps**lehetőséget.

   ![A "Logic Apps" szolgáltatás megkeresése és kiválasztása](./media/monitor-logic-apps/find-your-logic-app.png)

   A Azure Portal megjeleníti az Azure-előfizetésekhez társított összes logikai alkalmazást. A listát a név, az előfizetés, az erőforráscsoport, a hely stb. alapján szűrheti.

   ![Előfizetésekhez társított logikai alkalmazások megtekintése](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Válassza ki a logikai alkalmazást, majd válassza az **Áttekintés**lehetőséget.

1. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget. Az **Összefoglalás** szakaszban a **kiértékelés**alatt válassza az **aktiválási előzmények megtekintése**lehetőséget.

   ![A logikai alkalmazáshoz tartozó triggerek előzményeinek megtekintése](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Az aktiválási előzmények ablaktábla megjeleníti a logikai alkalmazás által végrehajtott összes trigger-kísérletet. Minden alkalommal, amikor az eseményindító egy elemhez vagy eseményhez tüzet hoz létre, a Logic Apps motor létrehoz egy különálló logikai alkalmazás-példányt, amely futtatja a munkafolyamatot. Alapértelmezés szerint minden példány párhuzamosan fut, így a Futtatás elindítása előtt egyetlen munkafolyamatnak sem kell várakoznia. Így ha a logikai alkalmazás egyszerre több elemen is aktiválódik, az egyes elemekhez azonos dátummal és idővel rendelkező eseményindító-bejegyzés jelenik meg.

   ![Több trigger-kísérlet különböző elemekhez](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Itt láthatók az triggerek lehetséges állapotai:

   | status | Leírás |
   |--------|-------------|
   | **Sikertelen** | Hiba történt. A sikertelen triggerek által generált hibaüzenetek áttekintéséhez válassza ki az aktiválási kísérletet, és válassza a **kimenetek**lehetőséget. Előfordulhat például, hogy olyan bemeneteket talál, amelyek nem érvényesek. |
   | **Kimarad** | A trigger ellenőrizte a végpontot, de nem talált semmilyen adatsort. |
   | **Sikeres** | A trigger ellenőrizte a végpontot, és talált elérhető adatértékeket. Általában a "tüzelt" állapot is megjelenik az állapot mellett. Ha nem, az trigger definíciója olyan feltétellel vagy `SplitOn` paranccsal rendelkezhet, amely nem teljesült. <p>Ez az állapot manuális eseményindítóra, ismétlődési eseményindítóra vagy lekérdezési eseményindítóra alkalmazható. Egy trigger sikeresen futtatható, de a Futtatás akkor is meghiúsul, ha a műveletek nem kezelt hibákat eredményeznek. |
   |||

   > [!TIP]
   > A következő ismétlődés megkezdése nélkül újra megtekintheti az eseményindítót. Az Áttekintés eszköztáron válassza az **trigger futtatása**lehetőséget, majd válassza ki az triggert, amely az ellenőrzések elindítását kényszeríti. Vagy válassza a **futtatás** Logic apps Designer eszköztáron lehetőséget.

1. Egy adott eseményindító-kísérletre vonatkozó információk megtekintéséhez az eseményindító ablaktáblán válassza ki az eseményindító eseményt. Ha a lista számos trigger-kísérletet mutat, és nem találja a kívánt bejegyzést, próbálja meg szűrni a listát. Ha nem találja a várt adatok valamelyikét, próbálja meg a **frissítés** elemet kiválasztani az eszköztáron.

   ![Adott trigger-kísérlet megtekintése](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Most áttekintheti a kiválasztott eseményindító-eseményre vonatkozó információkat, például:

   ![Adott trigger információinak megtekintése](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Figyelési riasztások beállítása

Ha a logikai alkalmazáshoz meghatározott mérőszámok alapján vagy túllépte a riasztásokat, állítson be [riasztásokat a Azure monitorban](../azure-monitor/platform/alerts-overview.md). Ismerje meg [Az Azure mérőszámait](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Ha [Azure monitor](../log-analytics/log-analytics-overview.md)használata nélkül szeretné beállítani a riasztásokat, kövesse az alábbi lépéseket.

1. A logikai alkalmazás menüjében, a **figyelés**területen válassza a **riasztások** > **új riasztási szabály**lehetőséget.

   ![Riasztás hozzáadása a logikai alkalmazáshoz](./media/monitor-logic-apps/add-new-alert-rule.png)

1. A **szabály létrehozása** ablaktábla **erőforrás**területén válassza ki a logikai alkalmazást, ha még nincs kiválasztva. A **feltétel**területen válassza a **Hozzáadás** lehetőséget, hogy megadhatja a riasztást kiváltó feltételt.

   ![Feltétel hozzáadása a szabályhoz](./media/monitor-logic-apps/add-condition-for-rule.png)

1. A **jel logikai beállítása** panelen keresse meg és válassza ki azt a jelet, amelyhez riasztást szeretne kapni. Használhatja a keresőmezőt, vagy betűrendbe rendezheti a jeleket, és kiválaszthatja a **jel neve** oszlop fejlécét.

   Ha például riasztást szeretne küldeni egy trigger meghibásodása esetén, kövesse az alábbi lépéseket:

   1. A **jel neve** oszlopban keresse meg és válassza ki az **Eseményindítók sikertelen** jelét.

      ![Válassza ki a riasztás létrehozására szolgáló jelet](./media/monitor-logic-apps/find-and-select-signal.png)

   1. A kiválasztott jel megnyíló információs ablaktábláján, a **riasztási logika**területen állítsa be a feltételt, például:

   1. Az **operátor**beállításnál válassza a **nagyobb vagy egyenlő**lehetőséget.

   1. Az **összesítés típusa**beállításnál válassza a **darabszám**lehetőséget.

   1. A **küszöbérték**mezőben adja meg `1`a következőt:.

   1. A **feltétel előnézet**területen ellenőrizze, hogy a feltétel helyesnek tűnik-e.

   1. A **alapján a kiértékelés**alatt állítsa be a riasztási szabály futtatásának intervallumát és gyakoriságát. Az **Összesítés részletessége (időszak)** mezőben válassza ki az adatcsoportosítási időszakot. Az **értékelés gyakorisága**beállításnál válassza ki, hogy milyen gyakran szeretné megtekinteni a feltételt.

   1. Ha elkészült, válassza a **kész**lehetőséget.

   A befejezett feltétel:

   ![Riasztás állapotának beállítása](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   A **szabály létrehozása** lap mostantól megjeleníti a létrehozott feltételt és a riasztás futtatásának költségeit.

   ![Új riasztás a "szabály létrehozása" lapon](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Adja meg a riasztás nevét, nem kötelező leírását és súlyossági szintjét. Vagy hagyja bekapcsolva a **szabály engedélyezése** beállítást, vagy kapcsolja ki, amíg készen nem áll a szabály engedélyezésére.

1. Ha elkészült, válassza a **riasztási szabály létrehozása**lehetőséget.

> [!TIP]
> A logikai alkalmazások riasztásból való futtatásához belefoglalhatja a [kérelem-triggert](../connectors/connectors-native-reqres.md) a munkafolyamatba, amellyel a következő példákhoz hasonló feladatokat végezhet el:
> 
> * [Közzététel a Slackben](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Szöveg küldése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Üzenet hozzáadása egy várólistához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazások figyelése Azure Monitor használatával](../logic-apps/monitor-logic-apps-log-analytics.md)