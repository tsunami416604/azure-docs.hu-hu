---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások többértékű entitások használata |} Microsoft Docs
titleSuffix: Azure
description: Útmutató többértékű entitások beszélgetést tanuló alkalmazással.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 865b50747b2c9574b5f88d4902bea9e4c8e0e032
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348623"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-application"></a>Többértékű entitások beszélgetést tanuló alkalmazással használata
Ez az oktatóanyag bemutatja az entitások "több érték" tulajdonsága.

##<a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek
Egy entitás, amely "többértékű" értékek listáját, nem pedig egyetlen érték tárolása összegzi.  Ez akkor hasznos, entitások ahol a felhasználó egynél több érték, például egy pizzaszósz a toppings adhat meg.

Konkrétan Ha egy entitás "többértékű" van megjelölve, majd minden egyes felismerni listáját, amely a botot memória (nem pedig egy egyetlen entitás érték felülírása) hozzáfűzi a entitás példányához.

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása MultiValueEntities. Majd kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Az entitás nevét adja meg a Toppings.
3. A jelölőnégyzet többértékű adatelemeket.
    - Többértékű entitások gyűlik össze, hogy az entitás egy vagy több értéket.
2. A jelölőnégyzet negálható.  
    - Ez lehetővé teszi a felhasználó toppings eltávolítása a halmozott pizzaszósz toppings listája.
3. Kattintson a Létrehozás gombra.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Két tevékenység

1. Kattintson a műveletek, majd az új művelet
2. Válasz írja be a "milyen toppings van szüksége?".
3. Entitások kizárásának Toppings meg.
3. Kattintson a Létrehozás gombra

Ezután hozzon létre a második műveletet.

1. Kattintson a műveletek, majd új művelet egy második művelet létrehozásához.
3. A válasz, írja be "az alábbiakban a toppings: $Toppings".
4. Kattintson a Létrehozás gombra

Most, két műveletet kell végrehajtani.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "milyen toppings van szüksége?"
2. Adja meg a "gomba és sajt". 
    - A címkézés nulla, egy vagy több, mint egy entitást.
3. Kattintson a "gomba", és válassza ki a Toppings.
4. Kattintson a "sajt", és válassza a Toppings.
5. Kattintson a pontszám műveletek
    - Ne feledje, hogy a két érték már szerepel a Toppings entitás. 
6. Jelölje be "az alábbiakban a toppings: $Toppings".

A Microsoft adhat hozzá további ezt:

7. Adja meg a "paprika hozzáadása".
    - Kattintson a "paprika" entitás észlelése alatt, és válassza a Toppings.
3. Kattintson a pontszám műveletek.
    - Vegye figyelembe, hogy most paprika Toppings egy további értékként jeleníti meg.
6. Jelölje be "az alábbiakban a toppings: $Toppings".

Most egy transzformátorok eltávolítása, és vegyen fel egyet:

2. Írja be a "paprika eltávolítása és kolbászt hozzáadása".
1. Kattintson a "paprika", majd kattintson a piros x távolítsa el a.
2. Kattintson a "paprika", és jelölje ki "-Toppings'.
3. Kattintson a pontszám műveletek.
    - Ne feledje, hogy "paprika" törölt "kolbászt" hozzá lett adva.
6. Jelölje be "az alábbiakban a toppings: $Toppings".

Most tegyük távolítsa el minden:

6. Adja meg a "gomba eltávolítása, távolítsa el a sajt, és távolítsa el a kolbászt".
7. Kattintson a három egyes, és válassza ki "-Toppings'.
7. Kattintson a pontszám műveletek.
    - Vegye figyelembe, hogy minden toppings adatútvonalai törlődtek.
2. Válassza a "milyen toppings van szüksége?"
3. Kattintson a tanítási kész

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beépített entitások](./7-built-in-entities.md)
