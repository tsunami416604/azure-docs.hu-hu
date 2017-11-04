---
title: "Hibaelhárítását és diagnosztizálását hibák - Azure Logic Apps |} Microsoft Docs"
description: "Útmutató és érvek sikertelen-e a logic apps megismerése"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 10/15/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: de706f711e9c57b2e575d130a2a0cfd0bdc907a1
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2017
---
# <a name="troubleshoot-and-diagnose-logic-app-failures"></a>Hibaelhárítását és diagnosztizálását logic app hibák

A Logic Apps alkalmazást hoz létre, amelyek segítségével diagnosztizálhatja és elháríthatja az alkalmazásban felmerülő információkat. Az Azure portálon keresztül a munkafolyamatban lévő egyes lépések alapján diagnosztizálhatja a logikai alkalmazás. Vagy a munkafolyamat futásidejű hibakeresési néhány lépést is hozzáadhat.

## <a name="review-trigger-history"></a>Ellenőrzés eseményindító előzményei

Minden logikai alkalmazás eseményindító kezdődik. Ha az eseményindító nem érvényesítést, először ellenőrizze a eseményindító előzményeket. Az előzmények összes eseményindító kísérletet a Logic Apps alkalmazást és bemenetekhez és kimenetekhez minden eseményindító kísérlet részleteit sorolja fel.

