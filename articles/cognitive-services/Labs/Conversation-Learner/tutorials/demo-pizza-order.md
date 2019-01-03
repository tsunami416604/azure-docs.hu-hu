---
title: Bemutató Beszélgetéstanuló modell, kétpizzás rendelés – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre egy bemutató Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9b35c0fd412dd48137a3cb362f20fae067c80461
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792628"
---
# <a name="demo-pizza-order"></a>Bemutató: Kétpizzás sorrend
Ez a bemutató-robot esetén egyetlen kétpizzás által rendezése támogató rendezése kétpizzás mutatja be:

- FELISMERVE kétpizzás toppings a felhasználó kimondott szöveg
- toppings készlet kezelése és válaszadás megfelelően
- előző rendelések megjegyzését, és lerövidíti az azonos kétpizzás rendelje újra

## <a name="video"></a>Videó

[![Bemutató Kétpizzás előzetes verzió](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a kétpizzás rendelés robotot

    npm run demo-pizza

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a TutorialDemo Kétpizzás sorrendben. 

## <a name="entities"></a>Entitások

A modell három entitásokat tartalmaz:

- "Toppings" összegzi az a felhasználó megadott toppings, ha elérhető.
- "OutofStock" jelzi a felhasználó a kiválasztott transzformátorok kívül esik a készlet
- "LastToppings" tartalmazza az előző sorrendjét a korábbi toppings

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Műveletek

A modell tartalmazza azon műveletek csoportja, amely megkérdezi a felhasználót, transzformátorok kijelölés, halmozott toppings és más rendszerekhez.

Két API-hívások is áll rendelkezésre:

- "FinalizeOrder" kezeli a megrendelések teljesítése
- "UseLastToppings" dolgozza fel a korábbi toppings információk

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek

Számos képzési párbeszédpanelek a modellben található.

![](../media/tutorial_pizza_dialogs.PNG)

Nézzük betanítja a modellt, hozzon létre egy másik betanításához párbeszédpanel kicsivel több.

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Rendeljen egy kétpizzás sajtok iránti szenvedélyének az" írja be
    - Az entitás információkinyerő által kinyert "sajtok iránti szenvedélyének" szót.
3. A "Score műveletek" gombra.
4. Válassza ki a választ, az "Vannak sajtok iránti szenvedélyének a kétpizzás."
5. Válassza ki a válasz "Szeretné bármi más?"
6. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "add gomba és paprikát" típusa
7. A "Score műveletek" gombra.
8. Válassza ki a választ, az "Vannak sajtok iránti szenvedélyének, gomba és paprikát a kétpizzás."
9. Válassza ki a válasz "Szeretné bármi más?"
10. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "paprikát távolítsa el, és adja hozzá a kolbászt" típusra
11. A "Score műveletek" gombra.
12. Válassza ki a választ, az "Vannak sajtok iránti szenvedélyének, gomba és kolbászt a kétpizzás."
13. Válassza ki a válasz "Szeretné bármi más?"
14. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "add yam" típusa
15. A "Score műveletek" gombra.
    - A "yam" érték lett felvéve "OutofStock" entitás észlelési visszahívási kóddal, a szöveg nem egyezik meg a bármely támogatott összetevők.
16. Válassza ki a választ, a "OutOfStock"
17. Válassza ki a válasz "Szeretné bármi más?"
18. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "no" típusra
    - A "nem" nem megjelölt szándék szerint bármilyen típusú. Ahelyett hogy lesz kell kiválasztja a megfelelő műveletet az aktuális környezet alapján.
19. A "Score műveletek" gombra.
20. Válassza ki a választ, a "FinalizeOrder"
    - Az ügyfél aktuális toppings első mentve a "LastToppings" entitás, és a "Toppings" entitás törlése a FinalizeOrder visszahívási kóddal eredményezett kiválasztja ezt a műveletet.
21. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "rendeljen egy másik" írja be
22. A "Score műveletek" gombra.
23. Válassza ki a válasz "Szeretné sajtok iránti szenvedélyének, gomba és kolbászt?"
    - Ez a művelet érhető el mostantól a "LastToppings" entitás beállítása miatt.
24. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", az "Igen" típusa
25. A "Score műveletek" gombra.
26. Válassza ki a választ, a "UseLastToppings"
27. Válassza ki a választ, az "Vannak sajtok iránti szenvedélyének, gomba és kolbászt a kétpizzás."
28. Válassza ki a válasz "Szeretné bármi más?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszélgetéstanuló robotprogramok üzembe helyezése](../deploy-to-bf.md)
