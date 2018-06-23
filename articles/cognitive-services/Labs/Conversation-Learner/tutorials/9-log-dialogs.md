---
title: A beszélgetés tanuló alkalmazás – Microsoft kognitív szolgáltatások párbeszédpanelek bejelentkezés |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan párbeszédpanelek jelentkezzen be a beszélgetés tanuló alkalmazás.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348694"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>Bejelentkezés a beszélgetés tanuló alkalmazás párbeszédpanelek

Ez az oktatóanyag bemutatja, hogyan hajtsa végre a beszélgetés tanuló felületen; tesztelés végfelhasználói milyen párbeszédpanelek bejelentkezett; és hogyan végezheti el a javításokat párbeszédpanelek naplózza a modell javítása érdekében.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek
A napló párbeszédpanelek segítségével tekintse át, és a párbeszédpanelek a végfelhasználókkal végzett javításokat.  Pontosabban megoldhatja entitás címkék és a műveleti beállításokat a betanított modell és a rendszer általános teljesítményének javítása érdekében. 

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása LogDialogs. Majd kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Az entitás nevét adja meg a város.
3. Kattintson a Létrehozás gombra.

### <a name="create-two-actions"></a>Két tevékenység

1. Kattintson a műveletek, majd az új művelet
2. A válasz írja be a "Melyik városban?".
3. Entitások kizárásának $city meg.
3. Kattintson a Létrehozás gombra

A második művelet majd létrehozása:

1. Kattintson a műveletek, majd új művelet.
3. Válasz írja be a "a $city nem valószínűleg moziba".
4. Szükséges entitások $city adja meg.
4. Kattintson a Létrehozás gombra.

Most már két műveletet kell.

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Mi az az időjárási".
3. Kattintson a pontszám műveleteket, majd válassza a "Melyik városban?"
2. Adja meg a "Seattle".
3. Kattintson duplán arra a "Seattle", és válassza ki a város.
    - Ez jelzi a város egységként.
5. Kattintson a pontszám műveletek
6. Válassza az "a $city időjárási valószínűleg moziba".
7. Kattintson a tanítási végezhető el.

Adja hozzá egy másik példa párbeszédpanel:

1. Kattintson az új művelet, majd az új vonat párbeszédpanel.
2. Írja be a "Mi az budapesti időjárási?". Figyelje meg, Seattle van megjelölve, entitás.
5. Kattintson a pontszám műveletek 
6. Válassza az "a $city időjárási valószínűleg moziba".
7. Kattintson a tanítási végezhető el.

### <a name="try-the-bot-as-the-user"></a>Próbálja meg a botot felhasználóként
Most tegyük fel, hogy jelenleg telepített ez botot a felhasználók számára.

1. Kattintson a napló párbeszédpanelek.
2. Kattintson az új Csevegés.
    - Ez megadja a botot, a webes Csevegés vezérlő bal oldalán a felhasználói felület a felhasználó akkor számára. Az üres területen kattintson a jobb figyelmen kívül hagyhatja.
3. Írja be a "Hello szövegrészt".
4. Botot válasz: "melyik városban?"
4. "Boston" típusú.
5. Botot válasz: "melyik városban?"
    - Ez nem tűnik megfelelő. Most mentse ezt a párbeszédpanelt.
2. Kattintson a végzett tesztelése.

Kezdjük egy új munkamenetben:

2. Kattintson az új Csevegés.
3. "Boston előrejelzésének" típusú.
4. Botot válasz: "melyik városban?"
2. Kattintson a tanítási végezhető el.

Most már most Meggyőződünk javításokat a második párbeszédpanel:

1. Kattintson a "előrejelzést Boston" alatt napló párbeszédpanel.
    - Ekkor megnyílik a beszélgetést.
    - Ha a felhasználó oldalon a beszélgetés kattintson (ide "Boston az előrejelzés"), entitás címkéket módosíthatja.
    - Ha a rendszer oldalon (Itt a "melyik városban"), módosíthatja a kiválasztott végrehajtandó művelet.
5. Kattintson a "Boston előrejelzést". 
    - Itt okozza-e, hogy Boston nem lett megjelölve egy egységként. Amely módosítani kell.
    - Kattintson duplán arra a "Boston", majd válassza ki a város.
    - Kattintson a módosítások küldje el, és kattintson a Mentés gombra. Ez létrehoz egy képzési párbeszédpanelen végrehajtott módosítások alapján, és dobja el, a képzési párbeszédpanelek helyén a módosítások.
6. Válassza az "a $city időjárási valószínűleg moziba."
7. Kattintson a tanítási végezhető el. Ha vonat párbeszédek most, látni fogja az új művelet jelenik meg.

![](../media/tutorial9_logdiag1.PNG)

Most már most Meggyőződünk más párbeszédpanel javításokat:

1. Kattintson a "hello" alatt napló párbeszédpanel.
    - Ekkor megnyílik a beszélgetést.
3. Megjegyzés: a "hello" válasz melyik városban. De biztosak szeretnénk módosíthatja, hogy egy több legcélszerűbb. Jobb válasz lenne valami például a "hello, én vagyok a időjárási bot". Azonban nincs olyan művelet, amelyet, hogy, hogy a létrehozáshoz.
4. Kattintson a művelet.
    - Írja be a válasz "a időjárási botot vagyok. I elősegíti az előrejelzések. "
6. A válasz jelölőnégyzetet, várjon egy nem várja meg a művelet végrehajtásához UN-ellenőrzése.
7. Kattintson a Létrehozás gombra.
8. Kattintással jelölje be ezt a műveletet. Ezután kattintson a Save (Mentés) gombra.
    - Ekkor meg vissza az adott pontra a képzés munkamenetben.
6. Válassza ki, kattintson a "melyik városban?"
7. A "Boston" típusú. Kattintson duplán egy entitás, ha még nem Boston címkéhez.
8. Kattintson a pontszám műveletek.
9. Jelölje be a "a $city nem valószínűleg moziba."
10. Kattintson a tanítási végezhető el.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás észlelési visszahívási](./10-entity-detection-callback.md)
