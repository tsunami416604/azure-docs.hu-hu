---
title: Feltételes utasítások hozzáadása munkafolyamatokhoz
description: Olyan feltételek létrehozása, amelyek az Azure Logic Apps munkafolyamataiban végrehajtott műveleteket szabályozzák
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: fe79cf5af86e1f303e4735214b993d8db4488a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793251"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Feltételes utasítások létrehozása, amelyek az Azure Logic Apps munkafolyamat-műveleteit szabályozzák

Ha adott műveleteket csak egy megadott feltétel nek ad át, adjon hozzá egy *feltételes utasítást.* Ez a vezérlőstruktúra összehasonlítja a munkafolyamat adatait bizonyos értékekkel vagy mezőkkel. Ezután különböző műveleteket adhat meg, amelyek attól függően futnak, hogy az adatok megfelelnek-e a feltételnek. A feltételekegymásba ágyazhatók.

Tegyük fel például, hogy van egy logikai alkalmazás, amely túl sok e-mailt küld, amikor új elemek jelennek meg a webhely RSS-hírcsatornájában. Feltételes utasítást csak akkor adhat hozzá az e-mail küldéséhez, ha az új elem egy adott karakterláncot tartalmaz. 

> [!TIP]
> Ha különböző konkrét értékeken alapuló különböző lépéseket szeretne futtatni, használjon [*kapcsolóutasítást.*](../logic-apps/logic-apps-control-flow-switch-statement.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A jelen cikkben szereplő példát követve [hozza létre ezt a mintalogikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com vagy Office 365 Outlook-fiókkal.

## <a name="add-condition"></a>Feltétel hozzáadása

1. Az <a href="https://portal.azure.com" target="_blank">Azure Portalon</a>nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Adjon hozzá egy feltételt a kívánt helyen. 

   Ha feltételt szeretne hozzáadni a lépések közé, vigye az egérmutatót azon nyíl fölé, ahová a feltételt hozzá szeretné adni. Válassza ki a**+** megjelenő **pluszjelet** ( ), majd a **Művelet hozzáadása**lehetőséget. Példa:

   ![Művelet hozzáadása lépések közé](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Ha a munkafolyamat végén szeretne feltételt hozzáadni, a logikai alkalmazás alján válassza az **Új lépés** > **művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "feltétel" szót. Válassza a következő műveletet: **Feltétel - Vezérlés**

   ![Feltétel hozzáadása](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. A **Feltétel** mezőben építsd meg az állapotodat. 

   1. A bal oldali mezőben adja meg az összehasonlítani kívánt adatokat vagy mezőt.

      Ha a bal oldali mezőre kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a logikai alkalmazás előző lépéseiből származó kimeneteket. 
      Ebben a példában válassza ki az RSS-hírcsatorna összegzését.

      ![Építsd meg az állapotod](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. A középső mezőben jelölje ki a végrehajtani kívánt műveletet. 
   Ebben a példában válassza a "**tartalmazza a**" lehetőséget. 

   1. A jobb oldali mezőben adjon meg egy értéket vagy mezőt feltételként. 
   Ebben a példában adja meg ezt a karakterláncot: **Microsoft**

   Itt a teljes feltétel:

   ![Teljes feltétel](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Ha egy másik sort szeretne hozzáadni a feltételhez, válassza **a Hozzáadás** > **i sor lehetőséget.** 
   Ha alfeltételeket is felszeretne adni egy csoportot, válassza **a Hozzáadás** > **csoport**lehetőséget. 
   Meglévő sorok csoportosításához jelölje be a sorok jelölőnégyzetét, válassza bármelyik sor három pont (...) gombját, majd válassza a **Csoport keresése lehetőséget.**

1. Az If True és a If False **(Ha** **hamis)** csoportban adja hozzá a feltétel teljesülése alapján végrehajtandó lépéseket. Példa:

   ![Feltétel a "Ha igaz" és a "Ha hamis" elérési útokkal](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > A meglévő műveleteket a **Ha igaz** és a **Ha hamis** elérési utakba húzhatja.

1. Mentse a logikai alkalmazást.

Ez a logikai alkalmazás most antól csak akkor küld e-mailt, ha az RSS-hírcsatorna új elemei megfelelnek az Ön állapotának.

## <a name="json-definition"></a>JSON-definíció

Íme a feltételes utasítás mögött immár magas szintű kóddefiníció:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciók és javaslatok elküldéséhez vagy az azokra való szavazáshoz látogasson el az [Azure Logic Apps felhasználói visszajelzési webhelyére.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>További lépések

* [Lépések futtatása különböző értékek alapján (kapcsolóutasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Lépések futtatása és ismétlése (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveletállapot (hatókörök) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
