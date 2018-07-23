---
title: Várja meg és nem várja meg műveletek használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a wait és nem várja meg műveleteket Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a8f7ccf79e750c9f3c21c25c50c3e275db7e4195
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173084"
---
# <a name="wait-and-non-wait-actions"></a>Várjon, és nem várakozási művelet

Ebben az oktatóanyagban a Beszélgetéstanuló várakozási műveletek és nem várja meg műveletek közötti különbséget mutatja.

## <a name="video"></a>Videó

[![Az oktatóanyag 2 előzetes verzió](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

- Várjon a művelet: Miután a rendszer a "wait" művelet vesz igénybe, azt műveletekkel leáll, és várnak felhasználói bevitelre.
- Nem-wait művelet: után a rendszer egy "nem-wait" művelet vesz igénybe, azt közvetlenül lehetőség egy másik műveletet (jóváhagyású felhasználói inpu először).

## <a name="steps"></a>Lépések

### <a name="create-a-new-model"></a>Új modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a WaitNonWait. Ezután kattintson a Létrehozás gombra.

### <a name="create-the-first-wait-action"></a>Az első várakozási művelet létrehozása

1. Kattintson a műveletek, majd az új művelet.
2. A választ adja meg a "melyik szolgáltatást szeretne?".
    - A várakozási művelet, ezért ne be van jelölve, a válasz mező várja.
3. Kattintson a Létrehozás gombra.

### <a name="create-a-non-wait-action"></a>Nem-Wait művelet létrehozása

1. Kattintson az új művelet
2. Válaszként írja be a "Tehenek tegyük fel, hogy moo".
3. A válasz a jelölőnégyzetet, várja meg, UN-ellenőrzése.
4. Kattintson a Létrehozás gombra

### <a name="create-a-second-non-wait-action"></a>Hozzon létre egy második nem-Wait művelet

1. Kattintson az új művelet
2. Válaszként írja be a "Kacsa tegyük fel, hogy quack".
3. A válasz a jelölőnégyzetet, várja meg, UN-ellenőrzése.
4. Kattintson a Létrehozás gombra

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be "hello"
3. Kattintson a pontszám műveletek, és válassza a "melyik szolgáltatást szeretne?".
4. Adja meg a vonatkozó"
5. Kattintson a pontszám műveletek, és válassza a "Tehenek tegyük fel, hogy moo".
    - A robot bemenet nem várakozik, és megteszi a következő lépéseket.
2. Válassza ki a "melyik szolgáltatást szeretne?".
3. Adja meg a "kacsa"
5. Kattintson a pontszám műveletek, és válassza a "Kacsa tegyük fel, hogy quack".

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> A robot válaszok várakozási és nem várja meg műveleteket érdemes sorrendjét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitások bemutatása](./3-introduction-to-entities.md)
