---
title: A beszélgetés tanuló műveletek - Microsoft kognitív szolgáltatások "entitás várt" tulajdonságának használatával |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használja a "várt entitás" tulajdonságot a beszélgetés tanuló alkalmazás.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348655"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>A "Várt entitás" tulajdonság műveletek használata

Ez az oktatóanyag azt mutatja be, a műveletek a "várt entitás" mező.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek
A "entitás várt érték" mezőben egy művelet használatával kommunikáljon a rendszer a felhasználó válasz művelet lesz, hogy egy entitás várt.

Konkrétan, ha a "entitás várt érték" mezőben egy művelet $entity, majd meg a következő felhasználó utterance van beállítva, a rendszer lesz:

1. Először a szokásos módon, megkísérli megkeresni a gépi tanuláson alapuló entitás kibontási modellel entitások
2. Ha nincs entitások található 1. lépésben, majd – heurisztikát--, rendelje hozzá a teljes felhasználói utterance $entity.
3. Hívás `EntityDetectionCallback` a szokásos módon, és folytathatja a művelet kiválasztása.

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása ExpectedEntities. Majd kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Entitás nevét adjon meg nevet.
3. Kattintson a Létrehozás gombra

Vegye figyelembe a entitás típusa nem "egyéni" – Ez azt jelenti, hogy az entitás lehet szükség.  Előre elkészített entitások, ami azt jelenti, hogy azok viselkedését nem állítható be – ezekre vonatkozik-e egy másik oktatóprogram is vannak.

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Két tevékenység

1. Kattintson a műveletek, majd az új művelet
2. Válasz írja be a "Mi az a név?".
3. A várt entitások adja meg a $name. Kattintson a Mentés gombra.
    - Ez azt jelenti, hogy ha ezt a kérdést kapcsolatba, és a felhasználói válaszra nem rendelkezik a entitások észlelt, a botot kell fel, hogy a felhasználói válaszra teljes: ehhez az entitáshoz.
2. Kattintson a műveletek, majd új művelet egy második művelet létrehozásához.
3. A válasz írja be a "Hello $name".
    - Vegye figyelembe, hogy az entitás automatikusan hozzáadott disqualifying egységként. 
4. Kattintson a Save (Mentés) gombra.

Most, két műveletet kell végrehajtani.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Hello szövegrészt".
3. Kattintson a pontszám műveleteket, majd válassza a "Mi az a név?"
    - Vegye figyelembe, hogy a válasz "Hello $name" nem állítható be, mert az az entitás $name meghatározva requies és $name nincs botot a memória.
2. Adja meg a "david". 
    - Vegye figyelembe, hogy egy egységként ki van-e jelölve a neve. Ez egy, a beállítjuk fent jelölje be a válasz a egységként heurisztika miatt.
5. Kattintson a pontszám műveletek
    - Megjegyzés: név értéke most már a botot memóriában.
    - Válaszként a "Hello $name" most érhető el. 
6. Válassza ki a "Hello $name".
7. Kattintson a tanítási végezhető el.

Íme két példa arra, ahol a gépi tanulásra entitásmodell kinyerési nevét adja meg egy, így a "várt entitás" heurisztika indított nem.

1. Kattintson az új vonat párbeszédpanel.
2. Adja meg a "nevem david".
    - Vegye figyelembe, hogy azt azonosítása david a név egységként, mert azt észlelte, hogy a word előtt.
2. Kattintson a pontszám műveletek
3. Válassza ki a "Hello $name".
4. Adja meg a "nevem susan".
    - Vegye figyelembe, hogy azonosítja susan a nevét, mivel azt már tapasztalt ebben a mintában.
2. Kattintson a pontszám műveletek.
2. Válassza ki a "Hello susan".
3. Kattintson a tanítási végezhető el.

Itt két további példákat, ahol a "várt entitás" heurisztika váltja ki, de nem megfelelő, és hogyan tökéletesíthetjük javítás.

1. Adja meg "hívjon jose".
    - Vegye figyelembe, hogy azt nem ismeri fel a neve egy egységként.
2. Jose parancsára, és jelölje ki a nevét.
3. Kattintson a pontszám műveletek.
4. Válassza ki a hello $name.
5. Kattintson a tanítási végezhető el.
1. Kattintson az új vonat párbeszédpanel.
2. Adja meg a "Hello szövegrészt".
3. "Mi az a név" választ adja meg "Vagyok meghívtam frank".
    - Vegye figyelembe, hogy a teljes kifejezés ki van jelölve. Ennek az az oka a statisztikai modell nem talált egy nevet, a heurisztika következik be, és a teljes válasz választotta a név entitás.
2. Javítsa ki, kattintson a kiemelt kódot, majd kattintson a piros x. 
3. Jelölje be a frank, majd kattintson a nevére.
2. Kattintson a pontszám műveletek
3. Válassza ki a "Hello $name".
4. Kattintson a tanítási végezhető el.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Negálható entitások](./5-negatable-entities.md)
