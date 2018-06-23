---
title: Várja meg, és nem várakozási műveletek használata a beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, várja meg, és nem várakozási műveletek használata a beszélgetés tanuló alkalmazás.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348582"
---
# <a name="wait-and-non-wait-actions"></a>Várjon, és nem várakozási műveletek

Ez az oktatóanyag a beszélgetés tanuló várakozási műveletek és nem várja meg műveletek közötti különbséget mutatja.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek

- Várjon, amíg a művelet: a rendszer végrehajtja a "Várakozás" műveletet, miután az leállítja műveletek végrehajtása és várnak felhasználói bevitelre.
- Nem-wait művelet: a rendszer végrehajtja a "nem várakozási" műveletet, miután azt azonnal lehetőség egy másik művelet (nélkül Várakozás a felhasználói inpu először).

## <a name="steps"></a>Lépések

### <a name="create-a-new-app"></a>Új alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása WaitNonWait. Majd kattintson a Létrehozás gombra.

### <a name="create-the-first-wait-action"></a>Az első várakozási művelet létrehozása

1. Kattintson a műveletek, majd új művelet.
2. Választ adja meg "mely állat van szüksége?".
    - Ez egy Várjon, amíg a művelet, így hagyja az be van jelölve, a válasz mezőbe való várakozás.
3. a Kész gombra.

### <a name="create-a-non-wait-action"></a>Nem-Wait művelet létrehozása

1. Kattintson az új művelet
2. Válasz írja be a "Tehenek tegyük fel például, moo".
3. Válasz jelölőnégyzet való várakozás UN-ellenőrzést.
4. Kattintson a Létrehozás gombra

### <a name="create-a-second-non-wait-action"></a>Hozzon létre egy második nem-Wait-művelet

1. Kattintson az új művelet
2. Válasz írja be a "Kacsa mondja ki quack".
3. Válasz jelölőnégyzet való várakozás UN-ellenőrzést.
4. Kattintson a Létrehozás gombra

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Típus: "hello"
3. Kattintson a pontszám műveleteket, majd válassza a "mely állat van szüksége?".
4. Adja meg a "vonatkozó"
5. Kattintson a pontszám műveleteket, majd válassza a "Tehenek tegyük fel például, moo".
    - Vegye figyelembe, hogy a botot bemeneti nem várakozik, és a következő műveletet nem hajt végre.
2. Válassza ki a "mely állat van szüksége?".
3. Adja meg a "kacsa"
5. Kattintson a pontszám műveleteket, majd válassza a "Kacsa mondja ki quack".

![](../media/tutorial2_dialogs.PNG)

Vegye figyelembe a botot válaszok jelenítik várja meg, és nem várakozási műveletek sorrendjét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az entitások bemutatása](./3-introduction-to-entities.md)
