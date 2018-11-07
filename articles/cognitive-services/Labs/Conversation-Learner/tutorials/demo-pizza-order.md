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
ms.openlocfilehash: e23ff60a0a2ea10ace09130ba115e72b4e1c9ad7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249812"
---
# <a name="demo-pizza-order"></a>Bemutató: Kétpizzás sorrend
Ez a bemutató a robot rendezése kétpizzás mutatja be. Ezzel a funkcióval egy egyetlen kétpizzás rendezése támogatja:

- FELISMERVE kétpizzás toppings a felhasználó kimondott szöveg
- ellenőrzi, hogy a készlet vagy készleten kívüli kétpizzás toppings vannak, és megfelelően válaszol
- kétpizzás toppings egy korábbi utasítás a megjegyzését, és szeretné, hogy – egy új rendelést az azonos toppings ajánlat

## <a name="video"></a>Videó

[![Bemutató Kétpizzás előzetes verzió](https://aka.ms/cl-demo-pizza-preview)](https://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a kétpizzás rendelés robotot

    npm run demo-pizza

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a TutorialDemo Kétpizzás sorrendben. 

## <a name="entities"></a>Entitások

Három entitások hozott létre.

- Toppings: ehhez az entitáshoz felhalmozódnak a toppings kéri a felhasználót. Ez magában foglalja a készleten lévő érvényes toppings. Azt ellenőrzi, hogy ha egy transzformátorok, vagy ki a készlet.
- OutofStock: ehhez az entitáshoz való kommunikációhoz a felhasználó számára, hogy a kiválasztott transzformátorok ne legyen készlet szolgál.
- LastToppings: egy rendelés történik, ha az entitás segítségével elérhetővé teheti a felhasználó a toppings sorrendjét a listája.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Műveletek

Létrehozott kérni a felhasználót, amit szeretnének a saját kétpizzás műveletek egy csoportját, létrehozóját arról, mit hozzáadott eddig, és így tovább.

Két API-hívások is van:

- FinalizeOrder: a sorrendjét a kétpizzás elhelyezése
- UseLastToppings: a toppings át az előző sorrendje 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
Minősített képzési párbeszédpanelek van definiálva. 

![](../media/tutorial_pizza_dialogs.PNG)

Tegyük fel próbáljon meg egy tanítási munkamenet.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg az "order kétpizzás egy'.
2. Kattintson a pontszám művelet.
3. Válassza ki a kattintson a "mit szeretne a a kétpizzás?"
4. Adja meg a "gomba és sajtok iránti szenvedélyének".
    - Figyelje meg, a LUIS feliratú Toppings mindkettőt. Ha, amely nem volt megfelelő, akkor sikerült kattintással jelölje ki a, majd javítsa ki.
    - A "+" jelre az entitás mellett, az azt jelenti, hogy azt ad hozzá a toppings készletét.
5. Kattintson a pontszám műveletek.
    - Figyelje meg `mushrooms` és `cheese` nem szerepelnek a for Toppings a memóriát.
3. Jelölje be a "nincs $Toppings a kétpizzás"
    - Figyelje meg, hogy ez a egy nem várakozási művelet, így a robot rákérdez a következő művelet.
6. Válassza a "Szeretné bármi más?"
7. Adja meg a "gomba eltávolítása és paprikát hozzáadása".
    - Figyelje meg `mushroom` rendelkezik egy "-" bejelentkezési mellette, ahhoz, hogy el kell távolítani. És `peppers` rendelkezik egy "+" jelre mellett, adja hozzá a toppings.
2. Kattintson a pontszám művelet.
    - Figyelje meg `peppers` most már a félkövér, mert jelent meg. És `mushrooms` küszöbértéke túllépve.
8. Jelölje be a "nincs $Toppings a kétpizzás"
6. Válassza a "Szeretné bármi más?"
7. Adja meg a "esetében hozzáadása".
    - `Peas` látható egy példa egy transzformátorok, amely kívül esik a készlet. Ez továbbra is egy transzformátorok kerülhetnek.
2. Kattintson a pontszám művelet.
    - `Peas` mint OutOfStock megjelenik-e.
    - Látható, hogy ez történt, nyissa meg a kódot `C:\<\installedpath>\src\demos\demoPizzaOrder.ts`. Tekintse meg a EntityDetectionCallback metódust. A metódus meghívása után minden transzformátorok megtekintéséhez, hogy a készleten. Ha nem, akkor azt törli a toppings készletéből, és hozzáadja a OutOfStock entitás. A inStock változó adott metódust, amely a készlet toppings listája szerepel felett van meghatározva.
6. Válassza ki a "Nincs $OutOfStock".
7. Válassza a "Szeretné bármi más?"
8. "Nem" adja meg.
9. Kattintson a pontszám művelet.
10. Válassza ki a "FinalizeOrder" API-hívás. 
    - Ez a kód meghatározott "FinalizeOrder" függvényt fogja hívni. Ez törli a toppings, és visszaadja a "a rendelés hamarosan megérkezik". 
2. Adja meg az "egy másik order". Mi indítja egy új rendelést.
9. Kattintson a pontszám művelet.
    - "sajtok iránti szenvedélyének" és "paprikát", az utolsó rendelés toppings vannak a memóriában.
1. Válassza ki a "Szeretné $LastToppings".
2. Írja be az "Igen"
3. Kattintson a pontszám művelet.
    - A robot a UseLastToppings műveletet szeretne. Ez a két visszahívási módszer közül a második. Ez az utolsó rendelés toppings toppings másolja, és töröljük az utolsó toppings. Ez az utolsó rendelés megjegyzését, és ha a felhasználó szerint, egy másik kétpizzás szeretnének, így ezek közül toppings módja.
2. Kattintással jelölje ki a "nincs $Toppings a kétpizzás".
3. Válassza a "Szeretné bármi más?"
8. "Nem" adja meg.
4. Kattintson a tanítási kész gombra.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató – VR appindítója](./demo-vr-app-launcher.md)
