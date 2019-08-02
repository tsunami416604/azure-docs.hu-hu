---
title: Conversation Learner műveletek "várt entitás" tulajdonságának használata – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja egy Conversation Learner modell "várt entitás" tulajdonságát.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5fbe4f09d377b9f157368184ab26341782e9aed1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707318"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>A műveletek "várt entitás" tulajdonságának használata

Ez az oktatóanyag a műveletek "várt entitás" tulajdonságát mutatja be.

## <a name="video"></a>Videó

[![A várt entitás-oktatóanyag előzetes verziója](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek
Egy művelet "várt entitás" tulajdonságával mentse a felhasználó válaszát erre a műveletre egy entitásba.

Ha entitásokat ad hozzá egy művelet "várt entitás" tulajdonságához, a rendszer a következőt fogja:

1. Első lépésként próbálja meg egyeztetni az entitásokat a gépi tanuláson alapuló entitás-kinyerési modell használatával
2. A teljes felhasználói Kimondás kiosztása $entity a heurisztikus elemek alapján, ha nem találhatók entitások
3. Hívást `EntityDetectionCallback`, és folytassa a művelet kiválasztásával.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületen kattintson az "új modell" elemre.
2. A "név" mezőbe írja be a "ExpectedEntities" kifejezést, és nyomja le az ENTER billentyűt.
3. Kattintson a "létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson az "entitások", majd az "új entitás" gombra.
2. Válassza a "Custom betanítva" lehetőséget az "entitás típusa" elemnél.
3. Írja be a "Name" kifejezést az "entitás neve" értékre.
4. Kattintson a "létrehozás" gombra.

> [!NOTE]
> A "Custom betanított" entitás típusa azt jelenti, hogy ez az entitás kitanítható, más típusú entitásokkal ellentétben.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a "mi a neve?" kifejezést.
3. A "várt entitások" mezőben írja be a "név" kifejezést.
4. Kattintson a "létrehozás" gombra.

> [!NOTE]
> A felhasználó válaszában észlelt és kinyert entitásokat a rendszer a "név" entitásba menti, ha ezt a műveletet választja. Ha egyetlen entitás sincs észlelve, a rendszer a teljes választ menti az entitásba.

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a "Hi $name!" kifejezést.
3. Kattintson a "létrehozás" gombra.

> [!NOTE]
> A válaszban a "Name" entitást a rendszer automatikusan hozzáadta "kötelező entitásként".

Most már két művelettel rendelkezik.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>A modell betanítása

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hi" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést.
    - Ez szimulálja a beszélgetés felhasználójának oldalát.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ: "mi a neve?"
    - A "Hi $name!" a válasz nem jelölhető ki, mert ehhez a válaszhoz a modell memóriájában definiálni kell a "Name" entitást.
5. A csevegés panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "Frank".
    - A "Frank" ki van emelve egy entitásként a korábban beállított heurisztikus alapján, hogy mentse a választ entitásként.
6. Kattintson a "pontszám műveletek" gombra.
    - A "név" entitás most "Frank"-ként van definiálva a modell memóriájában, így a "Hello $name" művelet műveletként választható ki.
7. Válassza ki a választ, "Szia $name!"
8. Kattintson a Save (Mentés) gombra.

Alternatív bemenetek hozzáadása további vonatok a modellt.

1. Az "alternatív bemenet hozzáadása..." mezőjébe írja be az "I 'm Jose" kifejezést.
    - A modell nem ismeri fel a nevet entitásként, így a teljes szöveges blokkot kiválasztja az entitás értékeként.
2. Kattintson a "I 'm Jose" kifejezésre, majd kattintson a Kuka ikonra.
3. Kattintson a "Jose", majd a "név" elemre az entitások listájáról.
4. Kattintson a pontszám műveletek elemre.
5. Válassza ki a választ, "Szia Frank!"
6. Kattintson a Save (Mentés) gombra.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtagadható entitások](./06-negatable-entities.md)
