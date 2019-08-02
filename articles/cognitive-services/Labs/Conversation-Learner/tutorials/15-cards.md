---
title: Kártyák használata Conversation Learner modellel, 1. rész – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhat Conversation Learner modellel rendelkező kártyákat.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 426d7c8de29abeb88833e94962a7291a641702ac
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703750"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kártyák használata (2. rész)

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és használhat egy egyszerű kártyát a robotban.

> [!NOTE]
> Conversation Learner jelenleg arra vár, hogy a kártya definíciós fájljai a "cards" nevű könyvtárban legyenek, amely abban a könyvtárban található, ahol a robot elindult.

## <a name="video"></a>Videó

[![Kártyák – oktatóanyag – előzetes verzió](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek

A kártyák olyan felhasználói felületi elemek, amelyek lehetővé teszik, hogy a felhasználó kiválasszon egy lehetőséget a beszélgetésben. 

### <a name="open-the-demo"></a>A bemutató megnyitása

A webes felhasználói felületen kattintson az "oktatóanyagok importálása" elemre, és válassza ki a "tutorial-15-cards" nevű modellt.

### <a name="the-card"></a>A kártya

A kártya definíciója a következő helyen található: C:\<installedpath\>\src\cards\prompt.json.

A rendszer arra vár, hogy megtalálja a kártya definícióit ebben a "kártyák" könyvtárban.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Figyelje meg, hogy a "TextBlock" törzs és a (z) "{{Question}}" helyőrző szerepel a szövegmezőben.
> Két beküldési gomb és a beküldött szöveg látható.

### <a name="actions"></a>Műveletek

Három műveletet hoztunk létre. Ahogy az alábbiakban látható, az első művelet egy kártya.

![](../media/tutorial13_actions.PNG)

Lássuk, hogyan jött létre a kártya műveleti típusa:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> A kártya három különböző paramétert tartalmaz: a kérdéses bemenetet, az 1. és a 2. gombot. Ezek az elemek olyan sablon-referenciák a kártyán, ahol megadhatja a kérdést és a megfelelő válaszokat. Hivatkozhat és használhat entitásokat, valamint szöveg és entitások keverékét is.

A szem ikon a kártya megjelenését mutatja.

### <a name="practicing-creating-card-actions"></a>A Card-műveletek létrehozásának gyakorlása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. Válassza a "kártya" lehetőséget a "művelet típusa" elemnél.
3. Válassza a "kérdés" lehetőséget a "sablon" listából.
4. A "kérdés" mezőbe írja be az "ugrás balra vagy jobbra" kifejezést.
5. A "Button1" mezőbe írja be a "Left" kifejezést.
6. A "Button2" mezőbe írja be a "Right" kifejezést.
7. Kattintson a "Mégse" gombra.

### <a name="train-dialog-using-an-adaptive-card"></a>Betanítási párbeszédpanel adaptív kártyát használva

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hi" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ, "kérdés: kérdés: Ugrás balra vagy jobbra? "
    - A szem ikon használható a kártya előzetes megtekintésére
5. A csevegés panelen kattintson a bal oldali gombra a megjelenített parancssorban.
6. Kattintson a "pontszám műveletek" gombra.
7. Válassza ki a választ, "Left"
8. Kattintson a Save (Mentés) gombra.
9. Válassza ki a választ, "kérdés: kérdés: Ugrás balra vagy jobbra? "
10. A csevegés panelen kattintson a jobb gombbal a megjelenített parancssorban.
11. Kattintson a "pontszám műveletek" gombra.
12. Válassza ki a választ, "jobb"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hibrid robotok](./16-hybrid-bots.md)
