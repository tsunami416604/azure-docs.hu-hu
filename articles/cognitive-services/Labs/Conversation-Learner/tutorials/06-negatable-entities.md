---
title: A beszélgetés Learner modell – a Microsoft Cognitive Services negálható entitások használata |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja negálható entitásokat a beszélgetés Learner modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 34806803c50308a288007bf3bad129075ca8bc6d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796826"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>A beszélgetés Learner modell negálható entitások használata

Ez az oktatóanyag bemutatja az entitások "Negatable" tulajdonságát.

## <a name="video"></a>Videó

[![Negálható entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
A "Negatable" tulajdonság egy entitás lehetővé teszi mindkét normál (pozitív) címke és az entitás példánya negatív, alapján a pozitív és negatív modellek tanítása és törölni az értéket egy meglévő entitás. A "Negatable" tulajdonság beállítása rendelkező entitások a beszélgetés Leaner negálható entitásokat nevezzük.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "NegatableEntity" és az enter.
3. A "Létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza az "Egyéni" a "entitástípus."
3. Írja be a "name" a "entitás neve."
4. Jelölje be a "Negatable" jelölőnégyzetet.
    - Ez a tulajdonság lehetővé teszi, hogy a felhasználónak meg kell adnia egy entitás érték, vagy tegyük fel, valami *nem* egy entitás értéket. Az utóbbi esetben ez a megfelelő entitás érték törlése.
5. A "Létrehozás" gombra.

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Nem tudom a neve."
3. A "Kizárásának feljogosítja a" mezőbe írja be a "name".
4. A "Létrehozás" gombra.

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "tudom a neve. Ami $name."
3. A "Létrehozás" gombra.

> [!NOTE]
> A "name" entitás a rendszer automatikusan felvette entitásokként egy"szükséges" hivatkozással a válaszban.

Most már két műveletet.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hello".
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "Nem tudom a neve."
    - A PERCENTILIS 100 %-os megegyezik az egyetlen érvényes művelet a korlátok alapján.
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "a nevem Frank" típusra
6. Válassza ki a "Frank", és válassza ki a címke "+ nevezze el"
    - Két esetben a "name" entitás számára: "+ name" és "-name".  (+) Plusz ad hozzá, vagy felülírja a értéket. (-) Mínuszjel eltávolítja az értéket.
7. A "Score műveletek" gombra.
    - A "name" entitás most már van definiálva "Frank" a memória a modell, ezért a "tudom a neve. "Ami korábban $name" művelet érhető el.
8. Válassza ki a választ, az "tudom a neve. Ami $name."
9. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "saját név nem Frank."
10. Válassza ki a "Frank", és válassza ki a címke "-name"
    - Kiválasztjuk "-name" jelet az entitás aktuális értéke.
11. A "Score műveletek" gombra.
12. Válassza ki a választ, a "Nem tudom a neve."
13. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", írja be "a nevem Susan."
14. Válassza ki a "Susan", és válassza ki a címke "+ nevezze el"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többértékű entitások](./07-multi-value-entities.md)
