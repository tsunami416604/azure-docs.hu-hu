---
title: Alternatív bemenetek használata Beszélgetéstanuló – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használható alternatív bemenetek Beszélgetéstanuló.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 10335f9c74b9033b303c960a77af136cc80d75bb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094364"
---
# <a name="how-to-use-alternative-inputs"></a>Alternatív bemenetek használata

Ez az oktatóanyag bemutatja, hogyan használja az alternatív bemenetek mezőt a tanítási felületén a felhasználó kimondott szöveg.

## <a name="video"></a>Videó

[![Alternatív bemenetek oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
Alternatív bemenetek olyan alternatív, szemantikailag egyenértékűnek felhasználói kimondott szöveg, amely a felhasználó előfordulhat, hogy rendelkezik említett, egy tanítási párbeszédpanelen egy adott időpontban. Ezeket a bemeneteket alternatív lehetővé teszik több compactly adja meg a kimondott szöveg változata oldja meg a tanítási párbeszédpanelek egyes módosítások nélkül.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "AlternativeInputs" és az enter.
3. A "Létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza ki a "Custom betanított" a "entitástípus."
3. Írja be a "city" a "entitás neve."
4. A "Létrehozás" gombra.

![](../media/T10_actions.png)

Most hozzunk létre három műveletet.

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Melyik városban?"
3. A "Várt entitás felhasználói válasz..." mezőbe írja be a "city".
4. A "Kizárásának feljogosítja a" mezőjében írja be a "city".
5. A "Létrehozás" gombra.

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "$city az időjárás Ez valószínűleg napsütéses."
3. A "Létrehozás" gombra.

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>A harmadik művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "Próbálja ki az időjárás kérése."
3. A "Kizárásának feljogosítja a" mezőjében írja be a "city".
4. A "Létrehozás" gombra.

![](../media/T10_action_create_3.png)

Most már három műveletet.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "Mi az az időjárás?"
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "Melyik városban?"
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Denver" típusra
6. A "Score műveletek" gombra.
7. Válassza ki a választ, a "Denver az időjárás valószínűleg napsütéses."
8. A "Módosítások elküldése" gombra.

![](../media/T10_training_1.png)

Nézzük betanítja a modellt, további hozzon létre egy másik train párbeszédpanel.

### <a name="second-model-train-dialog"></a>Második modell Train párbeszédpanel

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "Mire használható?"
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "Próbálja ki az időjárás kérése."
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "Mi az az időjárás, a Seattle?"
6. Kattintson a "Seattle", majd kattintson a "city" entitás listájából.
7. A "Score műveletek" gombra.
8. Válassza ki a választ, a "Budapesti időjárás valószínűleg napsütéses."
9. A "Módosítások elküldése" gombra.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Alternatív bemeneti harmadik modell Train párbeszédpanel

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", írja be a "Súgó"
3. A "Score műveletek" gombra.
    - A modell le bizonytalan a legjobb lehetőség, így alapértelmezés szerint azt választja a legmagasabb százalékos gyakoriságot.
4. Kattintson a "Abandon oktatási" gombra, majd a "Jóváhagyás" gombra.

![](../media/T10_training_3.png)

Nézzük jobban finomhangolása alternatív bemenet használatával a rendszer. Alternatív beviteli adhat hozzá, amíg oktatási vagy újabb verziója.

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd válassza a "Mire használható?" a párbeszédpanelek Train listájából.
1. Kattintson a "mire használható?" a Csevegés panelen utterance.
1. A "alternatív bemenet hozzáadása..." mező, írja be "Súgó", majd nyomja le adja meg.
1. A "Módosítások mentése" gombra.

![](../media/T10_training_4.png)

Adjunk hozzá egy másik alternatív beviteli Houston kezelésére.

1. Kattintson a "Mi az az időjárás, a Seattle?" a Csevegés panelen utterance.
1. Az "Add alternatív beviteli..." mezőben típusa "előrejelzést ad a Houston", és az enter.
   - A hiba üzenet fontosabb eseményeket a tény alternatív bemenetei között legyen szemantikailag, és ugyanazokat az entitásokat, mint az eredeti utterance (kifejezés); nem csak ugyanazokat az értékeket az entitások. Ugyanezeket az entitásokat a szükséges.
1. Kattintson a "Houston", és válassza a "city" entitások listájáról.
1. Az "Add alternatív beviteli..." mezőben típusa "előrejelzést ad a Seattle" és az enter.
1. Kattintson a "Seattle", és válassza a "city" entitások listájáról.
1. A "Módosítások mentése" gombra.
1. Kattintson a "Mentés szerkesztése" gombra.

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>A modell tesztelése

1. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez", majd az "új naplófájl párbeszédpanelen."
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", írja be a "Súgó me"
3. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Denver vonatkozó előrejelzésben" típusa

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Napló párbeszédpanelek](./11-log-dialogs.md)
