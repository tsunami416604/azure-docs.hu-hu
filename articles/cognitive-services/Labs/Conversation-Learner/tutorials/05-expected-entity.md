---
title: A "Várt entitás" tulajdonság a beszélgetés Learner műveletek – a Microsoft Cognitive Services használatával |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a "Várt entitás" tulajdonságot a beszélgetés Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226268"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>A "Várt entitás" tulajdonság a műveletek használata

Ez az oktatóanyag bemutatja a "Várt entitás" tulajdonság a műveletek.

## <a name="video"></a>Videó

[![Várt entitás oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
Egy művelet a "Várt entitás" tulajdonság használatával a felhasználó válaszát, ez a művelet az entitás mentéséhez.

Entitások hozzáadása egy művelet a "Várt entitás" tulajdonság, a rendszer fogja végrehajtani:

1. Első lépésként felel meg a machine learning segítségével entitásokat próbál alapú entitás kinyerési modell
2. A teljes felhasználói utterance (kifejezés) hozzárendelése $entity heurisztika alapján, ha nincsenek entitások találhatók
3. Hívás `EntityDetectionCallback`, és folytathatja a művelet kiválasztása.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "ExpectedEntities" és az enter.
3. A "Létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza ki a "Custom betanított" a "entitástípus."
3. Írja be a "name" a "entitás neve."
4. A "Létrehozás" gombra.

> [!NOTE]
> A "Custom betanított" entitástípus azt jelenti, hogy az entitás is vélik, ellentétben más típusú entitásokat.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Mi a neve?"
3. A "Várt entitások" mezőbe írja be a "name".
4. A "Létrehozás" gombra.

> [!NOTE]
> Entitások észlelt, és a felhasználói válaszra kinyert menti a rendszer a "name" entitást, ha ez a művelet akkor kell kiválasztani. Nincsenek entitások észlelése esetén a teljes választ menti az entitás.

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Hi $name!"
3. A "Létrehozás" gombra.

> [!NOTE]
> A "name" entitás a rendszer automatikusan felvette entitásokként egy"szükséges" hivatkozással a válaszban.

Most már két műveletet.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hi."
    - Ez szimulálja a beszélgetést a felhasználó oldalán.
3. A "Score műveletek" gombra.
4. Válassza ki a választ, "Mi a neve?"
    - A "Hi $name!" válasz nem lehet kiválasztani, mivel ez a válasz a "name" entitás kell definiálni a modell memória most van szükség.
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Frank". a típus
    - Egy entitás alapján a heurisztika beállítottuk a válasz mentse őket az entitás korábbi, "Frank" kiemelve.
6. A "Score műveletek" gombra.
    - A "name" entitás most típusúként van definiálva "Frank" memória a modell, tehát a "Hello $name" művelet választható műveletet.
7. Válassza ki a választ, a "Hi $name!"
8. A "Mentés" gombra.

A modell hozzáadása a további vonatok alternatív bemenetei között.

1. Az "Add alternatív beviteli..." mezőben írja be a "Vagyok Jose."
    - A modell nem ismeri fel a nevet egy egységként így kijelöli a teljes szöveges letiltása az entitás értékként
2. Kattintson a "Vagyok Jose" kifejezést, majd kattintson a Kuka ikonra.
3. Kattintson a "Jose", majd kattintson a "name" entitás listájából.
4. Kattintson a pontszám műveletek.
5. Válassza ki a választ, "Frank Üdvözöljük!"
6. A "Mentés" gombra.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Negálható entitások](./06-negatable-entities.md)
