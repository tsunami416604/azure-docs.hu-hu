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
ms.openlocfilehash: 95cf223004c50583701e65d3adf02f57bd541fae
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796800"
---
# <a name="wait-and-non-wait-actions"></a>Várjon, és nem várakozási művelet

Ebben az oktatóanyagban a Beszélgetéstanuló várakozási műveletek és nem várja meg műveletek közötti különbséget mutatja.

## <a name="video"></a>Videó

[![Várjon, és nem-Wait oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

- Várjon a művelet: Miután a rendszer a "wait" művelet vesz igénybe, azt műveleteket, leáll, és várnak felhasználói bevitelre.
- Nem-wait művelet: Miután a rendszer egy "nem-wait" művelet vesz igénybe, azt azonnal lehetőség egy másik művelet (Várakozás a felhasználói bevitel) nélkül.

## <a name="steps"></a>Lépések

### <a name="create-a-new-model"></a>Új modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A "Name" mezőbe írja be a "Várakozás nem-Wait", találati aránya adja meg, vagy a "Create" gombra.

### <a name="create-the-first-two-wait-actions"></a>Az első két várjon műveletek létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "milyen kétpizzás szeretné használni?".
    - Ez a várakozási művelet, ezért üresen hagyja a "Várakozás a Response" be van jelölve.
3. A "Létrehozás" gombra.
4. Ismételje meg ezeket a lépéseket, hozzon létre egy másik műveletet a "Kétpizzás von!" a robot válaszként.

### <a name="train-using-those-wait-actions"></a>Várjon műveletek használatával betanítása

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Hi" típusra. 
    - Ez szimulálja a beszélgetést a felhasználó oldalán.
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "milyen kétpizzás szeretné?".
5. A felhasználó válaszolni a "Margherita".
6. A "Score műveletek" gombra.
7. Válassza ki a választ, a "Kétpizzás von!".
8. A "Mentés" gombra.

### <a name="create-a-non-wait-action-while-training"></a>Képzés során nem várakozási művelet létrehozása
Bár nem-Wait művelet sikerült létrehozni, mint a korábban végrehajtott, is létrehozhat, a képzési munkameneten belül.
1. A "Train párbeszédpanel" gombra.
2. A felhasználó típusaként a "Hello".
3. A "Score műveletek" gombra.
4. Kattintson a "+ művelet" gombra. 
    - Ekkor a jól ismert (an Action létrehozása) párbeszédpanelt.
5. Írja be a robot válasz való, "Üdvözöljük Kétpizzás Bot!"
6. Ellenőrzés megszünteti a "Várakozás a Response" jelölőnégyzetet.
7. A "Létrehozás" gombra.
    - Figyelje meg, hogy a robot reagál, azonnal a "Üdvözöljük Kétpizzás Bot!" és szeretné újra egy másik Bot választ kér. Ennek az oka a robot válasz volt az imént létrehozott nem-Wait művelet.
9. Válassza ki a választ, a "milyen kétpizzás szeretné?".
10. A felhasználó válaszolni a "Margherita".
11. A "Score műveletek" gombra.
12. Válassza ki a választ, a "Kétpizzás von!".
13. A "Mentés" gombra.

> [!NOTE]
> A robot válaszok várakozási és nem várja meg műveleteket érdemes sorrendjét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitások bemutatása](./04-introduction-to-entities.md)
