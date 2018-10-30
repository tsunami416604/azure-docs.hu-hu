---
title: Adja hozzá a feltételes utasításokat-munkafolyamatok – Azure Logic Apps |} A Microsoft Docs
description: Az Azure Logic Apps-munkafolyamatokban műveleteket szabályzó feltételek létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 9ee484971e217b0ca4dd7ad855e9e6dc3313e5d4
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230335"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Hozzon létre feltételes utasításokat, amelyek vezérlik a munkafolyamat-műveletek az Azure Logic Appsben

Bizonyos műveletek csak a megadott feltétel átadása után a logikai alkalmazás fut, adjon hozzá egy *feltételes utasítás*. Ez a vezérlő struktúra hasonlítja össze az adatokat a munkafolyamatban meghatározott értékek vagy mezők. Megadhatja, hogy különböző műveleteket, amelyek alapján futtathatók-e az adatok megfelel a megadott követelményeknek. Beágyazhatja feltételek belül egymással.

Tegyük fel például, egy logikai alkalmazást, amely túl sok e-mailt küld, amikor egy webhely RSS-hírcsatornában új elemek jelennek meg. E-mail küldése, csak akkor, amikor az új elem egy adott karakterláncot tartalmaz egy feltételes utasítást adhat hozzá. 

> [!TIP]
> Különböző lépéseket különböző megadott értékek alapján futtatásához használja a [ *utasítás váltson* ](../logic-apps/logic-apps-control-flow-switch-statement.md) helyette.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Kövesse az ebben a cikkben szereplő példa [a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com-os vagy Office 365 Outlook-fiókkal.

## <a name="add-condition"></a>Feltétel hozzáadása

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. Adjon hozzá egy feltételt a kívánt helyen található. 

   Lépések közötti feltétel hozzáadása, vigye az egérmutatót a nyíl felett, amelyre a feltétel hozzáadása. Válassza ki a **plusz jelre** (**+**), amely akkor jelenik meg, majd válassza a **művelet hozzáadása**. Példa:

   ![Lépések közötti művelet hozzáadása](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Ha szeretne hozzáadni egy feltételt, alján, a logikai alkalmazás a munkafolyamat végén **új lépés** > **művelet hozzáadása**.

1. A Keresés mezőbe írja be a "feltétel" szűrőként. Válassza a következő műveletet: **feltétel – szabályozza**

   ![Feltétel hozzáadása](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Az a **feltétel** mezőben hozzon létre a feltételt. 

   1. A bal oldali panelen adja meg az adatok vagy összehasonlítani kívánt mezőt.

      Kattintson a bal oldali panelen, amikor a dinamikus tartalmak listája jelenik meg, így a kimenet az előző lépésekből a logikai alkalmazásban. 
      Ebben a példában válassza ki az RSS-hírcsatorna összegzése.

      ![Hozzon létre a feltételt](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. A középső mezőben válassza ki a végrehajtandó műveletet. 
   Ebben a példában válassza a "**tartalmaz**". 

   1. A megfelelő mezőben adja meg egy érték vagy mező a megadott feltételeknek. 
   Ebben a példában adja meg a következő karakterláncot: **Microsoft**

   Itt látható a teljes feltétel:

   ![Teljes feltétel](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Új sor hozzáadása a feltételhez, kattintson a **Hozzáadás** > **sor hozzáadása**. 
   A subconditions csoport hozzáadásához válassza a **Hozzáadás** > **csoport hozzáadása**. 
   Létező sorok csoportosítása, jelölje be a jelölőnégyzeteket a sorokat, a három pontra (…) gombra az összes sort, és válassza **márka csoport**.

1. Alatt **ha igaz** és **ha hamis**, adja hozzá a feltétel teljesülését alapján végrehajtásához szükséges lépéseket. Példa:

   ![A feltétel "Ha true" és "Ha false" elérési út](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Áthúzhatja a meglévő műveleteket, a **ha igaz** és **ha hamis** elérési utak.

1. Mentse a logikai alkalmazást.

Ez a logikai alkalmazás most már levelet küld, csak akkor, amikor az RSS-hírcsatorna új elemeinek megfelel a feltételnek.

## <a name="json-definition"></a>JSON-definíció

A következő mögött feltételes utasítás a magas szintű kód definíciója:

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
* Küldje el, vagy szavazhat a funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Hajtsa végre a lépéseket, a különböző értékek (switch-utasítások) alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ág)](../logic-apps/logic-apps-control-flow-branches.md)
* [Hajtsa végre a lépéseket, a csoportosított műveleti állapota (hatóköröket) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
