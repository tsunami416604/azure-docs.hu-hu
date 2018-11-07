---
title: Előre összeállított entitások hozzáadása egy Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan előre összeállított entitások hozzáadása Beszélgetéstanuló modellhez.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2dbbf2a47cdc4240e5b0ba38658a4cb8d5307ff8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260057"
---
# <a name="how-to-add-pre-built-entities"></a>Előre összeállított entitások hozzáadása
Ez az oktatóanyag bemutatja, hogyan "beépített" entitásokat ad hozzá a Beszélgetéstanuló modell.

## <a name="video"></a>Videó

[![Az oktatóanyag 7 előzetes verzió](https://aka.ms/cl-tutorial-07-preview)](https://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

Előre összeállított entitások ismeri fel az általános típusú entitások, például a számokat, dátumokat, pénzügyi összegek és mások.  Egyéni entitásokat, eltérően, "out-of-a-box" működik, így nincs szükség képzést.  Egyéni entitásokat, ellentétben a viselkedésük nem lehet módosítani.  Alapértelmezés szerint előre összeállított entitások többértékű – a bot memória azt jelenti, hogyan gyűlnek az entitás minden egyes azonosított példány.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a BuiltInEntities. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Kattintson a legördülő EntityType, és válassza a datetimev2.
    - Programozható és Negatable beállítások le vannak tiltva, mert előre hozhat létre entitásokat nem vonatkoznak.
3. Kattintson a Létrehozás gombra.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Hozzon létre két műveletet

1. Kattintson a műveletek, majd az új művelet
2. A válasz, írja be "a dátum az $luis-datetimev2".
3. Kattintson a Létrehozás gombra.

![](../media/tutorial7_actions.PNG)

Ezután hozzon létre a második műveletet:

1. Kattintson a műveletek, majd új műveletet egy másik művelet létrehozása.
3. A válasz írja be a "Mi az a dátum?".
4. Adja meg "a luis-datetimev2" kizárásának entitásokat.
4. Kattintson a Létrehozás gombra

![](../media/tutorial7_actions2.PNG)

Most már két műveletet.

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "Mi az a dátum?"
2. Adja meg a "ma". 
    - Figyelje meg még ma van megjelölve, és megjelenik a második sorban, mivel ez egy előre elkészített entitás, és nem szerkeszthető.
5. Kattintson a pontszám műveletek
    - Figyelje meg, hogy most már a dátum entitás memória szakaszban jelenik meg. 
    - Ha az egérmutatót a dátumot, a LUIS, amely használható által biztosított további adatokat fog látni, és további kezelhetők a kódban. 
6. Válassza "a dátum az $luis-datetimev2".
7. Kattintson a kész tanítás

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alternatív bemenetek](./8-alternative-inputs.md)
