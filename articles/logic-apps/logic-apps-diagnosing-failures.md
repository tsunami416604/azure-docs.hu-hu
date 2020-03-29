---
title: A munkafolyamatok hibáinak elhárítása és diagnosztizálása
description: Megtudhatja, hogyan háríthatja el és diagnosztizálhatja a munkafolyamatok problémáit, hibáit és hibáit az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905087"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>A munkafolyamatok hibáinak elhárítása és diagnosztizálása az Azure Logic Appsben

A logikai alkalmazás olyan információkat hoz létre, amelyek segíthetnek az alkalmazás problémáinak diagnosztizálásában és hibakeresésében. A logikai alkalmazások diagnosztizálásával a munkafolyamat egyes lépéseit az Azure Portalon keresztül. Vagy hozzáadhat néhány lépést a munkafolyamathoz a futásidejű hibakereséshez.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Eseményindító előzményeinek ellenőrzése

Minden logikai alkalmazás futtatása egy eseményindító kísérlettel kezdődik, így ha az eseményindító nem aktiválódik, kövesse az alábbi lépéseket:

1. Ellenőrizze az eseményindító állapotát [az eseményindító előzményének ellenőrzésével.](../logic-apps/monitor-logic-apps.md#review-trigger-history) Az eseményindítókísérlettel kapcsolatos további információk megtekintéséhez jelölje ki az eseményindító eseményt, például:

   ![Az eseményindító állapotának és előzményének megtekintése](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Ellenőrizze az eseményindító bemeneteit, hogy a várt nak tűnjenek-e. A **Bemenetek hivatkozás csoportban**jelölje ki a **hivatkozást,** amely a Bemenetek ablaktáblát jeleníti meg.

   Az eseményindító bemenetek tartalmazzák azokat az adatokat, amelyeket az eseményindító elvár, és a munkafolyamat elindításához szükséges. Ezeknek a bemeneteknek a áttekintésével meghatározhatja, hogy az eseményindító bemenetei helyesek-e, és hogy teljesült-e a feltétel, hogy a munkafolyamat folytathassa.

   Az itt `feedUrl` található tulajdonság nak például helytelen AZ RSS-hírcsatornája értéke:

   ![Hibák esetén vizsgálja meg az eseményindító-bemeneteket](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Ellenőrizze az eseményindítók kimeneteit, ha vannak ilyenek, hogy ellenőrizze, hogy a várt módon jelennek-e meg. A **Kimenetek hivatkozás csoportban**válassza ki a Kimenetek ablaktáblát megjelenítő **hivatkozást.**

   Az eseményindító kimenetek tartalmazzák azokat az adatokat, amelyeket az eseményindító a munkafolyamat következő lépésére ad át. Ezeknek a kimeneteknek a áttekintésével meghatározhatja, hogy a helyes vagy várt értékek átkerültek-e a munkafolyamat következő lépésére, például:

   ![Hibák esetén vizsgálja meg az eseményindító kimeneteket](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Ha olyan tartalmat talál, amelyet nem ismer fel, további információ a [különböző tartalomtípusokról](../logic-apps/logic-apps-content-type.md) az Azure Logic Apps-ben.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Az ellenőrzés lefutja az előzményeket

Minden alkalommal, amikor az eseményindító egy elem vagy esemény, a Logic Apps motor létrehoz és futtat egy külön munkafolyamat-példány minden elem vagy esemény. Ha egy futtatás sikertelen, kövesse az alábbi lépéseket a futtatás során történt műveletek áttekintéséhez, beleértve a munkafolyamat egyes lépéseinek állapotát, valamint az egyes lépések bemeneteit és kimeneteit.

1. Ellenőrizze a munkafolyamat futási állapotát [a futtatási előzmények ellenőrzésével.](../logic-apps/monitor-logic-apps.md#review-runs-history) Ha további információkat szeretne megtekinteni egy sikertelen futtatásról, beleértve az állapotukban futó összes lépést, válassza a sikertelen futtatást.

   ![Futtatási előzmények megtekintése és sikertelen futtatás kiválasztása](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Miután a futtatás összes lépése megjelenik, bontsa ki az első sikertelen lépést.

   ![Első sikertelen lépés kibontása](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Ellenőrizze a sikertelen lépés bemeneteit, és ellenőrizze, hogy a várt nak való megjelenésük-e.

1. Tekintse át az egyes lépéseket egy adott futtatás során. A **Futtatások előzményei**csoportban válassza ki a vizsgálni kívánt futtatást.

   ![Az ellenőrzés futtatási előzményei](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Logikai alkalmazás futtatási részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Egy adott lépés bemenetének, kimenetének és hibaüzenetének vizsgálatához válassza ki azt a lépést úgy, hogy az alakzat kitáguljon, és megjelenítse a részleteket. Példa:

   ![Lépés részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Futtatókörnyezet hibakeresésének végrehajtása

A hibakeresés segítése érdekében diagnosztikai lépéseket adhat hozzá a logikai alkalmazás munkafolyamatához, valamint áttekintheti az eseményindítót, és futtathatja az előzményeket. Hozzáadhat például a [Webhook-tesztelő](https://webhook.site/) szolgáltatást használó lépéseket, hogy megvizsgálhassa a HTTP-kérelmeket, és meghatározhassa azok pontos méretét, alakját és formátumát.

1. Nyissa meg a [Webhook-tesztelő](https://webhook.site/) webhelyét, és másolja a létrehozott egyedi URL-címet.

1. A logikai alkalmazásban adjon hozzá egy HTTP POST-műveletet, valamint a tesztelni kívánt törzstartalmat, például egy kifejezést vagy egy másik lépéskimenetet.

1. Illessze be az URL-címet a Webhook-tesztelőből a HTTP POST műveletbe.

1. Tekintse át, hogyan jön létre egy kérelem, amikor a Logic Apps motorból jön létre, futtassa a logikai alkalmazást, és további részletekért látogasson el újra a Webhook-tesztelő webhelyére.

## <a name="next-steps"></a>További lépések

* [A logikai alkalmazás figyelése](../logic-apps/monitor-logic-apps.md)
