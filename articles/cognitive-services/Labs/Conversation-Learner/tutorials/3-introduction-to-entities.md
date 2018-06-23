---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások entitások használata |} Microsoft Docs
titleSuffix: Azure
description: Útmutató entitások beszélgetést tanuló alkalmazással.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348619"
---
# <a name="introduction-to-entities"></a>Az entitások bemutatása

Ez az oktatóanyag bemutatja a entitásokat, és bemutatja, hogyan használja a "Disqualifying entitások" és "Entitások kötelező" mezőket a műveletek.

## <a name="requirements"></a>Követelmények

Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek

Ez az oktatóanyag azt mutatja be két gyakori használati entitások.  Első lépésként entitások nyerhet ki karakterláncrészletek felhasználói üzenetből, például a város, az "Mi az budapesti időjárási?" azonosító.  Második entitásokat is megkötése, ha műveletek érhetők el.  Pontosabban a művelet folyamatban "kötelező" vagy "kizárásának" entitás készíthetünk:
- Egy művelet kötelező entitások a botot memória ahhoz, hogy a művelet csak akkor érhető el a jelen kell lennie.
- Entitások kizárásának kell *nem* megtalálható a botot memória ahhoz, hogy a művelet csak akkor érhető el

Egyéb oktatóanyagok egyéb entitások, például az előre elkészített entitások, többértékű negálható entitások, programozott entitásokat, és testreszabhatóvá entitásokat, illetve a kódban aspektusainak foglalkozik.

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása IntroToEntities. Majd kattintson a Létrehozás gombra.

### <a name="create-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Az entitás nevét adja meg a város.
3. Kattintson a Létrehozás gombra

Vegye figyelembe a entitás típusa nem "egyéni" – Ez azt jelenti, hogy az entitás lehet szükség.  Előre elkészített entitások, ami azt jelenti, hogy azok viselkedését nem állítható be – ezekre vonatkozik-e egy másik oktatóprogram is vannak.

### <a name="create-two-actions"></a>Két tevékenység

1. Kattintson a műveletek, majd az új művelet
2. Válasz írja be a "Nem tudom kívánt városban".
3. Entitások kizárásának $city meg. Kattintson a Mentés gombra.
    - Ez azt jelenti, hogy ha ezt az entitást botot tartozó memória van definiálva, majd a művelet hatására *nem* érhető el.
2. Kattintson a műveletek, majd új művelet egy második művelet létrehozásához.
3. Válasz írja be a "a a $city nem valószínűleg moziba".
4. Szükséges entitások jegyezze fel, hogy város entitás hozzáadta automatikusan óta elé.
5. Kattintson a Save (Mentés) gombra.

Most, két műveletet kell végrehajtani.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "Nem tudom kívánt városban?"
    - Vegye figyelembe, hogy a válasz, ahol szükség-e a város entitás nem választható, mert a város entitás nincs definiálva a botot a memória.
2. Válassza ki a "Nem tudom kívánt városban".
4. Adja meg a "seattle". Jelölje ki a budapesti, majd kattintson a város.
5. Kattintson a pontszám műveletek
    - Megjegyzés: Város értéke most már a botot memóriában.
    - "A $city időjárási is valószínűleg moziba" már elérhető válaszként. 
6. Válassza az "a $city időjárási valószínűleg moziba".

Tegyük fel a felhasználó megadja a "repeat". 
1. Írja be, és adja meg. Vegye figyelembe, hogy város entitás és az értékét a memóriában, és elérhető.
2. Válassza az "a $city időjárási valószínűleg moziba".

![](../media/tutorial3_entities.PNG)

Most létrehozott egy entitás és példányai azt a felhasználói üzenetek címkével.  Is használta az entitás jelenléte vagy hiánya szabályozhatja a botot memóriában Ha műveletek érhetők el, a művelet kizárásának és a szükséges entitások mezők keresztül.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várt](./4-expected-entity.md)
