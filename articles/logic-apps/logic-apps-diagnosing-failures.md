---
title: Hibák elhárítása és diagnosztizálása
description: A munkafolyamat-hibák elhárítása és diagnosztizálása Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 93b6d9d2975aa1758afffd19deb1d315b974cc47
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790767"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Munkafolyamatok hibáinak elhárítása és diagnosztizálása Azure Logic Apps

A logikai alkalmazás olyan információkat hoz létre, amelyek segíthetnek az alkalmazásban felmerülő problémák diagnosztizálásában és hibakeresésében. A logikai alkalmazások diagnosztizálásához tekintse át a munkafolyamat egyes lépéseit a Azure Portalon keresztül. Vagy hozzáadhat néhány lépést egy munkafolyamathoz a futásidejű hibakereséshez.

## <a name="review-trigger-history"></a>Triggerek előzményeinek áttekintése

Az egyes logikai alkalmazások triggerrel kezdődnek. Ha az trigger nem indul el, először ellenőrizze az aktiválási előzményeket. Ez az előzmények felsorolja az összes olyan triggert, amelyet a logikai alkalmazás elkészített, valamint az egyes triggerekhez tartozó bemeneti és kimeneti adatokról.

1. A logikai alkalmazás menüjében az **Áttekintés**elemre kattintva ellenőrizhető, hogy a trigger kilőtte-e. Az **aktiválási előzmények**szakaszban tekintse át az trigger állapotát.

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

   ![Triggerek előzményeinek áttekintése](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Ha nem találja a várt adatok valamelyikét, próbálja meg a **frissítés** elemet kiválasztani az eszköztáron.
   > * Ha a lista számos trigger-kísérletet mutat, és nem találja a kívánt bejegyzést, próbálja meg szűrni a listát.

   Itt láthatók az triggerek lehetséges állapotai:

   | Állapot | Leírás | 
   | ------ | ----------- | 
   | **Sikerült** | A trigger ellenőrizte a végpontot, és talált elérhető adatértékeket. Általában a "tüzelt" állapot is megjelenik az állapot mellett. Ha nem, az trigger definíciója rendelkezhet olyan feltételsel vagy `SplitOn` paranccsal, amely nem teljesült. <p>Ez az állapot manuális eseményindítóra, ismétlődési eseményindítóra vagy lekérdezési eseményindítóra alkalmazható. Egy trigger sikeresen futtatható, de a Futtatás akkor is meghiúsul, ha a műveletek nem kezelt hibákat eredményeznek. | 
   | **Kihagyva** | A trigger ellenőrizte a végpontot, de nem talált semmilyen adatsort. | 
   | **Sikertelen** | Hiba történt. A sikertelen triggerek által generált hibaüzenetek áttekintéséhez válassza ki az aktiválási kísérletet, és válassza a **kimenetek**lehetőséget. Előfordulhat például, hogy olyan bemeneteket talál, amelyek nem érvényesek. | 
   ||| 

   Több trigger-bejegyzés is lehet ugyanazzal a dátummal és időponttal, ami akkor történik, ha a logikai alkalmazás több elemet talál. 
   A Logic Apps motor a folyamat minden indításakor létrehoz egy logikai alkalmazás-példányt a munkafolyamat futtatásához. Alapértelmezés szerint minden példány párhuzamosan fut, így a Futtatás elindítása előtt egyetlen munkafolyamatnak sem kell várakoznia.

   > [!TIP]
   > A következő ismétlődés megkezdése nélkül újra megtekintheti az eseményindítót. Az Áttekintés eszköztáron válassza az **trigger futtatása**lehetőséget, majd válassza ki az triggert, amely az ellenőrzések elindítását kényszeríti. Vagy válassza a **futtatás** Logic apps Designer eszköztáron lehetőséget.

3. Ha meg szeretné vizsgálni egy trigger-kísérlet részleteit, az **aktiválási előzmények**területen válassza ki az aktiválási kísérletet. 

   ![Trigger-kísérlet kiválasztása](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Tekintse át a bemeneteket és az trigger által generált kimeneteket. A trigger kimenetei az triggerből származó adatokat jelenítik meg. Ezek a kimenetek segítenek megállapítani, hogy az összes tulajdonság a várt módon lett-e visszaadva.

   > [!NOTE]
   > Ha olyan tartalmat talál, amelyet nem ismer, Ismerje meg, hogyan [kezeli a különböző tartalomtípusokat](../logic-apps/logic-apps-content-type.md)a Azure Logic apps.

   ![Trigger kimenetek](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Minden kilőtt trigger elindítja a munkafolyamat futtatását. Áttekintheti, hogy mi történt a Futtatás során, beleértve a munkafolyamat egyes lépéseinek állapotát, valamint az egyes lépések bemeneteit és kimeneteit is.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. A futtatási **Előzmények**szakaszban tekintse át az indító trigger futtatását.

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

   ![Futtatási előzmények áttekintése](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Ha nem találja a várt adatok valamelyikét, próbálja meg a **frissítés** elemet kiválasztani az eszköztáron.
   > * Ha a lista számos futtatást mutat, és nem találja a kívánt bejegyzést, próbálja meg szűrni a listát.

   A Futtatás lehetséges állapota:

   | Állapot | Leírás | 
   | ------ | ----------- | 
   | **Sikerült** | Az összes művelet sikeres volt. <p>Ha valamilyen hiba történt egy adott művelet során, akkor a munkafolyamatban a következő művelet kezelte a hibát. | 
   | **Sikertelen** | Legalább egy művelet meghiúsult, és a munkafolyamatban a későbbi műveletek nem lettek beállítva a hiba kezelésére. | 
   | **Megszakítva** | A munkafolyamat futott, de megszakítási kérelmet kapott. | 
   | **Fut** | A munkafolyamat jelenleg fut. <p>Ez az állapot a szabályozott munkafolyamatok esetében vagy a jelenlegi díjszabási csomag miatt fordulhat elő. További információkért tekintse meg a [díjszabási oldalon található műveletek korlátait](https://azure.microsoft.com/pricing/details/logic-apps/). Ha beállítja a [diagnosztikai naplózást](../logic-apps/logic-apps-monitor-your-logic-apps.md), az esetlegesen előforduló szabályozási eseményekről is kaphat információt. | 
   ||| 

2. Tekintse át az egyes lépések részleteit egy adott futtatásban. A futtatási **Előzmények**területen válassza ki a vizsgálni kívánt futtatást.

   ![Futtatási előzmények áttekintése](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   Azt jelzi, hogy a Futtatás sikeres vagy sikertelen volt-e, a Futtatás részletei nézet megjeleníti az egyes lépéseket, valamint azt, hogy sikeresek vagy sikertelenek voltak-e.

   ![Logikai alkalmazás futtatási részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Egy adott lépéshez tartozó bemenetek, kimenetek és hibaüzenetek vizsgálatához válassza ki ezt a lépést az alakzat kibontása és a Részletek megjelenítése érdekében. Példa:

   ![Lépés részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Futásidejű hibakeresés végrehajtása

Ha segítségre van szüksége a hibakereséshez, diagnosztikai lépéseket adhat hozzá egy munkafolyamathoz, valamint az trigger és a futtatási előzmények áttekintését. Hozzáadhat például olyan lépéseket, amelyek a [webhook Tester](https://webhook.site/) szolgáltatást használják, így ELLENŐRIZHETI a http-kérelmeket, és meghatározhatja a pontos méretet, alakot és formátumot.

1. [Webhook Tester](https://webhook.site/) megnyitása és a létrehozott egyedi URL-cím másolása

2. A logikai alkalmazásban adjon hozzá egy HTTP POST műveletet a vizsgálni kívánt szövegtörzshöz, például egy kifejezéshez vagy egy másik lépés kimenetéhez.

3. Illessze be a webhook-tesztelő URL-címét a HTTP POST műveletbe.

4. Ha szeretné áttekinteni, hogyan hozza létre a rendszer a Logic Apps motorból generált kérést, futtassa a logikai alkalmazást, és tekintse meg a következőt: webhook Tester.

## <a name="next-steps"></a>Következő lépések

[A logikai alkalmazás figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)