1. Ellenőrizze, hogy az eseményindító következik be, a logic app menüben válassza a **áttekintése**. A **eseményindító előzmények**, tekintse át az eseményindító állapotát.

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

   ![Ellenőrzés eseményindító előzményei](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * Ha nem találja a várt adatokat, próbálkozzon **frissítése** az eszköztáron.
   > * Ha a lista mutatja azokat, számos kísérletek indítható el, és a bejegyzés nem található, próbálja meg a tanúsítványlista szűrésekor.

   Az alábbiakban a lehetséges állapotok egy eseményindító kísérlet során:

   | status | Leírás | 
   | ------ | ----------- | 
   | **Sikeres volt.** | Az eseményindító be van jelölve a végpontot, és elérhető adatokat talált a rendszer. Általában egy "Fired" állapota is ez az állapot mellett jelenik meg. Ha nem, az eseményindító definícióját előfordulhat, hogy rendelkezhet condition tulajdonsággal, vagy `SplitOn` parancsot, amely nem teljesült. <p>Ez az állapot manuális eseményindító, ismétlődési eseményindító vagy lekérdezési eseményindító alkalmazhatja. A trigger sikeresen futtatható, de maga a Futtatás továbbra is meghiúsulhat, ha a művelet nem kezelt hibákat generálnak. | 
   | **Kihagyva** | Az eseményindító be van jelölve, a végpont, azonban adatot nem található. | 
   | **Nem sikerült** | Hiba történt. Tekintse át a sikertelen eseményindító létrehozott hibaüzeneteket, jelölje ki a eseményindító kísérlet, és válassza a **kimenetek**. Például előfordulhat, bemeneti adatokat, amelyek nem érvényes. | 
   ||| 

   Lehetséges, hogy ilyen dátum és idő, amely akkor fordul elő, amikor a Logic Apps alkalmazást talál több elem több eseményindító bejegyzést. 
   Minden alkalommal, amikor az eseményindító következik be, a Logic Apps motor hoz létre egy logic app-példány a munkafolyamat futtatásához. Alapértelmezés szerint minden példánya fut párhuzamosan úgy, hogy nem a munkafolyamatot futtató indítása előtt.

   > [!TIP]
   > Az eseményindító pontosítható Várakozás a következő ismétlődés nélkül. Áttekintés eszköztárán válassza **eseményindító futtassa**, és válassza ki az eseményindító, amely arra kényszeríti az ellenőrzést. Vagy válassza ki **futtatása** Logic Apps Designer eszköztáron.

3. A részletes egy eseményindító kísérlet alatt vizsgálata **eseményindító előzmények**, válassza ki az eseményindító kísérlet. 

   ![Válasszon egy eseményindító kísérlet](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. Tekintse át az adatokat és az eseményindító által létrehozott kimenet. Eseményindító kimenetének megjelenítése az adatokat, amely innen származik: az eseményindító. A kimenetek segítségével meghatározhatja, hogy az összes tulajdonság adott vissza a várt módon.

   > [!NOTE]
   > Ha telepített tartalmak nem világos, ismerje meg az Azure Logic Apps [kezeli a különböző típusú tartalmakat](../logic-apps/logic-apps-content-type.md).

   ![Eseményindító kimenete](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>Futtatási előzmények áttekintése

Minden egyes égetett indítsák el egy munkafolyamat futtatásához. Mi történt alatt futó, beleértve a munkafolyamat, valamint a be- és kimenetekkel egyes lépéseihez szükséges az egyes lépéseinek állapotát tekintheti meg.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. A **előzmények fut**, tekintse át a futtatáskor a égetett eseményindító.

   > [!TIP]
   > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

   ![Tekintse át futtatja előzmények](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * Ha nem találja a várt adatokat, próbálkozzon **frissítése** az eszköztáron.
   > * Ha a lista mutatja azokat a sok fut, és a bejegyzésre, próbálja szűrni a lista nem található.

   Az alábbiakban a lehetséges állapotok az futtató:

   | status | Leírás | 
   | ------ | ----------- | 
   | **Sikeres volt.** | Minden művelet sikeresen befejeződött. <p>Hiba történt egy adott művelet, ha a munkafolyamat a következő művelet kezelni, hogy a hibát. | 
   | **Nem sikerült** | Legalább egy művelete sikertelen volt, és a munkafolyamat a későbbi műveletek nem voltak állítsa be a hibakezeléshez olyan. | 
   | **Megszakítva** | A munkafolyamat működő állapotban volt, de a megszakítási kérelmet kapott. | 
   | **Fut** | A munkafolyamat jelenleg fut. <p>Ez az állapot akkor fordulhat elő, a szabályozottan halmozott munkafolyamatok, vagy a jelenlegi tarifacsomag miatt. További információkért lásd: a [művelet vonatkozó korlátozások a tarifákat tartalmazó oldalt](https://azure.microsoft.com/pricing/details/logic-apps/). Ha korábban beállított [diagnosztikai naplózás](../logic-apps/logic-apps-monitor-your-logic-apps.md), bármely késleltetési eseményeket információ segítségével is lehet. | 
   ||| 

2. A megadott futtató lévő egyes lépések részletes adatok áttekintésére. A **előzmények fut**, jelölje be a Futtatás, amelyet meg szeretne vizsgálni.

   ![Tekintse át futtatja előzmények](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   E a Futtatás maga sikeres vagy sikertelen volt, a részletek futtassa a nézet megjeleníti az egyes lépések, és hogy azok sikeres vagy sikertelen volt.

   ![Logikai alkalmazás futtatási részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. Vizsgálja meg a bemenetek, kimenetek és egy adott lépésre vonatkozó hibaüzeneteket, válassza a lépés, hogy az alakzat bővíti, és részleteit jeleníti meg. Példa:

   ![Lépés részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Hajtsa végre a futásidejű hibakeresés

A hibakeresés érdekében hozzáadhat diagnosztikai munkafolyamat, és tekintse át az eseményindító szükséges további lépéseket, és előzmények futtatja. Például lépéseket vehet fel, amelyek használják a [RequestBin](http://requestb.in) szolgáltatást, hogy vizsgálja meg a HTTP-kérelmekre, és határozza meg a pontos méretét, alakzat és formátumban.

1. Hozzon létre egy RequestBin, amelyek személyes és csak a böngésző megtekinthető.

2. A Logic Apps alkalmazást adja hozzá a szervezet szeretné tesztelni, például tartalom, kifejezés vagy egy másik lépés kimeneti egy HTTP POST műveletet.

3. Illessze be az URL-címet a RequestBin a HTTP POST műveletet.

4. Tekintse át, hogyan kérés formátuma a Logic Apps motor létrehozásakor, futtassa a logikai alkalmazást, és frissítse a RequestBin.

## <a name="next-steps"></a>Következő lépések

[A logikai alkalmazás figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)