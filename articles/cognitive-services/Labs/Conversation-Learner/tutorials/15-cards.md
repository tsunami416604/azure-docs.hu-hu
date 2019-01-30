---
title: Hogyan használja a kártyák Beszélgetéstanuló modell, 1. rész – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a kártyák egy Beszélgetéstanuló modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a2db74e4c5688c0a2a3975ff828caf20c3cf7904
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222576"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Kártya (2 1. rész) használata

Ez az oktatóanyag bemutatja, hogyan adhat hozzá, és a egy egyszerű kártya használata a robot.

> [!NOTE]
> Beszélgetéstanuló jelenleg vár a kártya definíciós fájlokat, és egy "kártyák" nevű könyvtárat, amely megtalálható a címtárban található, a Bot elindult.

## <a name="video"></a>Videó

[![Kártyák oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

Kártyák a felhasználói felületi elemeket, amelyek lehetővé teszik a felhasználó kiválaszthatja a beállítást a beszélgetésben. 

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen kattintson a "Oktatóanyagokban importálása", és válassza a "Az oktatóanyag-15-kártyák" nevű modell.

### <a name="the-card"></a>A kártya

A kártya definíciója van a következő helyen: C:\<installedpath\>\src\cards\prompt.json.

A rendszer vár, a kártya-definíciók keresése a "kártyák" könyvtárban található.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Figyelje meg, hogy a szervezet, írja be a "TextBlock" és a "{{kérdés}}" helyőrzőt a szövegmezőben.
> Nincsenek a két elküldéséhez, gombok és a szöveg, amelyben az egyes elküldött beolvasása.

### <a name="actions"></a>Műveletek

Három műveletet hoztunk létre. Amint alább látható, az első művelet eredménye egy kártyát.

![](../media/tutorial13_actions.PNG)

Lássuk, hogyan jött létre a kártya művelet típusa:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> A kártya három különböző paramétereket - bevitel kérdés, 1 és gomb 2 tartalmaz. Ezeket az elemeket is hivatkozik a kártyán, ahol meg kell adni a kérdés és a hozzájuk tartozó válaszok. Is hivatkozhat és entitások vagy vegyesen szöveg és entitásokat.

Szem ikonra bemutatja, hogy a kártya néz ki.

### <a name="practicing-creating-card-actions"></a>Kártya létrehozása műveletek eszköztárába

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. Válassza ki a "Kártya" a "művelettípus."
3. Válassza ki a "figyelmeztetés" a "Sablon" listából.
4. A "kérdés" mezőbe írja be a "Ugrás a bal vagy jobb"
5. A "button1" mezőbe írja be "balra"
6. A "button2" mezőbe írja be "megfelelő"
7. A "Mégse" gombra.

### <a name="train-dialog-using-an-adaptive-card"></a>Az adaptív kártyák használatával train párbeszédpanel

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hi"
3. A "Score műveletek" gombra.
4. Válassza ki a választ, "kérés: kérdést: Nyissa meg a bal vagy jobb?"
    - Használható szem ikonjára a kártya előzetes verzió
5. A Csevegés panelen kattintson a "Left" gombra a megjelenített parancssorban.
6. A "Score műveletek" gombra.
7. Válassza ki a választ, "Balra"
8. A "Mentés" gombra.
9. Válassza ki a választ, "kérés: kérdést: Nyissa meg a bal vagy jobb?"
10. A Csevegés panelen kattintson a "Jobb" gombra a megjelenített parancssorban.
11. A "Score műveletek" gombra.
12. Válassza ki a választ, a "Jobb"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hibrid robotok](./16-hybrid-bots.md)
