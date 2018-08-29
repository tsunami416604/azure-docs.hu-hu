---
title: Hibaelhárítását és diagnosztizálását hibák – Azure Logic Apps |} A Microsoft Docs
description: Ismerje meg az Azure Logic Apps munkafolyamat-hibák diagnosztizálására és megoldására
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 994e7945a7107815029bd415f4cc0d45bb68e335
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123687"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Az Azure Logic Apps munkafolyamat-hibák diagnosztizálása és hibáinak elhárítása

A logikai alkalmazás állít elő, információkat, amelyek segítségével diagnosztizálhatja és elháríthatja a problémákat az alkalmazásban. Felderítheti a logikai alkalmazás a munkafolyamat az Azure Portalon minden lépése áttekintésével. Vagy a munkafolyamat futásidejű hibakeresést is hozzáadhat néhány lépést.

## <a name="review-trigger-history"></a>Triggerelőzmények áttekintése

Minden logikai alkalmazás az eseményindító kezdődik. Ha az eseményindító nem indul el, először ellenőrizze az eseményindító-előzményeket. Az előzményekben felsorolja az összes eseményindító kísérleteket, a logikai alkalmazás által végrehajtott és bemenetek és kimenetek minden trigger kísérlet részleteit.

1. Ellenőrizze, hogy az eseményindító aktiválódott, a logikai alkalmazás menüjében válassza a **áttekintése**. A **eseményindítási előzményeinek**, tekintse át a trigger állapotát.

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

   ![Triggerelőzmények áttekintése](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Ha nem találja a várt adatok, próbálkozzon **frissítése** az eszköztáron.
   > * Ha a lista számos aktiválása kísérletek, és nem találja a kívánt bejegyzést jeleníti meg, próbálja meg a tanúsítványlista.

   Az alábbiakban egy eseményindító kísérlet a lehetséges állapotok:

   | status | Leírás | 
   | ------ | ----------- | 
   | **Sikeres volt** | Az eseményindító be van jelölve a végpont, és a rendelkezésre álló adatok találhatók. Általában egy "Fired" állapota is mellett ez az állapot jelenik meg. Ha nem, az eseményindító definíciójában előfordulhat, hogy rendelkezik egy feltétel, vagy `SplitOn` parancsot, amely nem teljesült. <p>Ez az állapot egy manuális eseményindító, ismétlődési eseményindító vagy egy lekérdezés eseményindító alkalmazhatja. A trigger sikeresen futtatható, de maga a Futtatás továbbra is meghiúsulhat, ha a művelet nem kezelt hibát. | 
   | **Kihagyva** | Az eseményindító be van jelölve, a végpont, de adatot nem található. | 
   | **Nem sikerült** | Hiba történt. Tekintse át a sikertelen eseményindítók létrehozott hibaüzeneteket, a trigger kísérlet válassza, majd **kimenetek**. Észreveheti például bemenet nem érvényes. | 
   ||| 

   Előfordulhat, hogy több eseményindító bejegyzések ilyen dátumot és időpontot, amely a történik, ha a logikai alkalmazás több elem is talál. 
   Minden akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt a munkafolyamat futtatásához. Alapértelmezés szerint minden példány párhuzamosan fut, hogy nem a munkafolyamat nem futtató indítása előtt.

   > [!TIP]
   > Az eseményindító pontosítható Várakozás a következő ismétlődés nélkül. Az Áttekintés eszköztáron válassza **trigger futtatása**, és válassza ki az eseményindító, amely arra kényszeríti az ellenőrzést. Vagy válassza **futtatása** a Logic Apps Designer eszköztáron.

3. Egy eseményindító kísérlet részleteinek vizsgálatához alatt **eseményindítási előzményeinek**, válassza ki, hogy az eseményindító kísérlet. 

   ![Válasszon egy eseményindító kísérlet](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Tekintse át a bemeneteit és kimeneteit, az eseményindító által előállított. Triggerek be jeleníthetők meg a tartalomcsomagokból származó az eseményindító. Ezeket a kimeneteket segítségével meghatározhatja, hogy e az összes tulajdonság a várt módon adja vissza.

   > [!NOTE]
   > Ha nem ismeri a tartalmak, ismerje meg az Azure Logic Apps [kezeli a különböző típusú tartalmakra](../logic-apps/logic-apps-content-type.md).

   ![Trigger kimenete](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Minden egyes elindított trigger elindítja egy munkafolyamat-Futtatás. Mi történt, amely futtatja, beleértve az egyes lépések állapotát a munkafolyamatot, valamint bemeneteit és kimeneteit mindegyik lépéshez során tekintheti meg.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. A **futtatási előzmények**, tekintse át a az elindított trigger futtatása.

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

   ![A futtatási előzmények áttekintése](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Ha nem találja a várt adatok, próbálkozzon **frissítése** az eszköztáron.
   > * Ha a listán megjelenik számos fut, és nem találja a kívánt bejegyzést, próbálja meg a tanúsítványlista.

   Futtatás a lehetséges állapotok a következők:

   | status | Leírás | 
   | ------ | ----------- | 
   | **Sikeres volt** | Minden művelet sikeresen befejeződött. <p>Ha hibákat egy adott működés közben történt, a munkafolyamat a következő művelet kezeli, a hiba. | 
   | **Nem sikerült** | Legalább egy műveletet nem sikerült, és a munkafolyamatban a későbbi műveletek nem állított be a hiba kezelésére. | 
   | **Megszakítva** | A munkafolyamat működő állapotban volt, de a megszakítási kérelem érkezett. | 
   | **Fut** | A munkafolyamat jelenleg fut. <p>Ez az állapot akkor fordulhat elő, szabályozott munkafolyamatokat, akár a jelenlegi díjszabási csomag miatt. További információkért lásd: a [művelet korlátok a díjszabási lapon](https://azure.microsoft.com/pricing/details/logic-apps/). Ha beállította a [diagnosztikai célú naplózásának](../logic-apps/logic-apps-monitor-your-logic-apps.md), bármely történik késleltetési eseményekkel kapcsolatos információkat is kaphat. | 
   ||| 

2. Egy adott Futtatás minden lépése részleteit is érdemes megvizsgálni. A **futtatási előzmények**, válassza ki a vizsgálni kívánt Futtatás.

   ![A futtatási előzmények áttekintése](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   E a Futtatás sikeres vagy sikertelen magát, a Futtatás részletei nézeten áttekintheti az egyes lépések, és hogy azok sikeres vagy sikertelen.

   ![Logikai alkalmazás futtatási részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Vizsgálja meg a bemenetek, kimenetek és egy adott lépésre vonatkozó hibaüzeneteket, válassza ezt a lépést, hogy az alakzat oktatóanyagon, és a részleteket jeleníti meg. Példa:

   ![Lépés részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Hajtsa végre a futásidejű hibakeresést

A hibakeresés érdekében hozzáadhat diagnosztikai lépések egy munkafolyamathoz, és az eseményindító áttekintése és a futtatási előzmények. Például használatára vonatkozó lépéseket adhat hozzá a [Webhook tesztelő](https://webhook.site/) szolgáltatást, hogy vizsgálja meg a HTTP-kérelmekre, és a pontos méretét, az alakzat és a formátum meghatározása.

1. Látogasson el [Webhook tesztelő](https://webhook.site/) , és másolja az egyedi URL-cím létrehozása

2. A logikai alkalmazásban adjon hozzá egy HTTP POST műveletet, amelyet szeretne tesztelni, például szövegtörzse, lépés másik kimeneti vagy egy kifejezés.

3. Illessze be az URL-címet a Webhook Tester a HTTP POST műveletet.

4. Tekintse át, hogyan lett létrehozva egy kérelmet a Logic Apps-motor által létrehozott, a logikai alkalmazás futtatása és Webhook tesztelő részletekért lásd:.

## <a name="next-steps"></a>További lépések

[A logikai alkalmazás figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)
