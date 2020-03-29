---
title: Kapcsolóutasítások hozzáadása a munkafolyamatokhoz
description: Kapcsolóutasítások létrehozása, amelyek az Azure Logic Apps adott értékei alapján szabályozzák a munkafolyamat-műveleteket
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 5c40feec2dca65e4bc9617a71a6d0a8e4c872a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74793235"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Olyan kapcsolóutasítások létrehozása, amelyek munkafolyamat-műveleteket futtatnak az Azure Logic Apps adott értékei alapján

Objektumok, kifejezések vagy jogkivonatok értékein alapuló konkrét műveletek *switch* futtatásához adjon hozzá egy kapcsolóutasítást. Ez a struktúra kiértékeli az objektumot, kifejezést vagy jogkivonatot, kiválasztja az eredménynek megfelelő esetet, és csak az adott esethez futtat konkrét műveleteket. Amikor a kapcsolóutasítás fut, csak egy eset nek kell egyeznie az eredménnyel.

Tegyük fel például, hogy egy logikai alkalmazást szeretne, amely különböző lépéseket tesz az e-mailben kiválasztott beállítás alapján. Ebben a példában a logikai alkalmazás ellenőrzi a webhely RSS-hírcsatornáját az új tartalom ért. Amikor egy új elem jelenik meg az RSS-hírcsatornában, a logikai alkalmazás e-mailt küld egy jóváhagyónak. Attól függően, hogy a jóváhagyó a "Jóváhagyás" vagy az "Elutasítás" lehetőséget választja, a logikai alkalmazás különböző lépéseket követ.

> [!TIP]
> Mint minden programozási nyelv, a switch-utasítások is csak az egyenlőségi szolgáltatókat támogatják. Ha más relációs operátorokra van szüksége, például "nagyobb, mint", használjon [feltételes utasítást.](../logic-apps/logic-apps-control-flow-conditional-statement.md)
> A determinisztikus végrehajtási viselkedés biztosítása érdekében az eseteknek dinamikus jogkivonatok vagy kifejezések helyett egyedi és statikus értéket kell tartalmazniuk.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A jelen cikkben szereplő példát követve [hozza létre ezt a mintalogikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com vagy Office 365 Outlook-fiókkal.

  1. Amikor hozzáadja az e-mail küldéséhez szükséges műveletet, keresse meg és jelölje ki helyette ezt a műveletet: **Jóváhagyási e-mail küldése**

     ![Válassza a "Jóváhagyási e-mail küldése" lehetőséget](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Adja meg a szükséges mezőket, például a jóváhagyási e-mailt kérő személy e-mail címét. 
  A **Felhasználói beállítások csoportban**adja meg a "Jóváhagyás, Elutasítás" értéket.

     ![Adja meg az e-mail adatait](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Kapcsolóutasítás hozzáadása

1. Ebben a példában adjon hozzá egy kapcsolóutasítást a mintamunkafolyamat végén. Az utolsó lépés után válassza az **Új lépés lehetőséget.**

   Ha kapcsolóutasítást szeretne hozzáadni a lépések közé, vigye az egérmutatót azon a nyíl fölé, ahová a kapcsolóutasítást hozzá szeretné adni. Válassza ki a**+** megjelenő **pluszjelet** ( ), majd a **Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "switch" szót. Válassza a következő műveletet: **Switch - Control**

   ![Kapcsoló hozzáadása](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Egy kapcsolóutasítás jelenik meg egy és egy alapértelmezett esetlel. 
   Alapértelmezés szerint a kapcsolóutasításhoz legalább egy eset és az alapértelmezett eset szükséges. 

   ![Üres alapértelmezett kapcsolóutasítás](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Kattintson a **Be** mezőre, hogy megjelenjen a dinamikus tartalomlista. Ebből a listából válassza ki azt a **SelectedOption** mezőt, amelynek kimenete határozza meg a végrehajtandó műveletet. 

   ![Válassza a "SelectedOption" lehetőséget](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Azon esetek kezeléséhez, amikor `Approve` a `Reject`jóváhagyó a lehetőséget választja, vagy adjon hozzá egy másik esetet **az Eset** és **az Alapértelmezett**közé . 

   ![Másik eset hozzáadása](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adja hozzá ezeket a műveleteket a megfelelő esetekhez:

   | Esetben # | **Selectedoption (Kijelölt beállítás)** | Műveletek |
   |--------|--------------------|--------|
   | 1. eset | **Jóváhagyás** | Adja hozzá az Outlook **Küldése e-mailműveletet** az RSS-elem részleteinek elküldéséhez, ha a **jóváhagyó a Jóváhagyás**lehetőséget választotta. |
   | 2. sz. | **Elutasítás** | Adja hozzá az Outlook **Küldése e-mailműveletet,** ha értesítené a többi jóváhagyót az RSS-elem elutasításáról. |
   | Alapértelmezett | None | Nincs szükség beavatkozásra. Ebben a példában az **Alapértelmezett** eset üres, mert a **SelectedOption lehetőségnek** csak két lehetősége van. |
   |||

   ![Kész kapcsolóutasítás](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Mentse a logikai alkalmazást. 

   A példa manuális teszteléséhez válassza a **Futtatás** gombot, amíg a logikai alkalmazás meg nem talál egy új RSS-elemet, és küld egy jóváhagyási e-mailt. 
   Az eredmények megfigyeléséhez válassza a **Jóváhagyás** lehetőséget.

## <a name="json-definition"></a>JSON-definíció

Most, hogy létrehozott egy logikai alkalmazást egy kapcsolóutasítás használatával, nézzük meg a kapcsolóutasítás mögötti magas szintű kóddefiníciót.

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
| `"Switch"`         | A kapcsolóutasítás neve, amelyet átnevezhet az olvashatóság érdekében |
| `"type": "Switch"` | Itt adható meg, hogy a művelet kapcsolóutasítás-utasítás-e. |
| `"expression"`     | Ebben a példában megadja a jóváhagyó kiválasztott beállítását, amelyet a program a definíció későbbi részében később deklaráltként értékel ki az egyes estékre. |
| `"cases"` | Tetszőleges számú esetet határoz meg. Minden esetnél `"Case_*"` ez az eset alapértelmezett neve, amelyet az olvashatóság érdekében átnevezhet |
| `"case"` | Megadja az eset értékét, amelynek állandó és egyedi értéknek kell lennie, amelyet a kapcsolóutasítás az összehasonlításhoz használ. Ha egyetlen eset sem felel meg a `"default"` kapcsolókifejezés eredményének, a szakaszban lévő műveletek futnak. | 
| | | 

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciók vagy javaslatok elküldéséhez vagy szavazásához látogasson el az [Azure Logic Apps felhasználói visszajelzési webhelyére.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>További lépések

* [Feltételek (feltételes utasítások) alapján lépések futtatása](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása és ismétlése (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveletállapot (hatókörök) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
