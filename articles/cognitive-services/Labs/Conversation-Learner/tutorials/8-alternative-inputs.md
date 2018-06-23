---
title: Alternatív bemeneti adatok használata a beszélgetés tanuló - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan beszélgetés tanuló alternatív bemenetek használandó.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2214436b193932e5b3b80c190f7754a0436b7ed8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348698"
---
# <a name="how-to-use-alternative-inputs"></a>Alternatív bemeneti adatok használata

Ez az oktatóanyag bemutatja, hogyan használja az "alternatív bemenetek" mezőt a tanítási felületen felhasználói beavatkozást.

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy fut-e az általános útmutató botot

    npm run tutorial-general

## <a name="details"></a>Részletek
"Alternatív bemenetek" alternatív felhasználói utterances, amely a felhasználó tudta rendelkezik említett képzési párbeszédpanelen adott helyen. Alternatív bemeneti adatok lehetővé teszik több compactly adja meg, mi a felhasználó a következő lehet: anélkül, hogy egyes módosítások egy külön képzési párbeszédpanelen listázásához változatait.

## <a name="steps"></a>Lépések

### <a name="create-the-application"></a>Az alkalmazás létrehozása

1. A webes felhasználói felületén kattintson az új alkalmazás
2. A név megadása AlternativeInputs. Majd kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd új entitás.
2. Az entitás nevét adja meg a város.
3. Kattintson a Létrehozás gombra.

### <a name="create-three-actions"></a>Hozzon létre három műveletek

1. Kattintson a műveletek, majd az új művelet
2. Válasz írja be a "mely Cityben van szüksége?".
3. Entitások kizárásának $city meg.
3. Kattintson a Létrehozás gombra

A második művelet majd létrehozása:

1. Kattintson a műveletek, majd új művelet.
3. Válasz írja be a "a $city nem valószínűleg moziba".
4. Szükséges entitások $city adja meg.
4. Kattintson a Létrehozás gombra.

A harmadik művelet létrehozása:

1. Kattintson a műveletek, majd új művelet.
3. Válasz írja be a "Megadását kéri a időjárási Try".
    - Ez lenne a felhasználó kérdéséhez tartozó válasz, mint "milyen műveleteket végezhetnek el a rendszer?"
4. Entitások kizárásának $city meg.
4. Kattintson a Létrehozás gombra

Most már rendelkezik három műveletet.

### <a name="train-the-bot"></a>A botot képzése

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
2. Írja be a "Mi az az időjárási".
3. Kattintson a pontszám műveleteket, majd válassza a "mely Cityben van szüksége?"
2. Adja meg a "jelenti".
3. Kattintson duplán arra a "jelenti", és válassza ki a város.
    - Ez jelzi a város egységként.
5. Kattintson a pontszám műveletek
    - Vegye figyelembe, hogy jelenti most már szerepel a város entitás. 
6. Válassza az "a $city időjárási valószínűleg moziba".
7. Kattintson a tanítási végezhető el.

Adja hozzá egy másik példa párbeszédpanel:

1. Kattintson az új művelet, majd az új vonat párbeszédpanel.
2. Írja be a "mire szolgál?".
3. Kattintson a pontszám műveleteket, majd válassza a "Megadását kéri a időjárási Try"
2. Adja meg, "Mi az a budapesti időjárási".
3. Kattintson duplán arra a "seattle", és válassza ki a város.
    - Ez jelzi a város egységként.
5. Kattintson a pontszám műveletek
    - Vegye figyelembe, hogy Budapest most már szerepel a város entitás. 
6. Válassza az "a $city időjárási valószínűleg moziba".
7. Kattintson a tanítási végezhető el.

Nézzük meg, mi történik, ha a felhasználó a fenti szemantikailag hasonló szerint:

1. Kattintson az új művelet, majd az új vonat párbeszédpanel.
2. Írja be a "Súgó".
3. Kattintson a pontszám műveletek.
    - Vegye figyelembe, hogy a két lehetséges válaszokat pontszámok nagyon közel. Ez alapján nekünk a modell tisztában van vele a határt, a két művelet között.
6. Kattintson a megszakítási oktatási és erősítse meg.

![](../media/tutorial8_closescores.png)

Ebben az esetben azt segítene párbeszédek alternatív bemenetek hozzáadásához. Később is hozzáadhatja, a tanítási végez. Lépjen vissza, és később is.

2. Kattintson a "Mire szolgál?" a vonat párbeszédpanelek.
2. A párbeszédpanelen kattintson a "mire szolgál?" Jelölje ki.
    1. A jobb oldali ablaktáblában az entitás észlelése a bővítmény alternatív bemeneti adja meg alternatív néhány:
    1. Adja meg a "Mik a választási lehetőségek?"
    2. Adja meg a "Arról, hogy a választási lehetőségek".
    3. Adja meg a "Súgó"
    1. Kattintson a változások elküldése.


![](../media/tutorial8_helpalternates.png)

2. Most kattintson a "Mi az a budapesti időjárási".
    1. Hozzáadás alternatív beviteli adja meg a "seattle előrejelzésének".
    2. Kattintson duplán arra a "seattle", és válassza ki a város. Vegye figyelembe, hogy az entitások alternatív bemenetek legyen jelen és ugyanazokat az entitásokat. Nem probléma a entites tartalma nem egyezik.
    3. Hozzáadás alternatív beviteli adja meg a "fogja azt eső ma Debrecenben".
    4. Kattintson a "jelenti", és válassza a város.
    5. Változások elküldése gombra és történik.


Az első párbeszédpanel alternatív bemenetek adjuk hozzá:

1. Kattintson a vonat párbeszédpanelek.
2. Kattintson a párbeszédpanelen, "Mi az az időjárási" kezdve.
2. Kattintással jelölje ki a "Mi az az időjárási" a bal oldali panelen:
    1. Hozzáadás alternatív beviteli adja meg a "időjárás".
    2. Adja meg a "fogja azt eső?"
    3. Kattintson a Küldés módosítások.
4. Kattintson a "jelenti" kiválasztásához kattintson a bal oldali ablaktáblában:
    1. Hozzáadás alternatív beviteli megadása "jelenti".
    2. Adja meg a "austin előrejelzésének".
        - Vegye figyelembe, hogy a teljes kifejezés ki van jelölve. Kattintson a kifejezést, majd a piros x. Válasszon austin, majd kattintson a város.
        - Kattintson a Küldés módosítások
    1. Kattintson a újratanítása át a modellt, amely akkor végezhető el.

![](../media/tutorial8_altcities.png)

Próbáljuk meg az eltéréseket:

1. Kattintson az új vonat párbeszédpanel.
2. Írja be a "Mik képességek,".
3. Kattintson a pontszám műveletek.
    - Ne feledje, hogy az eredmények most több döntő meg a következő művelet, amely megadja, hogy a modell biztonsága.
2. Válassza ki a "Időjárási kérő Try".
6. Kattintson a tanítási kész

Most már rendelkezik látott hogyan alternatív bemenetek jelzi a felhasználó előfordulhat, hogy rendelkezik említett egyebek használható. Segítenek azoknak ne hozzon létre számos párbeszédpanelt, amely az sokféleképpen megegyeznek, az egyetlen párbeszédpanel bezárásával őket, és a felhasználó mondani számbavétele.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Napló párbeszédpanelek](./9-log-dialogs.md)
