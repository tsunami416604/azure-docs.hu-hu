---
title: Bejelentkezés párbeszédpanelek Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan párbeszédpanelek jelentkezzen be egy Beszélgetéstanuló modellt.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5369e16e0f1adc48eb019f3790dc900577c144af
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243659"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Bejelentkezés párbeszédpanelek Beszélgetéstanuló modell

Ez az oktatóanyag bemutatja, hogyan hajtsa végre a végfelhasználói az Beszélgetéstanuló felületén belül tesztelése Hogyan naplózza a párbeszédpanelek; és hogyan javíthatja a megadottakat a párbeszédpanelek a modell javítása érdekében.

## <a name="video"></a>Videó

[![Az oktatóanyag 9 előzetes verzió](https://aka.ms/cl-tutorial-09-preview)](https://aka.ms/blis-tutorial-09)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
A napló párbeszédpanelek használatával tekintse át, és a párbeszédpanelek a végfelhasználókkal végzett javításokat.  Pontosabban a megoldásához entitás címkék és a műveleti beállításokat a betanított modell és a teljes rendszer teljesítményét. 

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a LogDialogs. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Az entitás nevét adja meg a város.
3. Kattintson a Létrehozás gombra.

### <a name="create-two-actions"></a>Hozzon létre két műveletet

1. Kattintson a műveletek, majd az új művelet
2. A választ írja be a "Melyik városban?".
3. Adja meg $city kizárásának entitásokat.
3. Kattintson a Létrehozás gombra

Ezután hozzon létre a második műveletet:

1. Kattintson a műveletek, majd az új művelet.
3. A válasz írja be a "$city az időjárás is valószínűleg sunny".
4. Szükséges entitások $city adja meg.
4. Kattintson a Létrehozás gombra.

Most már két műveletet.

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "Mi is az időjárás".
3. Kattintson a pontszám műveletek, és válassza a "Melyik városban?"
2. Adja meg a "Seattle".
3. Kattintson duplán a "Seattle", és válassza ki az városa.
    - Ez jelzi a város entitásként.
5. Kattintson a pontszám műveletek
6. Jelölje ki a "$city az időjárás valószínűleg sunny".
7. Kattintson a tanítási kész gombra.

Adjon hozzá egy másik példa párbeszédpanel:

1. Kattintson a vonat párbeszédpanelek és majd új Train párbeszédpanel.
2. Írja be a "Mi az az időjárás, a Seattle?". Figyelje meg, Seattle egy entitás van megjelölve.
5. Kattintson a pontszám műveletek 
6. Jelölje ki a "$city az időjárás valószínűleg sunny".
7. Kattintson a tanítási kész gombra.

### <a name="try-the-bot-as-the-user"></a>Próbálja ki a robot a felhasználóként
Tegyük fel, hogy ez a robot a felhasználók számára helyeztük.

1. Kattintson a napló párbeszédpanelek.
2. Kattintson az új naplófájl párbeszédpanel.
    - Ez megadja a robot, a felhasználói élmény lenne, a webes csevegési vezérlőelem bal oldalán a felhasználói felületen. Az üres területen, a jobb oldali figyelmen kívül hagyhatja.
3. Írja be a "hello".
4. A robot választ: "melyik városban?"
4. Írja be a "Boston".
5. A robot választ: "melyik városban?"
    - Ez nem tűnik megfelelő. Ezért mentse el ezt a párbeszédpanelt.
2. Kattintson a kész tesztelése.

Kezdjük egy új munkamenetet:

2. Kattintson az új naplófájl párbeszédpanel.
3. Írja be a "Boston előrejelzésének".
4. A robot választ: "melyik városban?"
2. Kattintson a kész tesztelése.

Most tekintsük meg a második párbeszédpanel javításokat:

1. Kattintson a "előrejelzés Boston a" Log-párbeszédpanelekhez alatt.
    - Ekkor megnyílik a beszélgetést.
    - A témakör a felhasználói oldalon kattint (a "bostonra vonatkozó előrejelzést" Itt), entitás címkéket módosíthatja.
    - Ha a rendszer oldalon kattint (Itt a melyik városban) módosíthatja a végrehajtandó művelet van kiválasztva.
5. Kattintson a "bostonra vonatkozó előrejelzést". 
    - Itt okozza-e, hogy Boston nem lett megjelölve egy egységként. Módosíthatja, hogy kell.
    - Kattintson duplán a "Boston", majd válassza ki az városa.
    - Módosítások elküldése gombra, és kattintson a Mentés gombra. Ezzel a végzett módosítások alapján képzési párbeszéd létrehozása, és akkor betett képzési párbeszédpanelek elvégzett módosítás ponton kell megadni.
6. Válassza a "$city az időjárás valószínűleg napsütéses is."
7. Kattintson a tanítási kész gombra. Ha Train-párbeszédpanelekhez mostantól, látni fogja az új művelet kerül.

![](../media/tutorial9_logdiag1.PNG)

Most tekintsük meg a többi párbeszédpanel javításokat:

1. Kattintson a "hello" Log-párbeszédpanelekhez alatt.
    - Ekkor megnyílik a beszélgetést.
3. A "hello" válasz melyik városban. Most azonban módosíthatja, hogy úgy, hogy több értelme. Még jobb válasz lenne valami, például "hello, vagyok az időjárás bot". De nem tartoznak, amely, hogy ne hozzon létre egyet a műveletek.
4. Kattintson a műveletet.
    - A választ, írja be "az időjárás bot vagyok. Az előrejelzések segíthetünk. "
6. A válasz a jelölőnégyzetet, várja meg, hogy egy nem várja meg a művelet megszünteti-ellenőrzése.
7. Kattintson a Létrehozás gombra.
8. Kattintással jelölje ki az új művelet. Ezután kattintson a Save (Mentés) gombra.
    - Ekkor azt vissza az adott pont a betanítási munkamenetben.
6. Ide kattintva válassza ki melyik városban?"
7. Írja be a "Boston". Kattintson duplán a címkéhez Boston entitásokban, ha még nem tette.
8. Kattintson a pontszám műveletek.
9. Jelölje be a "$city az időjárás valószínűleg napsütéses is."
10. Kattintson a tanítási kész gombra.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás észlelési visszahívási](./10-entity-detection-callback.md)
