---
title: "Feltételek hozzáadása, és indítsa el az Azure Logic Apps-munkafolyamatok – |} Microsoft Docs"
description: "Szabályozza, hogyan munkafolyamatok futnak, az Azure Logic Apps feltételes logikához, eseményindítók, műveletek és paraméterek hozzáadásával."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: estfan; LADocs
ms.openlocfilehash: cca13db916dc2560ff78d477a0faa241e032ab27
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2018
---
# <a name="use-logic-apps-features"></a>Logic Apps funkcióinak használata

Az egy [előző témakörben](../logic-apps/quickstart-create-first-logic-app-workflow.md), az első logikai alkalmazás. A Logic Apps alkalmazást munkafolyamat szabályozásához elérési útja eltér a logikai alkalmazás futtatásához, és hogyan kell feldolgozni az adatokat a tömböket, gyűjtemények és kötegek is megadhat. Ezeket az elemeket a logic app munkafolyamat alábbiakból állhat:

* Feltételek és [utasítások kapcsoló](../logic-apps/logic-apps-switch-case.md) lehetővé teszik a Logic Apps alkalmazást, hogy meghatározott feltételek alapján más műveletek futtatása.

* [Hurkok](../logic-apps/logic-apps-loops-and-scopes.md) lehetővé teszik a Logic Apps alkalmazást ismételten lépések futtatásával. Például megismétlésével műveletek keresztül tömb használatakor egy **For_each** hurok. Vagy a művelet megismétlésével egy feltétel teljesüléséig, használatakor egy **amíg** hurok.

* [Hatókörök](../logic-apps/logic-apps-loops-and-scopes.md) lehetővé csoportosítása műveletsorozattal együtt, például kivételkezelést végrehajtásához.

* [Debatching](../logic-apps/logic-apps-loops-and-scopes.md) lehetővé teszi, hogy a logikai alkalmazás indul elemek külön munkafolyamatok tömbben el a **SplitOn** parancsot.

Ez a témakör bemutatja a más fogalmak összeállítani saját logikai alkalmazását:

* Kód nézetre, és egy meglévő logikai alkalmazás szerkesztése
* Egy munkafolyamat beállítások

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Feltételek: Lépéseket csak egy feltétel teljesítése után futtassa.

Ahhoz, hogy a logikai alkalmazás lépések futtatásával csak akkor, amikor adatokat meghatározott feltételeknek, egy feltételt, amely összehasonlítja a munkafolyamat adott mezők vagy értékek adatokat is hozzáadhat.

Tegyük fel például, hogy egy webhely RSS-hírcsatorna a POST túl sok e-mailt küld logikai alkalmazás. Hozzáadhat egy feltétel, hogy a logikai alkalmazás e-mailt küld, csak akkor, ha az új post tartozik egy adott konkrét kategóriával.

