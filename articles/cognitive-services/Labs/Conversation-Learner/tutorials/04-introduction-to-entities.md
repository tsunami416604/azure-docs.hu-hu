---
title: Az entitások használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatók Conversation Learner modellel rendelkező entitások.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: cba12b6c09c1bdbf4e8f7841676a609c34109d93
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707334"
---
# <a name="introduction-to-entities"></a>Az entitások bemutatása

Ez az oktatóanyag bemutatja az entitásokat, az entitásokat, a szükséges entitásokat és azok használatát Conversation Learneron belül.

## <a name="video"></a>Videó

[![Bevezetés az entitások oktatóanyagának előzetes verziójába](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Követelmények

Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek

Az entitások rögzítik azokat az információkat, amelyeket a robotnak el kell végeznie a feladat elvégzéséhez a felhasználói hosszúságú kimondott szöveg való kivonás vagy egyéni kód szerinti hozzárendelés útján. Az entitások maguk is korlátozhatja a beavatkozások rendelkezésre állását azáltal, hogy explicit módon "kötelező" vagy "nem megfelelő" minősítéssel vannak osztályozva.

- Ahhoz, hogy a művelet elérhető legyen, a szükséges entitásoknak jelen kell lenniük a modell memóriájában.
- A nem megfelelő entitások *nem* lehetnek jelen a modell memóriájában ahhoz, hogy a művelet elérhető legyen

Ez az oktatóanyag az egyéni entitásokra koncentrál. Az előre betanított, többértékű, megtagadható entitások és programozott entitások más oktatóanyagokban is elérhetők.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületen kattintson az "új modell" elemre.
2. A "név" mezőbe írja be a "IntroToEntities" kifejezést, és nyomja le az ENTER billentyűt.
3. Kattintson a "létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson az "entitások", majd az "új entitás" gombra.
2. Válassza a "Custom betanítva" lehetőséget az "entitás típusa" elemnél.
3. Írja be a "város" kifejezést az "entitás neve" értékre.
4. Kattintson a "létrehozás" gombra.

> [!NOTE]
> A "Custom betanított" entitás típusa azt jelenti, hogy ez az entitás kitanítható, más típusú entitásokkal ellentétben.

### <a name="create-the-actions"></a>A műveletek létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőben írja be a következőt: "nem tudom, hogy melyik várost szeretné."
3. A "nem feljogosító entitások" mezőben írja be a "City" kifejezést.
4. Kattintson a "létrehozás" gombra.

> [!NOTE]
> Ha a "város" entitást "az entitások kizárása" értékkel adja hozzá, akkor a robot által érintett "város" entitást a bot memóriájában kell megállapítania.

Most hozzon létre egy második műveletet.

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a következőt: "az időjárás a $city valószínűleg napos."
3. Kattintson a "létrehozás" gombra.

> [!NOTE]
> A "város" entitást a rendszer automatikusan felvette a szükséges entitások listájában a válaszra való hivatkozással.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>A modell betanítása

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hello" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést.
    - Ez szimulálja a beszélgetés felhasználójának oldalát.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ, "nem tudom, melyik várost szeretné."
5. Felhasználóként válaszoljon a "Seattle" kifejezésre.
6. Kattintson a "pontszám műveletek" gombra.
7. Válassza ki a választ, "az időjárás $city valószínűleg napos."
8. Kattintson a Save (Mentés) gombra.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várt entitás](./05-expected-entity.md)
