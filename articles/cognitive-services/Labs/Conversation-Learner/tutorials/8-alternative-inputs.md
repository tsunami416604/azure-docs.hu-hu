---
title: Alternatív bemenetek használata Beszélgetéstanuló – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használható alternatív bemenetek Beszélgetéstanuló.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3b38cc265b4adfb301dc1165e02b17a8aa7c9589
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252547"
---
# <a name="how-to-use-alternative-inputs"></a>Alternatív bemenetek használata

Ez az oktatóanyag bemutatja, hogyan használhatja a "alternatív bemenetek" mezőt a tanítási felületén a felhasználói bevitelhez.

## <a name="video"></a>Videó

[![Az oktatóanyag 8 előzetes verzió](https://aka.ms/cl-tutorial-08-preview)](https://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
"Másodlagos bemenetei között meg" olyan alternatív felhasználói kimondott szöveg, amely a felhasználó tudta rendelkezik említett egy képzési párbeszédpanelen egy adott időpontban. Alternatív bemenetek lehetővé teszik több compactly adja meg, mi a felhasználó a következő lehet:, listázásához, egy tanítási párbeszédpanelen egyes módosítások nélkül változata.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. A nevet írja be a AlternativeInputs. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-entity"></a>Entitás létrehozása

1. Kattintson az entitásokat, majd az új entitás.
2. Az entitás nevét adja meg a város.
3. Kattintson a Létrehozás gombra.

### <a name="create-three-actions"></a>Három műveletek létrehozása

1. Kattintson a műveletek, majd az új művelet
2. Válaszként írja be a "melyik városban szeretne?".
3. Adja meg $city kizárásának entitásokat.
3. Kattintson a Létrehozás gombra

Ezután hozzon létre a második műveletet:

1. Kattintson a műveletek, majd az új művelet.
3. A válasz írja be a "$city az időjárás is valószínűleg sunny".
4. Szükséges entitások $city adja meg.
4. Kattintson a Létrehozás gombra.

A harmadik művelet létrehozása:

1. Kattintson a műveletek, majd az új művelet.
3. Válaszként írja be a "Próbálja ki az időjárás kérése".
    - Ez akkor lehet felhasználó kérdésére adott válaszként például a "mit is a rendszer?"
4. Adja meg $city kizárásának entitásokat.
4. Kattintson a Létrehozás gombra

Most már három műveletet.

### <a name="train-the-bot"></a>A robot betanítása

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
2. Írja be a "Mi is az időjárás".
3. Kattintson a pontszám műveletek, és válassza a "melyik városban szeretne?"
2. Adja meg a "denver".
3. Kattintson duplán a "denver", és válassza ki az városa.
    - Ez jelzi a város entitásként.
5. Kattintson a pontszám műveletek
    - "denver" most már szerepel az városa entitás. 
6. Jelölje ki a "$city az időjárás valószínűleg sunny".
7. Kattintson a tanítási kész gombra.

Adjon hozzá egy másik példa párbeszédpanel:

1. Kattintson az új művelet, majd új Train párbeszédpanel.
2. Írja be a "mire használható?".
3. Kattintson a pontszám műveletek, és válassza a "Próbálja ki az időjárás kérése"
2. Adja meg a "Mi is az időjárás, a seattle".
3. Kattintson duplán a "seattle", és válassza ki az városa.
    - Ez jelzi a város entitásként.
5. Kattintson a pontszám műveletek
    - "seattle" most már szerepel az városa entitás. 
6. Jelölje ki a "$city az időjárás valószínűleg sunny".
7. Kattintson a tanítási kész gombra.

Lássuk, mi történik, ha a felhasználó szerint a fentiekre szemantikailag hasonló:

1. Kattintson az új művelet, majd új Train párbeszédpanel.
2. Írja be a "Súgó".
3. Kattintson a pontszám műveletek.
    - A két lehetséges válaszok pontszámok nagyon közel. Ez közli velünk a kapcsolatot a modell tisztában van vele a határ a két művelet között.
6. Kattintson a Szolgáltatásműveletnek oktatási, és győződjön meg róla.

![](../media/tutorial8_closescores.png)

Ebben az esetben, segít a párbeszédpanelek másodlagos bemenetek hozzáadásához. Felveheti őket, a tanítási végez. Lépjen vissza, és később is.

2. Kattintson a "Mire használható?" a párbeszédpanelek Train.
2. A párbeszédpanelen kattintson a "mire használható?" Jelölje ki.
    1. A jobb oldali ablaktáblában az entitás észlelése a Hozzáadás alternatív bemeneti adja meg néhány alternatív megoldások:
    1. Adja meg a "Mik azok a lehetőségek?"
    2. Adja meg a "Részletek a saját lehetőségek".
    3. Adja meg a "Súgó"
    1. Módosítások elküldése gombra.


![](../media/tutorial8_helpalternates.png)

2. Ezután kattintson a "Mi is az időjárás, a seattle".
    1. Hozzáadás alternatív beviteli adja meg a "seattle előrejelzésének".
    2. Kattintson duplán a "seattle", és válassza ki az városa. Az entitások alternatív bemenet legyen jelen és ugyanazokat az entitásokat. Azt is megfelel, ha az entitások a tartalom nem egyezik.
    3. Hozzáadás alternatív beviteli adja meg a "lesz, eső még ma a denver".
    4. Kattintson a "denver", és válassza ki az városa.
    5. Módosítások elküldése gombra, és hajtja végre.


Vegyünk fel más bemeneti adatokat az első párbeszédpanel:

1. Kattintson a vonat párbeszédpanelek.
2. Kattintson a "Mi is az időjárás" kezdetű párbeszédpanelen.
2. Kattintson a bal oldali panelen válassza a "Mi is az időjárás":
    1. Hozzáadás alternatív beviteli adja meg az időjárás-előrejelzés.
    2. Adja meg a "lesz, eső?"
    3. Módosítások elküldése gombra.
4. Kattintson a bal oldali panelen válassza a "denver":
    1. Hozzáadás alternatív beviteli adja meg "denver".
    2. Adja meg a "austin előrejelzésének".
        - A teljes kifejezés ki van emelve. Kattintson a kifejezést, majd a piros x. Válasszon austin, majd kattintson az városa.
        - Kattintson a Submit módosítások
    1. Kattintson a kész, amelynek hatására a modell újratanítása gombra.

![](../media/tutorial8_altcities.png)

Próbáljuk ki a változata létezik:

1. Kattintson az új Train párbeszédpanel.
2. Írja be a "Mik a képességek,".
3. Kattintson a pontszám műveletek.
    - A pontszámok mostantól több döntő meg a következő elvégzendő művelet, amely azt jelzi, hogy a modell a biztonságot.
2. Válassza ki a "Időjárási kérése Try".
6. Kattintson a kész tanítás

Most már rendelkezik látott hogyan alternatív bemenetek használható további tevékenység következik, előfordulhat, hogy rendelkezik mondta a felhasználó jelzi. Ezek segítenek ne hozzon létre több párbeszédpanelek, amely számos módon azonosak, egy egyetlen párbeszédpanel bezárásával őket és a felhasználó mondhatjuk számbavétele.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Napló párbeszédpanelek](./9-log-dialogs.md)
