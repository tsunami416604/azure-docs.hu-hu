---
title: Entitások használata a beszélgetés Learner modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja az entitásokat a beszélgetés Learner modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4239cbfe369060516653145b04a2bcfa0882f20d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796818"
---
# <a name="introduction-to-entities"></a>Entitások bemutatása

Ez az oktatóanyag bemutatja a entitásokat, kizárásának entitások, szükséges entitások és azok Beszélgetéstanuló belüli használat.

## <a name="video"></a>Videó

[![Bevezetés az entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Követelmények

Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut.

    npm run tutorial-general

## <a name="details"></a>Részletek

Entitások, amelyek a robot kell elvégezni a feladatot, kinyerési felhasználói utterances vagy egyéni kód hozzárendelés keresztül információk rögzítése. Entitások maguk is kijelölhetjük művelet rendelkezésre állása által éppen explicit módon kell besorolni "Kötelező" vagy "Kizárásának."

- Szükséges entitások szerepelnie kell a modellt a memóriában ahhoz, hogy a művelet csak akkor érhető el
- Entitások kizárásának kell *nem* szerepelnie a modellben a memóriában ahhoz, hogy a művelet csak akkor érhető el

Ez az oktatóanyag az egyéni entitások összpontosít. Előre betanított, többértékű, negálható programozott alá tartozó alanyokra és jelennek meg a többi.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "IntroToEntities" és az enter.
3. A "Létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza ki a "Custom betanított" a "entitástípus."
3. Írja be a "city" a "entitás neve."
4. A "Létrehozás" gombra.

> [!NOTE]
> A "Custom betanított" entitástípus azt jelenti, hogy az entitás is vélik, ellentétben más típusú entitásokat.

### <a name="create-the-actions"></a>A műveletek létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Nem tudom, melyik városban kívánt."
3. A "Kizárásának entitások" mezőben írja be a "city".
4. A "Létrehozás" gombra.

> [!NOTE]
> Hozzáadása a "city" entitás "Kizárásának entitások" lenne, akkor diszkvalifikálhatjuk, ennek a műveletnek a robot szempont, ha a "city" entitás van definiálva a robot a memóriában.

Most hozzon létre egy második műveletet.

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "a $city az időjárás Ez valószínűleg napsütéses."
3. A "Létrehozás" gombra.

> [!NOTE]
> A "city" entitás hozzá lett adva automatikusan a szükséges entitásainak listája a válaszban hivatkozással.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hello".
    - Ez szimulálja a beszélgetést a felhasználó oldalán.
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "Nem tudom, melyik városban kívánt."
5. A felhasználó a "Seattle" válaszol.
6. A "Score műveletek" gombra.
7. Válassza ki a választ, a "$city az időjárás valószínűleg napsütéses."
8. A "Mentés" gombra.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várt entitás](./05-expected-entity.md)
