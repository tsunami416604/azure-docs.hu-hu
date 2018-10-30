---
title: Switch-utasítások hozzáadása az Azure Logic Apps-munkafolyamatok – |} A Microsoft Docs
description: Switch-utasítások alapján adott értékekre Azure Logic Apps munkafolyamat-műveletek vezérlő létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232443"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Switch-utasítások, amely alapján az adott értékekre Azure Logic Apps munkafolyamat-műveletek létrehozása

Objektumok, kifejezések vagy jogkivonatok értékei alapján műveleteket futtatni, adjon hozzá egy *váltson* utasítást. Ez a struktúra értékeli ki az objektumot, kifejezés vagy token, úgy dönt, az eset, amely megegyezik az eredményt, és adott esetben csak az adott műveletek futtatása. A switch utasítás futtatása közben csak egyetlen esetet tartalmaz meg kell egyeznie az eredményt.

Például tegyük fel, hogy egy logikai alkalmazást, amely a különböző lépések alapján egy e-mailben a jelölőnégyzetet. Ebben a példában a logikai alkalmazás ellenőrzi egy webhely RSS-Hírcsatornájában keres új tartalmat. Az RSS-hírcsatornában új elem jelenik meg, ha a logikai alkalmazás egy jóváhagyó e-mailt küld. Alapján választja-e a jóváhagyó a "Jóváhagyás" vagy "Elutasítás", a logikai alkalmazás eltérő módon történik.

> [!TIP]
> Minden programozási nyelvet – például a switch-utasítások csak egyenlőségi operátorok támogatja. Ha más relációs operátorokat, például "nagyobb, mint", van szüksége egy [feltételes utasítás](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Ahhoz, hogy determinisztikus végrehajtási viselkedése, esetekben dinamikus jogkivonatok vagy kifejezések helyett egyedi és statikus értéket kell tartalmaznia.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Kövesse az ebben a cikkben szereplő példa [a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com-os vagy Office 365 Outlook-fiókkal.

  1. Amikor hozzáadja a műveletet az e-mailt, keresse meg és válassza inkább a következő műveletet: **jóváhagyási e-mail küldése**

     ![Válassza a "Jóváhagyási e-mail küldése"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Adja meg a kötelező mezőket, például a jóváhagyási e-mailben megkapó személy e-mail címet. 
  A **felhasználói beállítások**, adja meg "Jóváhagyás, Elutasítás".

     ![Adja meg az e-mail adatai](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Switch utasítás hozzáadása

1. Ebben a példában adjon hozzá egy kapcsoló utasítást a végén a munkafolyamat-minta. Válassza ki az utolsó lépés után **új lépés**.

   Ha szeretne hozzáadni egy switch utasítás lépések közötti, vigye az egérmutatót a nyíl felett oda, ahol a switch utasítás hozzá szeretné. Válassza ki a **plusz jelre** (**+**), amely akkor jelenik meg, majd válassza a **művelet hozzáadása**.

1. A Keresés mezőbe írja be "kapcsoló" szűrőként. Válassza a következő műveletet: **váltás – szabályozza**

   ![Kapcsoló hozzáadása](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   A switch utasítás egyetlen esetet tartalmaz, és a egy alapértelmezett eset jelenik meg. 
   Alapértelmezés szerint a switch utasítás legalább egy esetet, valamint az alapértelmezett esetben van szükség. 

   ![Üres alapértelmezett switch utasítás](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Kattintson a **a** mezőre, hogy a dinamikus tartalmak listája jelenik meg. A listában jelölje ki a **SelectedOption** mező, amelynek kimeneti meghatározza, hogy a művelet végrehajtásához. 

   ![Válassza ki a "SelectedOption"](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Kezelje az esetekben, ahol a jóváhagyó kiválasztja `Approve` vagy `Reject`, egy másik eset közötti **eset** és **alapértelmezett**. 

   ![Egy másik eset](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adja hozzá ezeket a műveleteket a megfelelő esetekben:

   | Kis # | **SelectedOption** | Műveletek |
   |--------|--------------------|--------|
   | 1. eset | **Hagyja jóvá** | Adja hozzá az Outlook **e-mail küldése** művelet csak akkor, ha a jóváhagyó kijelölve az RSS-elem részleteinek megadása **jóváhagyás**. |
   | 2. eset | **Elutasítás** | Adja hozzá az Outlook **e-mail küldése** műveletet, hogy az RSS-elem el lett utasítva, más jóváhagyók értesítésére. |
   | Alapértelmezett | None | Nincs szükség műveletre. Ebben a példában a **alapértelmezett** funkcióban üres mert **SelectedOption** csak két pontot tartalmaz. |
   |||

   ![Befejezett switch utasítás](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Mentse a logikai alkalmazást. 

   Ebben a példában manuális teszteléséhez válassza **futtatása** mindaddig, amíg a logikai alkalmazás egy új RSS-elem keresése, és a egy jóváhagyási e-mailt küld. 
   Válassza ki **jóváhagyás** megfigyelni az eredményeket.

## <a name="json-definition"></a>JSON-definíció

Most, hogy létrehozott egy logikai alkalmazást egy switch utasítás használatával, tekintsük át a magas szintű kód definíciója a switch utasítás mögött.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Címke | Leírás |
|-------|-------------|
| `"Switch"`         | A switch utasításban, amely átnevezheti az olvashatóság érdekében neve |
| `"type": "Switch"` | Megadja, hogy a művelet egy switch utasítás |
| `"expression"`     | Ebben a példában adja meg, amely minden esetben, mivel később a definícióban képest értékeli ki a jóváhagyó a kiválasztott beállítás |
| `"cases"` | Minden olyan esetek számát határozza meg. Minden esetben a `"Case_*"` erre az esetre az olvashatóság érdekében átnevezheti alapértelmezett neve |
| `"case"` | Adja meg a kis értéket, amely a switch utasítás az összehasonlítást használó állandó és egyedi értéknek kell lennie. Ha egyetlen eset szereplő műveletek kapcsoló kifejezés eredménye megfelel-e a `"default"` szakasz futnak. | 
| | | 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Küldje el, vagy szavazhat a szolgáltatások vagy a javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Hajtsa végre a lépéseket, egy feltételt (feltételes kifejezések) alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ág)](../logic-apps/logic-apps-control-flow-branches.md)
* [Hajtsa végre a lépéseket, a csoportosított műveleti állapota (hatóköröket) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
