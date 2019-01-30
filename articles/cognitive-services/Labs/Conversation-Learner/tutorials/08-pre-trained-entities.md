---
title: A beszélgetés Learner modell – a Microsoft Cognitive Services Pre-Trained entitások hozzáadása |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan Pre-trained entitásokat ad hozzá a beszélgetés Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: afa927009e684fa7f8c6217c91dcb589b331b5f5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224173"
---
# <a name="how-to-add-pre-trained-entities"></a>Pre-trained entitások hozzáadása
Ez az oktatóanyag bemutatja, hogyan Pre-Trained entitásokat ad hozzá a beszélgetés Learner modell.

## <a name="video"></a>Videó

[![Előre betanított entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

Előre betanított entitások felismerése közös típusú entitások, például a számokat, dátumokat, pénzügyi összegek és mások.  Működnek-e "-a-beépített," nem igényelnek képzést, és eltérően az egyéni entitások nem lehet módosítani a felhasználók viselkedését.  Alapértelmezés szerint a Pre-Trained entitások többértékű adatelemeket, az entitás minden egyes azonosított példány gyűlik.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "PretrainedEntities" és az enter.
3. A "Létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza ki a "Előtti-Trained/datetimeV2" a "entitástípus."
3. Jelölje be a "Többértékű" jelölőnégyzetet.
    - Többértékű entitások gyűlnek az entitás egy vagy több értéket.
    - Negálható tulajdonságok Pre-Trained entitások le vannak tiltva.
4. A "Létrehozás" gombra.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "a dátum az $builtin-datetimev2"
3. A "Létrehozás" gombra.

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Mi az a dátum?"
    - Előre betanított entitások nem lehet entitások szükséges, mert azokat az összes felhasználó utterances alapértelmezés szerint ismerhető fel.
3. A "Kizárásának feljogosítja a" mezőjében írja be a "beépített-datetimev2."
4. A "Létrehozás" gombra.

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hello".
3. A "Score műveletek" gombra.
4. Válassza ki a választ, "Mi az a dátum?"
5. A Csevegés panelen, ahol ugyanakkor "Típust az üzenetet...", típus a "ma"
    - A mai utterance (kifejezés) automatikusan felismeri a LUIS előre betanított modellek.
    - LUIS által biztosított további adatok felett Pre-Trained entitások értékét jeleníti meg.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás feloldók](./09-entity-resolvers.md)
