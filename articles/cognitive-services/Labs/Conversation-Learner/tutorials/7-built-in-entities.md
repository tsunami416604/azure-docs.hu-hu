---
title: Előre elkészített entitások felvétele a beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan előre elkészített entitások a beszélgetés tanuló az alkalmazáshoz való hozzáadásához.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f014464419bfac39a9e57e679fcd28a737e9ebdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349343"
---
# <a name="how-to-add-pre-built-entities"></a>Előre elkészített entitások hozzáadása
Ez az oktatóanyag bemutatja, hogyan "előzetesen elkészített" entitások a beszélgetés tanuló az alkalmazáshoz való hozzáadásához.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek

Előre elkészített entitások entitások, például a dátum, pénzügyi összegek, és másokkal közös típusú ismeri fel.  Egyéni entitások eltérően azok "out-of-az-box" működik, és nem igényelnek képzést.  Egyéni entitásokat, ellentétben a viselkedés nem módosítható.  Alapértelmezés szerint előre elkészített entitások többértékű – Ez azt jelenti, hogy a botot memória felhalmozódnak az entitás minden azonosított példányát.

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása BuiltInEntities. Majd kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Kattintson a legördülő lista EntityType, és válassza a datetimev2.
    - Programozható és Negatable beállítások le vannak tiltva, mert előzetesen összeállításához az entitások nem vonatkoznak.
3. Kattintson a Létrehozás gombra.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Két tevékenység

1. Kattintson a műveletek, majd az új művelet
2. A válasz, írja be "a dátum, $luis-datetimev2".
3. Kattintson a Létrehozás gombra.

![](../media/tutorial7_actions.PNG)

A második művelet majd létrehozása:

1. Kattintson a műveletek, majd új művelet egy második művelet létrehozásához.
3. Válasz írja be a "Mi az a dátum?".
4. A kizárásának entitások írja be a "luis-datetimev2".
4. Kattintson a Létrehozás gombra

![](../media/tutorial7_actions2.PNG)

Most, két műveletet kell végrehajtani.

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "Mi az a dátum?"
2. Adja meg a "ma". 
    - Ma van megjelölve, és megjelenik a második sorban, mivel ez egy előre elkészített entitás, és nem szerkeszthető.
5. Kattintson a pontszám műveletek
    - Figyelje meg, a dátum most megjelenik a entitás memóriaszakasz. 
    - Ha egér mozgatásakor át a dátumot, akkor jelenik meg a további adatok LUIS, amely használható által biztosított, és további kezelhetők a kódban. 
6. Jelölje ki "a dátum, $luis-datetimev2".
7. Kattintson a tanítási kész

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alternatív bemenetek](./8-alternative-inputs.md)
