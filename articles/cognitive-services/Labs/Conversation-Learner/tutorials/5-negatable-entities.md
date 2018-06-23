---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások negálható entitások használata |} Microsoft Docs
titleSuffix: Azure
description: Útmutató negálható entitások beszélgetést tanuló alkalmazással.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348662"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>Negálható entitások beszélgetést tanuló alkalmazással használata

Ez az oktatóanyag azt mutatja be, az entitások "negálható" tulajdonság.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek
Egy művelet, mintha a "negálható" megjelölni, ha a felhasználó "törlése" egy entitás értékét, mint a "Nem, most nem kívánok $entity" vagy "nem, nem $entity." Például "nem, nem szeretném, hogy a Boston."

Konkrétan Ha az entitás "negálható" tulajdonság értéke:

- Ha entitás címkézés említi, engedélyezi, hogy egy entitás normál (pozitív) példányát, mind az entitás "negatív" példányai címke
- LUIS megtanulja két entitás modell: egy a példányok pozitív és negatív példányok egy második
- A hatását, hogy az entitás egy negatív példányát, hogy törölje ezt az értéket a entitásra változóból (ha van ilyen)

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása NegatableEntity. Majd kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Entitás nevét adjon meg nevet.
3. A jelölőnégyzet negálható.
    - Jelzi a felhasználó fog tudni az entitás adjon meg egy értéket, vagy mondja ki van valami *nem* az entitás értékét. Az utóbbi esetben ennek eredményeképpen, az az entitás megfelelő érték törlése.
3. Kattintson a Létrehozás gombra.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Két tevékenység

1. Kattintson a műveletek, majd az új művelet
2. Válasz írja be a "Nem tudom a neve".
3. A kizárásának entitások írja be.
3. Kattintson a Létrehozás gombra

Ezután hozzon létre a második műveletet.

1. Kattintson a műveletek, majd új művelet egy második művelet létrehozásához.
3. A válasz, írja be "tudható, hogy a neve. $Name ".
4. Kattintson a Létrehozás gombra

Most, két műveletet kell végrehajtani.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "Nem tudom a neve"
    - Figyelje meg, hogy már nehézséget 100 %-os pontszámot, mert az csak érvényes művelet.
2. Adja meg a "nevem david"
3. Válassza ki a "david", és válassza ki a címke "+ neve"
    - Figyelje meg a "name" két példánya van: "+ neve" és "-name".  Továbbá azt jelenti, hogy azt az értéket ad. Mínusz azt jelenti, hogy azt azt közli a rendszerrel, hogy valami nem értékét.
5. Kattintson a pontszám műveletek
    - Megjegyzés: név értéke most már a botot memóriában.
    - "Tudható, hogy a neve. $Name "csak akkor áll rendelkezésre a válasz. 
6. Jelölje ki "tudható, hogy a neve. $Name ".

Most törölje ki a negálható entitás:

7. Adja meg, "a név nincs david".
    - Figyelje meg "nem" választotta az előző minta alapján nevet. Amely nem megfelelő.
2. Kattintson a "nem", és a piros x. 
3. Kattintson a "david".
    - Ez már egy negatív entitás való kommunikációhoz, az érték nem az entitás neve.
2. Jelölje ki "-name".
3. Kattintson a pontszám műveletek.
    - Figyelje meg, az érték a memóriából törölve lett.
2. Jelölje ki a "Nem tudom a neve", amely az egyetlen művelet.

A következő megmutatjuk, hogyan döntéseit beírhatja a nevet az új értéket.

3. Adja meg "john" néven. Ezután válassza ki a "john", és kattintson a nevére.
4. Kattintson a pontszám műveletek.
5. Jelölje ki "tudható, hogy a neve. $Name ".

Próbálja meg a mag cseréje a megadott név.

6. Adja meg a "nevem susan".
7. Jelölje ki "tudható, hogy a neve. $Name ".
7. Kattintson a pontszám műveletek.
8. Értesítés **susan** felülírta **john** az entitás értékek.
9. Adja meg, "a név nincs susan".
    - Figyelje meg, a rendszer feliratú ez negatív-példányként.
2. Kattintson a pontszám műveletek.
3. Jelölje ki a "Nem tudom a neve", amely az egyetlen művelet.
7. Kattintson a tanítási végezhető el.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többértékű entitások](./6-multi-value-entities.md)
