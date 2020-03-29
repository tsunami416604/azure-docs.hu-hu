---
title: Állapotfigyelés, előzmények megtekintése és riasztások beállítása
description: Logikai alkalmazások hibaelhárítása a futtatási állapot ellenőrzésével, az eseményindítók előzményeinek áttekintésével és a riasztások engedélyezésével az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907772"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Futásállapotának figyelése, az eseményindítók előzményeinek áttekintése és riasztások beállítása az Azure Logic Apps alkalmazásokhoz

Miután [létrehozott és futtatott egy logikai alkalmazást,](../logic-apps/quickstart-create-first-logic-app-workflow.md)ellenőrizheti, hogy a logikai alkalmazás futási állapota, [előzmények futtatása,](#review-runs-history) [eseményindítóelőzmények](#review-trigger-history)és a teljesítmény. Ha értesítéseket szeretne kapni a hibákról vagy más lehetséges problémákról, állítson be [riasztásokat.](#add-azure-alerts) Létrehozhat például egy riasztást, amely észleli, hogy "ha egy óra alatt ötnél több futtatás sikertelen".

A valós idejű eseményfigyeléshez és a gazdagabb hibakereséshez állítsa be a diagnosztikai naplózást a logikai alkalmazáshoz az [Azure Monitor naplói](../azure-monitor/overview.md)nak használatával. Ez az Azure-szolgáltatás segít a felhőbeli és helyszíni környezetek figyelésében, így könnyebben megőrizheti azok rendelkezésre állását és teljesítményét. Ezután megkeresheti és megtekintheti az eseményeket, például az eseményindító eseményeket, az események futtatását és a műveleteseményeket. Ezen adatok az [Azure Monitor naplókban](../azure-monitor/platform/data-platform-logs.md)való tárolásával [naplólekérdezéseket](../azure-monitor/log-query/log-query-overview.md) hozhat létre, amelyek segítenek megtalálni és elemezni ezeket az információkat. Ezeket a diagnosztikai adatokat más Azure-szolgáltatásokkal, például az Azure Storage-szal és az Azure Event Hubs-szal is használhatja. További információ: [Logic apps monitor az Azure Monitor használatával.](../logic-apps/monitor-logic-apps-log-analytics.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Az ellenőrzés futtatási előzményei

Minden alkalommal, amikor az eseményindító egy elem vagy esemény, a Logic Apps motor létrehoz és futtat egy külön munkafolyamat-példány minden elem vagy esemény. Alapértelmezés szerint minden munkafolyamat-példány párhuzamosan fut, így egyetlen munkafolyamatnak sem kell várnia a futtatás megkezdése előtt. Megtekintheti, hogy mi történt a futtatás során, beleértve a munkafolyamat egyes lépései állapotát, valamint az egyes lépcsők bemeneteit és kimeneteit.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg a logikai alkalmazást a Logic App Designerben.

   A logikai alkalmazás megkereséséhez írja be `logic apps`a fő Azure-keresőmezőbe a beírást, és válassza a **Logic Apps**lehetőséget.

   ![A "Logic Apps" szolgáltatás megkeresése és kiválasztása](./media/monitor-logic-apps/find-your-logic-app.png)

   Az Azure Portalon az Azure-előfizetésekhez társított összes logikai alkalmazás látható. Ezt a listát név, előfizetés, erőforráscsoport, hely és így tovább alapján szűrheti.

   ![Előfizetésekhez társított logikai alkalmazások megtekintése](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Jelölje ki a logikai alkalmazást, majd válassza **az Áttekintés**lehetőséget.

   Az áttekintő ablaktáblán az **Előzmények futtatása**csoportban megjelenik az összes korábbi, aktuális és várakozási futtatás a logikai alkalmazáshoz. Ha a listában sok futtatás látható, és nem találja a kívánt bejegyzést, próbálja meg szűrni a listát. Ha nem találja a várt adatokat, próbálja meg a **Frissítés** lehetőséget az eszköztáron.

   ![Áttekintés, előzmények futtatása és egyéb logikai alkalmazásadatok](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   A logikai alkalmazások futtatásának lehetséges állapotai a következők:

   | status | Leírás |
   |--------|-------------|
   | **Lemondva** | A munkafolyamat futott, de megszakítási kérelem érkezett |
   | **Nem sikerült** | Legalább egy művelet sikertelen volt, és a munkafolyamatban nem történt későbbi művelet a hiba kezelésére |
   | **Fut** | A munkafolyamat jelenleg fut. <p>Ez az állapot a szabályozott munkafolyamatokvagy az aktuális díjszabási terv miatt is megjelenhet. További információt az [árképzési oldalon található műveletkorlátokban](https://azure.microsoft.com/pricing/details/logic-apps/)talál. Ha diagnosztikai [naplózást](../logic-apps/monitor-logic-apps.md)állít be, információt kaphat a szabályozási eseményekről. |
   | **Sikeres** | Minden művelet sikeres volt. <p>**Megjegyzés:** Ha egy adott műveletben hibák történtek, a munkafolyamat egy későbbi művelete kezelte a hibát. |
   | **Várakozás** | A munkafolyamat még nem indult el, vagy szünetel, például egy korábbi, még futó munkafolyamat miatt. |
   |||

1. Egy adott futtatás lépéseinek és egyéb információinak áttekintéséhez az **Előzmények futtatása**csoportban jelölje ki a futtatást.

   ![Az átvizsgálandó adott futtatás kiválasztása](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   A **Logikai alkalmazás futtatása** ablaktábla megjeleníti a kijelölt futtatás minden lépését, az egyes lépések futási állapotát, valamint az egyes lépésekkel a futtatáshoz szükséges időt, például:

   ![Minden egyes művelet az adott futtatásban](./media/monitor-logic-apps/logic-app-run-pane.png)

   Ha ezt az információt listaformában szeretné megtekinteni, a **Logikai alkalmazás futtatása** eszköztáron válassza a **Részletek futtatása**lehetőséget.

   ![Az eszköztáron válassza a "Részletek futtatása" lehetőséget.](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   A Futtatás részletei nézet az egyes lépéseket, azok állapotát és egyéb információkat jeleníti meg.

   ![A futtatás egyes lépései részleteinek áttekintése](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Például lekaphatja a futtatás **korrelációs azonosító** tulajdonságát, amelyre szükség lehet a [REST API logic apps használatához.](https://docs.microsoft.com/rest/api/logic)

1. Ha további információt szeretne kapni egy adott lépésről, válassza a következő lehetőséget:

   * A **Logikai alkalmazás futtatása** ablaktáblán jelölje ki a lépést úgy, hogy az alakzat kitáguljon. Most már megtekintheti az olyan információkat, mint a bemenetek, kimenetek és az ebben a lépésben előforduló hibák, például:

     ![A logikai alkalmazás futtatásának ablaktábláján a sikertelen lépés megtekintése](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * A **Logikai alkalmazás további részletek** ablaktábláján jelölje ki a kívánt lépést.

     ![A futtatásrészletei ablaktáblán a nézet sikertelen lépése](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     Most már megtekintheti az adatokat, például a bemenetek és kimenetek az adott lépéshez, például:

   > [!NOTE]
   > Minden futásidejű adatok és események titkosítva vannak a Logic Apps szolgáltatás. A rendszer csak akkor fejti vissza őket, ha a felhasználó az adatok megtekintését kéri. Az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md)segítségével [elrejtheti a bemeneteket és kimeneteket a futtatási előzményekben,](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) vagy szabályozhatja a felhasználók hozzáférését ezekhez az adatokhoz.

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Eseményindító előzményeinek áttekintése

Minden logikai alkalmazás futtatása egy eseményindítóval kezdődik. Az eseményindító előzmények felsorolja az összes eseményindító kísérletek, amelyek a logikai alkalmazás által végzett, és az egyes eseményindító kísérletek bemenetek és kimenetek információkat.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és nyissa meg a logikai alkalmazást a Logic App Designerben.

   A logikai alkalmazás megkereséséhez írja be `logic apps`a fő Azure-keresőmezőbe a beírást, és válassza a **Logic Apps**lehetőséget.

   ![A "Logic Apps" szolgáltatás megkeresése és kiválasztása](./media/monitor-logic-apps/find-your-logic-app.png)

   Az Azure Portalon az Azure-előfizetésekhez társított összes logikai alkalmazás látható. Ezt a listát név, előfizetés, erőforráscsoport, hely és így tovább alapján szűrheti.

   ![Előfizetésekhez társított logikai alkalmazások megtekintése](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Jelölje ki a logikai alkalmazást, majd válassza **az Áttekintés**lehetőséget.

1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. Az **Összegzés** csoport **Kiértékelés**területén válassza az **Eseményindító előzményeinek megtekintése**lehetőséget.

   ![A logikai alkalmazás eseményindítóelőzményeinek megtekintése](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Az eseményindító előzmények ablaktábla a logikai alkalmazás által végrehajtott összes eseményindító kísérletet jeleníti meg. Minden alkalommal, amikor az eseményindító egy elem vagy esemény, a Logic Apps motor létrehoz egy külön logikai alkalmazáspéldány, amely futtatja a munkafolyamatot. Alapértelmezés szerint minden példány párhuzamosan fut, így egyetlen munkafolyamatnak sem kell várnia a futtatás megkezdése előtt. Így ha a logikai alkalmazás egyszerre több elemen aktiválódik, minden elemhez megjelenik egy azonos dátummal és idővel rendelkező eseményindító bejegyzés.

   ![Több eseményindító kísérlet különböző elemekhez](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Az eseményindító kísérlet lehetséges állapotai a következők:

   | status | Leírás |
   |--------|-------------|
   | **Nem sikerült** | Hiba történt. A sikertelen eseményindítóhoz létrehozott hibaüzenetek áttekintéséhez jelölje ki az eseményindító kísérletet, és válassza **a Kimenetek**lehetőséget. Előfordulhat például, hogy érvénytelen bemeneteket talál. |
   | **Kimarad** | Az eseményindító ellenőrizte a végpontot, de nem talált adatokat. |
   | **Sikeres** | Az eseményindító ellenőrizte a végpontot, és elérhető adatokat talált. Általában egy "Fired" állapot is megjelenik az állapot mellett. Ha nem, az eseményindító definíciója lehet, hogy egy feltétel vagy `SplitOn` parancs, amely nem teljesült. <p>Ez az állapot vonatkozhat egy manuális eseményindítóra, ismétlődési eseményindítóra vagy lekérdezési eseményindítóra. Az eseményindító sikeresen futtatható, de maga a futtatás továbbra is sikertelen lehet, ha a műveletek nem kezelt hibákat hoznak létre. |
   |||

   > [!TIP]
   > Az eseményindítót újra ellenőrizheti anélkül, hogy megvárna a következő ismétlődésre. Az áttekintő eszköztáron válassza az **Eseményindító futtatása**lehetőséget, és jelölje ki az eseményindítót, amely egy ellenőrzést kényszerít. Vagy válassza a **Futtatás** a Logic Apps Designer eszköztáron lehetőséget.

1. Egy adott eseményindítókísérlettel kapcsolatos információk megtekintéséhez az eseményindító ablaktáblán jelölje ki az eseményindító eseményt. Ha a listában sok eseményindító kísérlet látható, és nem találja a kívánt bejegyzést, próbálja meg szűrni a listát. Ha nem találja a várt adatokat, próbálja meg a **Frissítés** lehetőséget az eszköztáron.

   ![Adott eseményindító-kísérlet megtekintése](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   Most már áttekintheti a kiválasztott eseményindító esemény adatait, például:

   ![Adott eseményindító-információk megtekintése](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Figyelési riasztások beállítása

Ha adott metrikák alapján szeretne értesítéseket kapni, vagy túllépi a logikai alkalmazás küszöbértékeit, állítson be [riasztásokat az Azure Monitorban.](../azure-monitor/platform/alerts-overview.md) Ismerje meg [az Azure metrikáit.](../monitoring-and-diagnostics/monitoring-overview-metrics.md) Riasztások beállítása az [Azure Monitor](../log-analytics/log-analytics-overview.md)használata nélkül kövesse az alábbi lépéseket.

1. A logikai alkalmazás menüjében válassza a **Figyelés** **csoportBan** > a Riasztások**új riasztási szabályt.**

   ![Riasztás hozzáadása a logikai alkalmazáshoz](./media/monitor-logic-apps/add-new-alert-rule.png)

1. A **Szabály létrehozása** ablaktáblán az **Erőforrás**csoportban jelölje ki a logikai alkalmazást, ha még nincs kijelölve. A **Feltétel csoportban**válassza a **Hozzáadás** lehetőséget, hogy meghatározhassa a riasztást kiváltó feltételt.

   ![Feltétel hozzáadása a szabályhoz](./media/monitor-logic-apps/add-condition-for-rule.png)

1. A **Jellogika konfigurálása** ablaktáblán keresse meg és válassza ki azt a jelet, amelyről riasztást szeretne kapni. A keresőmező segítségével vagy a jelek betűrendben történő rendezéséhez jelölje ki a **Jelnév** oszlop fejlécét.

   Ha például riasztást szeretne küldeni, ha egy eseményindító meghibásodik, kövesse az alábbi lépéseket:

   1. A **Jel név** oszlopban keresse meg és jelölje ki a **Sikertelen eseményindítók** jelet.

      ![A riasztás létrehozásához szolgáló jel kiválasztása](./media/monitor-logic-apps/find-and-select-signal.png)

   1. A kijelölt jelhez megnyíló információs ablaktáblán a **Riasztási logika**csoportban állítsa be a feltételt, például:

   1. Az **Operátor csoportban**válassza **a Nagyobb vagy azzal egyenlő lehetőséget.**

   1. **Az Összesítés típusa mezőben**válassza **a Darabszám**lehetőséget.

   1. A **Küszöbérték**mezőbe `1`írja be a mezőt.

   1. A **Feltétel előnézete csoportban**ellenőrizze, hogy az állapota helyesnek tűnik-e.

   1. A **Kiértékelés alapján csoportban**állítsa be a riasztási szabály futtatásának időközét és gyakoriságát. Az **Összesítés részletessége (időszak)** esetében válassza ki az adatok csoportosításának időszakát. A **Kiértékelés gyakorisága**területen adja meg, hogy milyen gyakran szeretné ellenőrizni a feltételt.

   1. Ha készen áll, válassza a **Kész gombot.**

   Itt a kész feltétel:

   ![Riasztási feltétel beállítása](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   A **Szabály létrehozása** lap mostantól a létrehozott feltételt és a riasztás futtatásának költségét jeleníti meg.

   ![Új riasztás a "Szabály létrehozása" oldalon](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Adja meg a riasztás nevét, nem kötelező leírását és súlyossági szintjét. Hagyja bekapcsolva az **Engedélyezés szabályt a létrehozási beállításután,** vagy kapcsolja ki, amíg készen nem áll a szabály engedélyezésére.

1. Ha elkészült, válassza **a Figyelmeztetési szabály létrehozása**lehetőséget.

> [!TIP]
> Ha egy logikai alkalmazást riasztásból szeretne futtatni, a [kérelem-eseményindítót](../connectors/connectors-native-reqres.md) is beillesztheti a munkafolyamatba, amely lehetővé teszi az alábbi példákhoz hasonló feladatok elvégzését:
> 
> * [Közzététel a tartalékidőbe](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Küldjön egy szöveget](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Üzenet hozzáadása várólistához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>További lépések

* [Logikai alkalmazások figyelése az Azure Monitor használatával](../logic-apps/monitor-logic-apps-log-analytics.md)