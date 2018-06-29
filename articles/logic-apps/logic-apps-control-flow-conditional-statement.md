---
title: Adja hozzá a feltételes utasításokat a munkafolyamatokat – Azure Logic Apps |} Microsoft Docs
description: Az Azure Logic Apps-munkafolyamatok műveleteket szabályzó feltételek létrehozása
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d4e69d33e07f484b4ccc5343786865230368c7ca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096376"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Az Azure Logic Apps munkafolyamat-műveleteket szabályzó feltételes utasítások létrehozása

A Logic Apps alkalmazást az adott műveletek futtatása csak a megadott feltétel sikeres után, vegye fel a *feltételes utasítás*. Ez a struktúra összehasonlítja az adatokat a munkafolyamat jellemző vagy mezők ellen. Majd megadhatja a különféle műveletek alapján a futó-e az adatok megfelel a feltételnek. Feltételek belül egymással ágyazhatja be.

Tegyük fel például, hogy túl sok e-mailt küld, amikor új elemek jelennek meg a webhely RSS-hírcsatorna logikai alkalmazás. Az e-mail üzenetek küldéséhez, csak akkor, ha az új elem egy adott karakterláncot tartalmaz feltételes utasítás adhat hozzá. 

> [!TIP]
> Különböző megadott értékek alapján más lépéseket futtatásához használja a [ *utasítás kapcsoló* ](../logic-apps/logic-apps-control-flow-switch-statement.md) helyette.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Kövesse az ebben a cikkben példa [a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com vagy az Office 365 Outlook-fiókkal.

## <a name="add-a-condition"></a>Feltétel hozzáadása

1. Az a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>, nyissa meg a Logic Apps alkalmazást a Logic App tervezőben.

2. Adja hozzá a feltételt, amelyet a helyen. 

   Lépések között feltétel hozzáadásához húzza az egérmutatót a nyíl ahová adja hozzá a feltételt. Válassza ki a **pluszjel** (**+**), amely akkor jelenik meg, majd válassza a **feltétel hozzáadása**. Példa:

   ![Lépések között feltétel hozzáadása](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Ha hozzá szeretne adni egy feltétel, a logikai alkalmazás alján a munkafolyamat végén válassza **+ új lépés** > **feltétel hozzáadása**.

3. A **feltétel**, a feltétel létrehozása. 

   1. A bal oldali panelen adja meg az adatok vagy az összehasonlítani kívánt mező.

      Gombra a bal oldali panelen belül a dinamikus tartalom listába jelenik meg, így a kimenetek az előző lépéseket a Logic Apps alkalmazást a. 
      Ehhez a példához válassza ki az RSS-hírcsatorna összegzése.

      ![A feltétel létrehozása](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   2. A középső listában válassza ki a végrehajtandó műveletet. 
   Ebben a példában válassza a "**tartalmaz**". 

   3. A megfelelő mezőben adja meg a feltételként egy érték vagy mező. 
   A jelen példában adja meg ezt a karakterláncot: **Microsoft**

   A teljes feltétel a következő:

   ![Teljes feltétel](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

5. A **igaz értéke esetén** és **hamis**, adja hozzá a feltétel teljesülését vizsgálja alapján végrehajtásához szükséges lépéseket. Példa:

   ![A feltétel a "Ha az érték true" és "Ha false" elérési út](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > A meglévő műveletek áthúzhatja a **igaz értéke esetén** és **hamis** elérési utak.

6. Mentse a logikai alkalmazást.

Most már a logic app csak küld mail az RSS-hírcsatorna új elemeinek felel meg a feltételnek.

## <a name="json-definition"></a>JSON-definícióból

Most, hogy a létrehozott logikai alkalmazás egy feltételes utasítás használatával, a magas szintű kód megadása a feltételes utasítás mögött vizsgáljuk meg.

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
            "@triggerBody()?['summary']", "Microsoft"
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
* Küldje el, vagy szavazna funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Futtatás (kapcsoló utasítások) eltérő értékek alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Futtassa a csoportosított (hatókör) állapota alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
