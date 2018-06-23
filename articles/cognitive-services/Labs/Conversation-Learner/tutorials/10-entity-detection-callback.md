---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások entitás észlelési visszahívási használata |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan entitás észlelési visszahívást beszélgetés tanuló alkalmazással.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348631"
---
# <a name="how-to-use-entity-detection-callback"></a>Entitás észlelési visszahívási használata

Ez az oktatóanyag az entitás észlelési visszahívási látható, illetve mutatja be egy közös mintát entitások megoldása.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e a "tutorialEntityDetectionCallback" bot.

    npm run tutorial-entity-detection

## <a name="details"></a>Részletek
Entitás észlelési visszahívási lehetővé teszi, hogy egyéni kód használatával entitások kapcsolódó üzleti szabályok kezelése. Ez a bemutató a használjuk visszahívások és programozott entitások megoldásához kanonikus nevét – például, hogy a felhasználó által megadott városnév "a nagy apple" a "new york" megoldása.

### <a name="open-the-demo"></a>Nyissa meg a bemutató

Az alkalmazás listájában kattintson a oktatóanyag-10-EntityDetenctionCallback. 

### <a name="entities"></a>Entitások

Három entitások definiáltuk az alkalmazásban.

![](../media/tutorial10_entities.PNG)

1. Cityben egy egyéni entitás, amely a felhasználó átadja szöveg bemenetként.
2. CityUnknown a programozott entitás. Ez a a rendszer feltölti beolvasása. A felhasználó által megadott azt fogja másolni, ha a rendszer nem tudja, melyik városban van.
3. CityResolved a város, a rendszer tudja, hogy kapcsolatban. Ez lesz a város kanonikus nevet, például a nagy apple fel a "new york".

### <a name="actions"></a>Műveletek

Létrehoztunk három műveletet. 

![](../media/tutorial10_actions.PNG)

1. Az első művelete "mely Cityben van szüksége?"
2. A második pedig a "nem tudható, hogy a város, $CityUknown. Melyik városban folytatja? "
3. A harmadik pedig a "$City válaszából kiderült, és szeretnék, hogy $CityResolved feloldva."

### <a name="callback-code"></a>A visszahívási kódot

Vizsgáljuk meg a kódot. A EntityDetectionCallback metódus található a C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts fájlt.

![](../media/tutorial10_callbackcode.PNG)

Ez a függvény meghívása megtörténik, entitás feloldása után.
 
- A legfontosabb fog tenni egyértelmű $CityUknown. $CityUknown csak megmaradnak az egyetlen menetben, hogy mindig lekérdezi bejelölve elején.
- Ezt követően azt listájának elismert város tartozik. Az elsőt igénybe vehet, és próbálja meg oldható meg.
- A függvény, amely feloldja az (resolveCity) van meghatározva további fent a kódban. Canonical város neveinek listáját rendelkezik. A város nevét a listában, akkor azt adja vissza. Más esetben "cityMap" jelenjenek meg, és a csatlakoztatott nevét adja vissza. Ha nem találja a város, hogy null értéket ad vissza.
- Végezetül a város megoldotta a nevét, ha tároljuk azt $CityKnown entitásban. Más esetben törölje, mi a felhasználó már rendelkezik, és $CityUknown entitás feltöltéséhez.

### <a name="train-dialogs"></a>Vonat párbeszédpanelek

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "mely Cityben van szüksége?"
2. Adja meg a "new york".
    - Vegye figyelembe, hogy lekérdezi-e felismerhető város egységként.
5. Kattintson a pontszám műveletek
    - Vegye figyelembe, hogy város és CityResolved vannak-e töltve.
6. Válassza ki a "$City válaszából kiderült, és szeretnék, hogy $CityResolved feloldása".
7. Kattintson a tanítási végezhető el.

Adja hozzá egy másik példa párbeszédpanel:

1. Kattintson az új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "mely Cityben van szüksége?"
2. Adja meg a "nagy apple".
    - Vegye figyelembe, hogy lekérdezi-e felismerhető város egységként.
5. Kattintson a pontszám műveletek
    - Vegye figyelembe, hogy CityResolved mutatja futó kód.
6. Válassza ki a "$City válaszából kiderült, és szeretnék, hogy $CityResolved feloldása".
7. Kattintson a tanítási végezhető el.

Adja hozzá egy másik példa párbeszédpanel:

1. Kattintson az új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "mely Cityben van szüksége?"
2. Adja meg a "PEL".
    - Itt látható egy példa a város, a rendszer nem tudja. 
5. Kattintson a pontszám műveletek
6. Jelölje ki "a város, $CityUknown nem tudom. Melyik városban folytatja?'.
7. Adja meg a "new york".
8. Kattintson a pontszám műveletek.
    - Vegye figyelembe, hogy CityUknown törölve lett, és CityResolved fel van töltve.
6. Válassza ki a "$City válaszából kiderült, és szeretnék, hogy $CityResolved feloldása".
7. Kattintson a tanítási végezhető el.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Munkamenet-visszahívások](./11-session-callbacks.md)