1. Az a [Azure-portálon](https://portal.azure.com), található, és nyissa meg a Logic Apps alkalmazást a Logic App tervezőben.

2. Adja hozzá a munkafolyamat-helyet, amelyet egy feltételt. 

   A logic app munkafolyamat meglévő lépések között feltétel hozzáadásához húzza az egérmutatót a nyíl ahová adja hozzá a feltételt. 
   Válassza ki a **pluszjel** (**+**), majd válassza a **feltétel hozzáadása**. Példa:

   ![Feltétel hozzáadása a logikai alkalmazás](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Ha szeretné a jelenlegi munkafolyamatot végén feltétel hozzáadása, nyissa meg a logikai alkalmazás alján, és válassza ki **+ új lépés**.

3. Most adja meg a feltételnek. Adja meg a használni kívánt értékelheti ki, a művelet elvégzéséhez, és a célérték vagy mezőben mezőjének. A feltétel már meglévő mezők hozzáadása, választhat a **hozzáadása a dinamikus tartalom listába**.

   Példa:

   ![Alapszintű módban feltétel szerkesztése](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   A teljes feltétel a következő:

   ![Teljes feltétel](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > A kód a feltétel megadásához válassza **speciális módban szerkesztése**. Példa:
   > 
   > ![A kód feltétel szerkesztése](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. A **Ha Igen** és **IF nem**, vegye fel a feltétel teljesülését vizsgálja alapján végrehajtásához szükséges lépéseket.

   Példa:

   ![Igen, és nincsenek elérési utak feltételei](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > A meglévő műveletek áthúzhatja a **Ha Igen** és **IF nem** elérési utak.

5. Amikor elkészült, mentse a Logic Apps alkalmazást.

Most e-mailek lekérése, csak akkor, ha a bejegyzések felel meg a feltételnek.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Ismételje meg a műveletek során a forEach

A forEach ciklus határozza meg, hogy a művelet megismétléséhez keresztül tömb. Ha nem tömb, a folyamat sikertelen lesz. Például ha action1 üzenetek tömbjét adja kimenetként, amely rendelkezik, és szeretne minden üzenetet küldeni, is felvehet a forEach utasítás a művelet tulajdonságait: `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>A kód megadása a logikai alkalmazás szerkesztése

Bár a Logic App Designer, a kódot, amely meghatározza a logikai alkalmazás közvetlenül szerkesztheti.

1. A parancssávon válassza **nézet Code**.

    A teljes szerkesztő megnyílik, és a szerkesztett definíciója látható.

    ![Kódnézet](media/logic-apps-use-logic-app-features/codeview.png)

    A szövegszerkesztőben másolja, és illessze be a tetszőleges számú műveletet belül az azonos logikai alkalmazás vagy a logic Apps alkalmazások között. 
    Is egyszerűen adja hozzá vagy távolítsa el a teljes szakaszok meghatározásából, és másokkal is megoszthat definíciókat.

2. A módosítások mentéséhez válassza **mentése**.

## <a name="parameters"></a>Paraméterek

Egyes Logic Apps tulajdonságai csak a kód nézetre, például a paraméterek érhetők el. Paraméterek könnyen újra felhasználhatja a Logic Apps alkalmazást értékekkel. Például ha egy e-mail címet, amelyet számos műveletet használja, meg kell határozni, hogy e-mail cím paraméterként.

A paraméterekre ideális húzza ki értékeket, melyek az módosulnak. Különösen akkor hasznos, ha különböző környezetekben paraméterének kell. A környezet alapján paraméterének, lásd: [logikai alkalmazás definícióiról szerzői](../logic-apps/logic-apps-author-definitions.md) és [REST API-dokumentáció](https://docs.microsoft.com/rest/api/logic).

Ez a példa bemutatja, hogyan frissítse a meglévő Logic Apps alkalmazást úgy, hogy a paraméter használható a lekérdezési kifejezés.

1. A kód nézetre, keresse a `parameters : {}` objektumot, és adjon hozzá egy `currentFeedUrl` objektum:

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Lépjen a `When_a_feed-item_is_published` művelet található a `queries` szakaszt, és cserélje le a lekérdezés értékét: `"feedUrl": "#@{parameters('currentFeedUrl')}"` 

    Két vagy több karakterlánc csatlakozni is használhatja a `concat` függvény. 
    Például `"@concat('#',parameters('currentFeedUrl'))"` ugyanúgy működik, mint a fentiek közül.

3.  Ha elkészült, kattintson a **Mentés** gombra. 

    Most a webhely RSS-hírcsatorna úgy, hogy egy másik URL-címet keresztül bármikor módosíthatja a `currentFeedURL` objektum.

További információ [hogyan hozhatnak létre a logikai alkalmazás definícióiról](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Indítsa el a logic app munkafolyamatok

Elindítja a munkafolyamatot a Logic Apps alkalmazást meghatározott különböző lehetősége van. Egy munkafolyamat-igény szerinti mindig megkezdheti a [Azure-portálon].

### <a name="recurrence-triggers"></a>Ismétlődés eseményindítók

Ismétlődés eseményindító megadott időközönként fut. Az eseményindító feltételes logikához rendelkezik, a az eseményindító meghatározza, hogy a munkafolyamat futtatásához szükséges. Egy eseményindító azt jelzi, hogy a munkafolyamat futtatásának vissza egy `200` állapotkódot. Amikor a munkafolyamat nem kell futtatnia, adja meg az eseményindító egy `202` állapotkódot.

### <a name="callback-using-rest-apis"></a>A visszahívási REST API-k használatával

Indítani egy munkafolyamatot, a szolgáltatások meghívhatja a logic app végpont. Az ilyen logic app igény szerinti indításához válassza **futtatása most** a parancssávon. Lásd: [indítsa el a munkafolyamatok logic app végpontok eseményindítóként használható meghívásával](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[Azure-portálon]: https://portal.azure.com

## <a name="next-steps"></a>További lépések

* [Kapcsoló utasítások](../logic-apps/logic-apps-switch-case.md) 
* [Hurkok, hatókörök és kibontás](../logic-apps/logic-apps-loops-and-scopes.md)
* [Logikaialkalmazás-definíciók készítése](../logic-apps/logic-apps-author-definitions.md)