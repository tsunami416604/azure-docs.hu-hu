---
title: A "várt entitás" tulajdonság Beszélgetéstanuló műveletek – a Microsoft Cognitive Services használatával |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a "várt entitás" tulajdonságot egy Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a43c52143f936eaefd4383714b1c67b6b74d34e8
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378669"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>A "Várt entitás" tulajdonság a műveletek használata

Ez az oktatóanyag bemutatja a "várt entitás" mező a műveletek.

## <a name="video"></a>Videó

[![Az oktatóanyag 4 előzetes verzió](http://aka.ms/cl-tutorial-04-preview)](http://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
A "várt entitás" mező egy művelet használatával kommunikáljon a rendszer a felhasználó választ egy műveletet, de egy entitás lesz várt.

Konkrétan Ha egy művelet a "várt entitás" mező értéke $entity, majd meg a következő felhasználó utterance (kifejezés), a rendszer fogja végrehajtani:

1. Először is megkísérli megkeresni a gépi tanuláson alapuló entitás kinyerési modellel entitások
2. Ha nincsenek entitások találhatók 1,. lépés – ezután heurisztikát – mint a teljes felhasználói utterance (kifejezés) hozzárendelése $entity.
3. Hívás `EntityDetectionCallback` a szokásos módon, és folytathatja a művelet kiválasztása.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a ExpectedEntities. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Az entitás nevét adja meg a nevét.
3. Kattintson a Létrehozás gombra

> [!NOTE]
> Entitás típusa: "egyéni". Ez az érték azt jelenti, hogy az entitás is vélik.  Még nincsenek előre összeállított entitások, ami azt jelenti, hogy a felhasználók viselkedését nem módosítható.  Ezek az entitások tárgyalja a [Pre-Built entitások oktatóanyag](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Hozzon létre két műveletet

1. Kattintson a műveletek, majd az új művelet.
2. A válasz írja be a "Mi a neve?".
3. Adja meg a várt entitásokat, $name. Kattintson a Mentés gombra.
    - Ez az érték azt jelenti, hogy meg kell adnia ezt a kérdést, és a felhasználói válaszra nem rendelkezik a entitások észlelt, a bot kell feltételezik, hogy az egész a felhasználói válaszra ehhez az entitáshoz.
2. Kattintson a műveletek, majd új műveletet egy másik művelet létrehozása.
3. A válasz írja be a "Hello $name".
    - Vegye figyelembe, hogy az entitás automatikusan hozzáadja a szükséges entitásként. 
4. Kattintson a Mentés gombra.

Most már két műveletet.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "Mi a neve?"
    - A "$name Hello" válasz nem választható, mert szükséges az entitás $name kell definiálni, és $name nem szerepel a robot memória.
2. Adja meg a "david". 
    - A név kiemelve jelenik meg egy egységként. Ez a miatt a heurisztika beállítjuk fölött, válassza ki a választ, az entitáshoz.
5. Kattintson a pontszám műveletek
    - A név-érték már a robot a memóriában.
    - "Hello $name" már elérhető adott válaszként. 
6. Válassza ki a "Hello $name".
7. Kattintson a tanítási kész gombra.

Az alábbiakban két példa, a gépi tanulási entitás kinyerési modell azonosítja a nevét, így a "várt entitás" heurisztika nem indul el attól.

1. Kattintson az új Train párbeszédpanel.
2. Adja meg a "a nevem david".
    - A modell a david a név entitásként azonosítja, mert azt észlelte, hogy a szó előtt.
2. Kattintson a pontszám műveletek
3. Válassza ki a "Hello $name".
4. Adja meg a "a nevem susan".
    - A modell a susan nevet azonosítja, mivel már látta, ezt a mintát.
2. Kattintson a pontszám műveletek.
2. Válassza ki a "Hello susan".
3. Kattintson a tanítási kész gombra.

A következő példákban a "várt entitás" heurisztika aktivál, de nem megfelelő. A példák majd bemutatják, hogyan javítás.

1. Írja be "hívást kérek jose".
    - A modell nem ismeri fel a nevet egy egységként.
2. Kattintson a jose, és válassza ki a nevét.
3. Kattintson a pontszám műveletek.
4. Válassza ki a hello $name.
5. Kattintson a tanítási kész gombra.
1. Kattintson az új Train párbeszédpanel.
2. Adja meg a "hello".
3. "Mi a neve" választ adja meg "Am meghívtam frank".
    - A teljes kifejezés ki van emelve. Ennek oka az, a statisztikai modell nem találta a nevét, így a heurisztika aktiválódott, és a teljes választ választotta a név entitás.
2. Javítsa ki, kattintson a kiemelt kifejezést, majd kattintson a a piros x. 
3. Jelölje be a frank, majd kattintson a nevére.
2. Kattintson a pontszám műveletek
3. Válassza ki a "Hello $name".
4. Kattintson a tanítási kész gombra.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Negálható entitások](./5-negatable-entities.md)
