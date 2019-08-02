---
title: Bemutató Conversation Learner modell, pizza Order-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre bemutató Conversation Learner modellt.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703419"
---
# <a name="demo-pizza-order"></a>Bemutató: Pizza-sorrend
Ez a bemutató egy pizza-megrendelési robotot ábrázol, amely a következő módon támogatja az egyetlen Pizza-rendezést:

- a pizza-feltöltések felismerése a felhasználói hosszúságú kimondott szöveg
- a fehéráru-leltár és a megfelelő válasz kezelése
- az előző megrendelések megemlékezése és az azonos pizzák átrendezésének gyorsítása

## <a name="video"></a>Videó

[![Bemutató pizza – előzetes verzió](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy a pizza Order robot fusson

    npm run demo-pizza

### <a name="open-the-demo"></a>A bemutató megnyitása

A webes felhasználói felület modellek listájában kattintson a TutorialDemo pizza Order elemre. 

## <a name="entities"></a>Entitások

A modell három entitást tartalmaz:

- A "feltöltések" a felhasználó megadott Feltöltéseit gyűjti, ha vannak ilyenek.
- A "OutofStock" kifejezés jelzi a felhasználó számára, hogy a kijelölt feltöltés nem raktáron van
- A "LastToppings" az előző sorrendből származó korábbi előtéteket tartalmazza

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Műveletek

A modell olyan műveleteket tartalmaz, amelyek megkérik a felhasználót, hogy kiválassza a kiválasztását, a felhalmozott feltöltéseket és egyebeket.

Két API-hívás is elérhető:

- A "FinalizeOrder" kezeli a sorrend teljesítését
- A "UseLastToppings" feldolgozza a korábbi előtétek adatait

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Betanítási párbeszédpanelek

A modellben számos betanítási párbeszédablak található.

![](../media/tutorial_pizza_dialogs.PNG)

Egy másik betanítási párbeszédablak létrehozásával egy kicsit jobban betanítjuk a modellt.

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. A csevegési panelen írja be az "üzenet beírása..." kifejezést, írja be a "pizza a sajttal" kifejezést.
    - A "Cheese" szót a kivonó entitás kibontotta.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ, "Ön rendelkezik sajttal a pizzán."
5. Válassza ki a választ: "szeretne bármi más?"
6. A csevegési panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "gomb hozzáadása és Peppers"
7. Kattintson a "pontszám műveletek" gombra.
8. Válassza ki a választ: "a sajt, a gombák és a paprika a pizzán."
9. Válassza ki a választ: "szeretne bármi más?"
10. A csevegési panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "a paprika eltávolítása és a kolbász hozzáadása"
11. Kattintson a "pontszám műveletek" gombra.
12. Válassza ki a választ, "a sajtot, a gombákat és a kolbászt a pizzán."
13. Válassza ki a választ: "szeretne bármi más?"
14. A csevegés panelen írja be az "üzenet beírása..." kifejezést, írja be a "Yam hozzáadása" kifejezést.
15. Kattintson a "pontszám műveletek" gombra.
    - A "Yam" értéket hozzáadták az "OutofStock" értékhez az entitás észlelésének visszahívási kódja alapján, mivel a szöveg nem felelt meg a támogatott összetevőknek.
16. Válassza ki a választ, "OutOfStock"
17. Válassza ki a választ: "szeretne bármi más?"
18. A csevegési panelen írja be a következőt: "nem".
    - A "No" nincs megjelölve bármilyen típusú szándékként. Ehelyett az aktuális környezet alapján választjuk ki a megfelelő műveletet.
19. Kattintson a "pontszám műveletek" gombra.
20. Válassza ki a választ, "FinalizeOrder"
    - Ennek a műveletnek a kiválasztásával az ügyfél aktuális feltéteket az "LastToppings" entitásba mentettük, és a "fehéráru" entitás törlését a FinalizeOrder visszahívási kódjával.
21. A csevegési panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "Order Another"
22. Kattintson a "pontszám műveletek" gombra.
23. Válassza ki a választ, "szeretne sajtot, gombákat és kolbászt?"
    - Ez a művelet mostantól a beállított "LastToppings" entitás miatt érhető el.
24. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "yes"
25. Kattintson a "pontszám műveletek" gombra.
26. Válassza ki a választ, "UseLastToppings"
27. Válassza ki a választ, "a sajtot, a gombákat és a kolbászt a pizzán."
28. Válassza ki a választ: "szeretne bármi más?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Conversation Learner robot üzembe helyezése](../deploy-to-bf.md)
