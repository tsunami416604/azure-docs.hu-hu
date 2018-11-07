---
title: Entitás észlelési visszahívási használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: 'Útmutató: entitás észlelési visszahívást Beszélgetéstanuló modell.'
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e9a3b0a2be58313266b949b907e4eb49a318a6dc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260074"
---
# <a name="how-to-use-entity-detection-callback"></a>Entitás észlelési visszahívási használata

Ez az oktatóanyag bemutatja az entitás észlelési visszahívási, és entitások feloldására vonatkozó gyakori minta mutatja be.

## <a name="video"></a>Videó

[![Az oktatóanyag 10-es előzetes verzió](https://aka.ms/cl-tutorial-10-preview)](https://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, amely a `tutorialEntityDetectionCallback` bot fut-e.

    npm run tutorial-entity-detection

## <a name="details"></a>Részletek
Entitás észlelési visszahívási lehetővé teszi, hogy egyéni kódot használó üzleti szabályok entitásokkal kapcsolatos kezelésére. Ez a bemutató visszahívások és szoftveres entitások használatával feloldani a "a big Data típusú apple", "Győr" feloldása egy canonical nevét – például, hogy a felhasználó által megadott város nevét.

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A modell-lista kattintson az oktatóanyag-10-EntityDetectionCallback. 

### <a name="entities"></a>Entitások

Három az entitások határozzák meg a modellben.

![](../media/tutorial10_entities.PNG)

1. Város egy egyéni entitást, amely a felhasználó szöveges bemenetként biztosít.
2. CityUnknown programozott entitás. Ehhez az entitáshoz fog lekérése kitölti a rendszer. Ha a rendszer nem tudja, melyik város, másolja a felhasználói bevitel.
3. CityResolved, a rendszer ismert az városa. Ehhez az entitáshoz city kanonikusnév például rendszer feloldja a big Data típusú apple "Győr" lesz.

### <a name="actions"></a>Műveletek

Három műveletet a modell vannak definiálva.

![](../media/tutorial10_actions.PNG)

1. Az első beavatkozásra "melyik városban szeretne?"
2. A második pedig a "nem tudom, az városa $CityUknown. Melyik városban biztosan? "
3. A harmadik pedig a "$City mondtad, és szeretnék, hogy $CityResolved megoldott."

### <a name="callback-code"></a>A visszahívás kód

Nézzük meg a kódot. A EntityDetectionCallback metódus találhatja meg a C:\<installedpath > \src\demos\tutorialSessionCallbacks.ts fájlt.

![](../media/tutorial10_callbackcode.PNG)

Ez a függvény meghívása megtörténik az entitások megoldási végrehajtása után.
 
- Először is fog tenni egyértelmű $CityUknown. $CityUknown csak megmaradnak az egyetlen menetben, hogy mindig lekérdezi bejelölve elején.
- Ezt követően a városok elismert listájának beolvasása. Az első utótagcímkéjét igénybe, és próbálja meg oldható meg.
- A függvényt, amely feloldja az (resolveCity) definiált további fent a kódban. Canonical városnevet listáját rendelkezik. A város nevét a listában, akkor azt adja vissza. Más esetben jelenjenek meg "cityMap", és a csatlakoztatott nevét adja vissza. Ha nem talál egy város, hogy null értéket ad vissza.
- Végül ha az városa megoldotta a nevét, tároljuk, $CityKnown entitásban. Más esetben törölje, mi a felhasználó már rendelkezik, és töltse fel a $CityUknown entitás.

### <a name="train-dialogs"></a>Párbeszédpanelek betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "melyik városban szeretne?"
2. Adja meg a "new york".
    - A szöveg egy város entitás végfelhasználóként.
5. Kattintson a pontszám műveletek
    - `City` és `CityResolved` vannak-e töltve.
6. Válassza ki a "$City mondtad, és szeretnék, hogy $CityResolved megoldott".
7. Kattintson a tanítási kész gombra.

Adjon hozzá egy másik példa párbeszédpanel:

1. Kattintson az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "melyik városban szeretne?"
2. Adja meg a "big Data típusú apple".
    - A szöveg egy város entitás végfelhasználóként.
5. Kattintson a pontszám műveletek
    - `CityResolved` futó mutatja.
6. Válassza ki a "$City mondtad, és szeretnék, hogy $CityResolved megoldott".
7. Kattintson a tanítási kész gombra.

Adjon hozzá egy másik példa párbeszédpanel:

1. Kattintson az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "melyik városban szeretne?"
2. Adja meg a "foo".
    - Ez a példa egy város, a rendszer nem tudja. 
5. Kattintson a pontszám műveletek
6. Jelölje be "az városa $CityUknown nem tudom. Melyik városban biztosan? ".
7. Adja meg a "new york".
8. Kattintson a pontszám műveletek.
    - `CityUknown` törölték, és `CityResolved` fel van töltve.
6. Válassza ki a "$City mondtad, és szeretnék, hogy $CityResolved megoldott".
7. Kattintson a tanítási kész gombra.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Munkamenet-visszahívások](./11-session-callbacks.md)
