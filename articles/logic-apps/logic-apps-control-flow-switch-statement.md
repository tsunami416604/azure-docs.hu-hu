---
title: "Váltás utasítások - Futtatás lépéseket a konkrét értékek - Azure Logic Apps alapján |} Microsoft Docs"
description: "Objektumok, kifejezés vagy tokeneket a logic apps értékei alapján különböző lépések futtatásával"
services: logic-apps
keywords: "Switch utasítás"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: e1f515189be8a5659af0f6c29b3fac0550abc9f9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="switch-statements-run-different-steps-based-on-specific-values-in-logic-apps"></a>Váltás utasításokat: különböző lépéseket a logic apps a megadott értékek alapján

Objektum, a kifejezés vagy a token értékei alapján különböző lépések végrehajtásához használja a *kapcsoló* utasítást. Ez a struktúra kiértékeli az objektum, kifejezés vagy token, úgy dönt, az eset, amely megfelel az eredmény, és csak az adott esethez lépéseket futtatja. Amikor a switch utasítás fut, csak egyetlen esetet meg kell felelnie az eredményt.

Tegyük fel például azt szeretné, hogy a logikai alkalmazás, amely végrehajtja az e-mailben kiválasztott lehetőségnek alapján különböző műveleteket. Ebben a példában a logikai alkalmazás ellenőrzi a webhely RSS-hírcsatorna új tartalom. Az RSS-hírcsatorna megjelenik egy új elemet, a logikai alkalmazás jóváhagyó e-mailt küld. Hogy a jóváhagyó választja-e a "Jóváhagyás" vagy "Elutasítás" alapján, a logikai alkalmazás eltérő módon történik.

> [!TIP]
> Minden programozási nyelv, például a switch utasítás csak egyenlőségi operátorok támogatja. Ha szükség más relációs operátorok, például a "nagyobb, mint", akkor egy [feltételes utasítás](#conditions).
> Ahhoz, hogy determinisztikus végrehajtási viselkedésének, esetekben dinamikus jogkivonatok vagy kifejezések helyett egyedi és statikus értéket kell tartalmaznia.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Kövesse az ebben a cikkben példa [a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com vagy az Office 365 Outlook-fiókkal.

  1. Amikor az e-mail üzenetek küldéséhez a művelet, válassza ki a **jóváhagyása e-mailt küldeni** helyette.

     ![Válassza a "Jóváhagyási e-mail küldése"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Adja meg a kötelező mezőket, például a személy, aki a jóváhagyási e-mailben kap e-mail címet. 
  A **felhasználói beállítások**, adja meg "Jóváhagyása, elutasítása".

     ![Adja meg az e-mailek részletei](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Adjon hozzá egy kapcsoló utasítást

1. Válassza ki a minta-munkafolyamat végén **+ új lépés** > **... További** > **kapcsoló eset hozzáadása**. 

   ![Adjon hozzá egy kapcsoló utasítást](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   A switch utasítás egyetlen esetet és egy alapértelmezett esetben jelenik meg. 
   A switch utasítás szükséges legalább egy esetet, valamint az alapértelmezett eset. 

   Ha hozzá szeretne adni egy switch utasítás lépések között, vigye az egérmutatót a nyíl ahol hozzáadandó a switch utasítás. 
   Válassza ki a **pluszjel** (**+**), amely akkor jelenik meg, majd válassza a **kapcsoló eset hozzáadása**.

4. Az a **a** mezőben válassza a **SelectedOption** mező, amelynek kimenete meghatározza, hogy milyen műveletet kell végrehajtani. 
   
   Kiválaszthatja a mezőt a **dinamikus tartalom hozzáadása az** lista, amely akkor jelenik meg.

5. A esetek, ahol a jóváhagyó kiválasztja kezelésére `Approve` vagy `Reject`, adja hozzá egy másik helyzet közötti **eset** és **alapértelmezett**. 
   
6. Vegye fel ezeket a műveleteket a megfelelő esetekben:

   | Nagybetűk # | **SelectedOption** | Műveletek |
   |:------ |:-------------------|:------ |
   | 1. eset | **Jóváhagyása** | Adja hozzá az Outlook **egy e-mailek küldése** művelet az RSS-elem adatokat küldjenek a jóváhagyó kiválasztásakor csak **jóváhagyás**. |
   | 2. eset | **Elutasítása** | Adja hozzá az Outlook **egy e-mailek küldése** , hogy az RSS-elem vissza lett utasítva, más jóváhagyóknak értesítési művelet. |
   | Alapértelmezett | \<none\> | Nincs szükség műveletre. Ebben a példában a **alapértelmezett** esetben üres mert **SelectedOption** csak két pontot tartalmaz. |
   |         |          |

   ![Switch utasítás](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Mentse a logikai alkalmazást. 

   Ez a példa manuális teszteléséhez válassza **futtatása** mindaddig, amíg a logic app keresi meg egy új RSS és jóváhagyási e-mailt küld. 
   Válassza ki **jóváhagyás** és figyelje meg az eredményeket.

## <a name="json-definition"></a>JSON-definícióból

Most, hogy a létrehozott logikai alkalmazás egy switch utasítás használatával, a magas szintű kód megadása a switch utasítás mögött vizsgáljuk meg.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
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

| Címke              | Leírás |
| :----------------- | :---------- |
| `"Switch"`         | A kapcsoló utasítást, ami az olvashatóság átnevezheti neve |
| `"type": "Switch"` | Megadja, hogy a művelet egy switch utasítás |
| `"expression"`     | Ebben a példában határozza meg, amely minden esetben későbbi szakaszában a definícióban megadott képest értékeli ki a jóváhagyó kijelölt beállítás |
| `"cases"` | Meghatározza bármely esetek számát. A minden esetben `"Case_*"` az adott esetben az olvashatóság átnevezhető alapértelmezett neve. |
| `"case"` | Adja meg az eset értéket, amely a switch utasítás az összehasonlításhoz használó állandó és egyedi értéknek kell lennie. Ha egyetlen eset felel meg a kapcsoló kifejezés eredményének, a műveletek a `"default"` szakasz futnak.
|           |         |

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Küldje el, vagy szavazna szolgáltatások vagy a javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [A lépéseket (feltételes utasítások) feltétel alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Futtassa a csoportosított (hatókör) állapota alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)