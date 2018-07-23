---
title: Beszélgetéstanuló modell – a Microsoft Cognitive Services negálható entitások használata |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja negálható entitásokat Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2fd00d53755e44e3a3d86782c40aa6a53ff4d378
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171401"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Beszélgetéstanuló modell negálható entitások használata

Ez az oktatóanyag bemutatja az entitások "negálható" tulajdonságát.

## <a name="video"></a>Videó

[![Útmutató 5 előzetes verzió](http://aka.ms/cl-tutorial-05-preview)](http://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
Egy művelet, "negálható" minősítés, ha a felhasználó "törölheti" egy entitás érték, mint a "Nem, most nem kívánok $entity" vagy "nem, nem $entity." Például "nem, nem szeretném, hogy a Boston."

Konkrétan Ha egy entitás a "negálható" tulajdonság értéke:

- Entitás említései címkézés, ha lehetővé teszi a normál (pozitív) példányok az entitások, mind az entitás "negatív" példánya felirat
- A LUIS megtanulja a két entitás modell: egy pozitív példányok és a egy második negatív példányok
- Egy entitás egy negatív példány hatását, hogy ezt az értéket az entitás változó törlése (ha létezik)

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a NegatableEntity. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Az entitás nevét adja meg a nevét.
3. A jelölőnégyzet negálható.
    - Ez a tulajdonság jelzi a felhasználó fogja tudni adjon meg egy értéket az entitás vagy tegyük fel, valami *nem* az entitás értékét. Az utóbbi esetben ez eredményez az entitás megfelelő érték törlése.
3. Kattintson a Létrehozás gombra.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Hozzon létre két műveletet

1. Kattintson a műveletek, majd az új művelet
2. Válaszként írja be a "Nem tudom, a neve".
3. Entitások kizárásának adjon meg nevet.
3. Kattintson a Létrehozás gombra

Ezután hozzon létre a második műveletet.

1. Kattintson a műveletek, majd új műveletet egy másik művelet létrehozása.
3. A válasz, írja be "tudom, hogy a neve. Célszerű a $name ".
4. Kattintson a Létrehozás gombra

Most már két műveletet.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "hello".
3. Kattintson a pontszám műveletek, és válassza a "Nem tudom, a neve"
    - A pontszám: 100 %-os, az egyetlen érvényes művelet, mert.
2. Adja meg az "a nevem david"
3. Válassza ki a "david", és válassza ki a címke "+ nevezze el"
    - Két esetben a "name": "+ name" és "-neve".  (+) Plusz ad hozzá, vagy felülírja a értéket. (-) Mínuszjel eltávolítja az értéket.
5. Kattintson a pontszám műveletek
    - A név-érték már a robot a memóriában.
    - "Tudom, hogy a neve. Célszerű a $name "csak akkor érhető el a válasz. 
6. Jelölje be "tudom, hogy a neve. Célszerű a $name ".

Próbáljon meg a negálható entitás törlése:

7. Adja meg a "saját név nem david".
    - Figyelje meg "nem" van kiválasztva a korábbi minta alapján neveként. Ez a címke nem megfelelő.
2. Kattintson a "nem", majd a piros x. 
3. Kattintson a "david".
    - Ez mostantól egy negatív entitáshoz való kommunikációhoz, hogy ez nem a név entitás értékét.
2. Válassza ki a(z) – neve ".
3. Kattintson a pontszám műveletek.
    - Figyelje meg, hogy a memóriából törölve lett az értéket.
2. Jelölje ki a "Nem tudom, a neve", amely az egyetlen művelet.

Bemutatjuk, hogyan lehet megadni, egy új értéket a név a következő.

3. Adja meg a "János" nevet. Ezután válassza ki a "János", és kattintson a nevére.
4. Kattintson a pontszám műveletek.
5. Jelölje be "tudom, hogy a neve. Célszerű a $name ".

Próbálja meg és cserélje le a megadott név.

6. Adja meg a "a nevem susan".
7. Jelölje be "tudom, hogy a neve. Célszerű a $name ".
7. Kattintson a pontszám műveletek.
8. Figyelje meg **susan** felülírta **john** az entitás értékeket.
9. Adja meg a "saját név nem susan".
    - Figyelje meg, hogy a rendszer rendelkezik címkével ez negatív példányként.
2. Kattintson a pontszám műveletek.
3. Jelölje ki a "Nem tudom, a neve", amely az egyetlen művelet.
7. Kattintson a tanítási kész gombra.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többértékű entitások](./6-multi-value-entities.md)
