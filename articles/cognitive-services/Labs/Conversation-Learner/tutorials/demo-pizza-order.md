---
title: Beszélgetés tanuló alkalmazás, pizzaszósz rendelés - Microsoft kognitív szolgáltatásokat bemutató |} Microsoft Docs
titleSuffix: Azure
description: Útmutató a bemutató beszélgetés tanuló alkalmazás létrehozásához.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348699"
---
# <a name="demo-pizza-order"></a>Bemutató: Pizzaszósz sorrendje
Ez a bemutató a rendezés botot pizzaszósz mutatja be. Ezzel a funkcióval egy egyetlen pizzaszósz sorrendje támogatja:

- a felhasználó utterances pizzaszósz toppings ismer fel
- Ha pizzaszósz toppings készleten, vagy nincs készleten ellenőrzése, és megfelelően válaszol
- pizzaszósz toppings egy előző sorrendje a jelszóelőzmények, és szeretné, hogy - új rendelés az azonos toppings ajánlat

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e a pizzaszósz rendelés botot

    npm run demo-pizza

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a TutorialDemo pizzaszósz sorrendje. 

## <a name="entities"></a>Entitások

Létrehoztunk három entitásokat.

- Toppings: felhalmozódnak a toppings a kér a felhasználótól. Ez magában foglalja a érvényes toppings a készlet. Azt ellenőrzi, hogy ha egy transzformátorok készlet kívül vagy belül van-e.
- OutofStock: Ez való kommunikációhoz használt azokat a felhasználónak, hogy a kijelölt transzformátorok nincs a készlet.
- LastToppings: sorrendben helyezkedik el, ha ehhez az entitáshoz használt biztosítson a felhasználó a sorrendnek toppings listája.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Műveletek

Létrehoztunk műveletekként kérni a felhasználó milyen kívánják a saját pizzaszósz közölve, mi hányszor adtak hozzá, amennyiben stb.

Van még két API-hívásokban:

- FinalizeOrder: a ahhoz, hogy a pizzaszósz elhelyezése
- UseLastToppings: a toppings át az előző sorrendje 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
Definiáltuk képzési párbeszédpanelek skálázhatja. 

![](../media/tutorial_pizza_dialogs.PNG)

Tegyük fel próbáljuk meg egy tanítási munkamenet.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg az "order egy pizzaszósz".
2. Kattintson a pontszám művelet.
3. Kattintással jelölje be "mit szeretne az a pizzaszósz?"
4. Adja meg a "gomba és sajt".
    - Figyelje meg, LUIS feliratú is, mint a Toppings. Ha, amely nem volt megfelelő, akkor sikerült kattintással jelölje ki, majd javítsa ki.
    - A "+" jel mellett az entitás azt jelenti, hogy éppen kerül toppings készletét.
5. Kattintson a pontszám műveletek.
    - Értesítés gomba és sajt nincsenek Toppings a memória.
3. Jelölje be a "rendelkezik $Toppings a pizzaszósz"
    - Figyelje meg, ez a egy nem várakozási művelet, így a botot rákérdez a következő művelet.
6. Válassza a "Kívánja, hogy bármi más?"
7. Adja meg a "gomba eltávolítása és paprika hozzáadása".
    - Értesítés **gomba** rendelkezik egy "-" bejelentkezési mellett azt is el kell távolítani. És paprika "+" toppings veheti fel.
2. Kattintson a pontszám művelet.
    - Értesítés **paprika** mostantól a félkövérrel szedett, ahogy új. És **gomba** küszöbértéke túllépve.
8. Jelölje be a "rendelkezik $Toppings a pizzaszósz"
6. Válassza a "Kívánja, hogy bármi más?"
7. Adja meg a "esetében hozzáadása".
    - Esetében a rendszer egy példa egy transzformátorok készlet kívül esik. Vegye figyelembe, hogy továbbra is lett címkézve, egy transzformátorok.
2. Kattintson a pontszám művelet.
    - Esetében OutOfStock jelenik meg.
    - Hogyan ez történt megtekintéséhez nyissa meg a kódot C:\<\installedpath > \src\demos\demoPizzaOrder.ts. És jegyezze fel a EntityDetectionCallback metódust. Ez a metódus után minden transzformátorok meg, ha a készlet neve. Ha nem, akkor azt törli azt a toppings készletét, és a OutOfStock entitást ad hozzá. A inStock változó Ez a módszer, amelynek a készlet toppings listája felett van definiálva.
6. Válassza ki a "Nem tudunk $OutOfStock".
7. Válassza a "Kívánja, hogy bármi más?"
8. "Nem" adja meg.
9. Kattintson a pontszám művelet.
10. Válassza ki a "FinalizeOrder" API-hívás. 
    - Ez a kódban a "FinalizeOrder" függvényt hívja. Ez törli a toppings, és visszaadja a "rendelés hamarosan megérkezik". 
2. Adja meg "egy másik order". Új rendelés indító azt.
9. Kattintson a pontszám művelet.
    - Megjegyzés: sajt és paprika van a memória, az utolsó rendelés toppings.
1. Válassza ki a "Kívánja, hogy $LastToppings".
2. Írja be az "Igen"
3. Kattintson a pontszám művelet.
    - A botot szabályozni akarja-e a UseLastToppings művelet. Ez a két visszahívási módszer közül a második. Emellett az utolsó rendelés toppings átmásolja toppings és az utolsó toppings törölje. Ez az utolsó rendelés jelszóelőzmények, és ha a felhasználó egy másik pizzaszósz kívánják, így ezek közül toppings.
2. Kattintással jelölje ki a "rendelkezik $Toppings a pizzaszósz".
3. Válassza a "Kívánja, hogy bármi más?"
8. "Nem" adja meg.
4. Kattintson a tanítási végezhető el.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató - VR app indítója](./demo-vr-app-launcher.md)
