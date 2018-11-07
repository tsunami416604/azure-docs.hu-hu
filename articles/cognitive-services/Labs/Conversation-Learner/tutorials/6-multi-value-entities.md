---
title: Többértékű entitások használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja a többértékű entitásokat Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9ef86ea728e197540180d486dc1cb171f78a2590
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229458"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Beszélgetéstanuló modell többértékű entitások használata
Ez az oktatóanyag bemutatja az entitások "több érték" tulajdonsága.

## <a name="video"></a>Videó

[![Az oktatóanyag 6 előzetes verzió](https://aka.ms/cl-tutorial-06-preview)](https://aka.ms/blis-tutorial-06)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
Egy entitás, amely "több érték" értékek listáját ahelyett, hogy egyetlen érték tárolása összegzi.  Ez hasznos az entitások ahol a felhasználó úgy adhat meg egynél több értéket, például az egy kétpizzás toppings.

Konkrétan Ha egy entitás "többértékű" van megjelölve, majd minden egyes ismeri az entitás példányához hozzá lesznek fűzve egy listát a bot memória (helyett egyetlen entitás érték felülírása).

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a MultiValueEntities. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Az entitás nevét adja meg a Toppings.
3. A jelölőnégyzet többértékű adatelemeket.
    - Többértékű entitások gyűlnek az entitás egy vagy több értéket.
2. A jelölőnégyzet negálható.  
    - Ez lehetővé teszi a felhasználó eltávolítása toppings halmozott kétpizzás toppings listájából.
3. Kattintson a Létrehozás gombra.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Hozzon létre két műveletet

1. Kattintson a műveletek, majd az új művelet
2. A válasz írja be a "milyen toppings szeretne?".
3. Adja meg Toppings kizárásának entitásokat.
3. Kattintson a Létrehozás gombra

Ezután hozzon létre a második műveletet.

1. Kattintson a műveletek, majd új műveletet egy másik művelet létrehozása.
3. A válasz, írja be "az alábbiakban a toppings: $Toppings".
4. Kattintson a Létrehozás gombra

Most már két műveletet.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "milyen toppings szeretne?"
2. Adja meg a "gomba és sajtok iránti szenvedélyének". 
    - A címkézés nulla, egy vagy több, mint az entitások egyike.
3. Kattintson a "gomba", és válassza ki a Toppings.
4. Kattintson a "sajtok iránti szenvedélyének", és válassza ki a Toppings.
5. Kattintson a pontszám műveletek
    - A két érték már szerepelnek a Toppings entitás. 
6. Jelölje be "az alábbiakban a toppings: $Toppings".

Hozzáadható több ezt:

7. Adja meg a "paprikát hozzáadása".
    - Kattintson a "paprikát" entitás észlelése alatt, és válassza ki a Toppings.
3. Kattintson a pontszám műveletek.
    - "paprikát" most már megjelenik-e egy további Toppings értékként.
6. Jelölje be "az alábbiakban a toppings: $Toppings".

Most egy transzformátorok távolítsa el, és vegyen fel egy:

2. Írja be a "paprikát eltávolítása és kolbászt hozzáadása".
1. Kattintson a "paprikát", majd kattintson a távolítsa el a piros x.
2. Kattintson a "paprikát", és válassza ki a(z)-Toppings'.
3. Kattintson a pontszám műveletek.
    - "paprikát" törölve lett, és a "kolbászt" hozzá lett adva.
6. Jelölje be "az alábbiakban a toppings: $Toppings".

Most próbáljuk ki minden eltávolítása:

6. Adja meg a "gomba eltávolítása, távolítsa el a sajtok iránti szenvedélyének és kolbászt eltávolítása".
7. Kattintson a három mindegyike, majd válassza ki a(z)-Toppings'.
7. Kattintson a pontszám műveletek.
    - Az összes toppings törlődnek.
2. Válassza a "milyen toppings szeretne?"
3. Kattintson a kész tanítás

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beépített entitásokat](./7-built-in-entities.md)
