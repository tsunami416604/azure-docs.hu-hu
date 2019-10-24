---
title: Alternatív bemenetek használata a Conversation Learner-Microsoft Cognitive Services használatával | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja a Conversation Learner alternatív bemeneteit.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7416939db8d6552f79fba700b8432de2ad228846
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704215"
---
# <a name="how-to-use-alternative-inputs"></a>Alternatív bemenetek használata

Ez az oktatóanyag bemutatja, hogyan használható az alternatív bemenetek mező a felhasználói hosszúságú kimondott szöveg az oktatási felületen.

## <a name="video"></a>Videó

[![Alternatív bemenetek oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek
Az alternatív bemenetek alternatív, szemantikailag egyenértékű felhasználói hosszúságú kimondott szöveg, amelyeket a felhasználó a betanítási párbeszédpanel egy adott pontján mondott. Ezek az alternatív bemenetek lehetővé teszik a hosszúságú kimondott szöveg-változatok tömörebb megadását anélkül, hogy az egyes variációkat külön betanítási párbeszédablakokban kellene foglalkoznia.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületen kattintson az "új modell" elemre.
2. A "név" mezőbe írja be a "AlternativeInputs" kifejezést, és nyomja le az ENTER billentyűt.
3. Kattintson a "létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson az "entitások", majd az "új entitás" gombra.
2. Válassza a "Custom betanítva" lehetőséget az "entitás típusa" elemnél.
3. Írja be a "város" kifejezést az "entitás neve" értékre.
4. Kattintson a "létrehozás" gombra.

![](../media/T10_actions.png)

Most hozzon létre három műveletet.

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a "melyik várost?"
3. A "várt entitás a felhasználói válaszban..." mezőjébe írja be a "város" kifejezést.
4. A "nem feljogosító jogosultságok" mezőben írja be a "város" kifejezést.
5. Kattintson a "létrehozás" gombra.

![](../media/T10_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a következőt: "az időjárás $city valószínűleg napos."
3. Kattintson a "létrehozás" gombra.

![](../media/T10_action_create_2.png)

### <a name="create-the-third-action"></a>A harmadik művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a "Try kérdés az időjárásra" kifejezést.
3. A "nem feljogosító jogosultságok" mezőben írja be a "város" kifejezést.
4. Kattintson a "létrehozás" gombra.

![](../media/T10_action_create_3.png)

Most már három művelettel rendelkezik.

![](../media/T10_actions.png)

### <a name="train-the-model"></a>A modell betanítása

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. A csevegés panelen, ahol a "írja be az üzenetet..." kifejezést, írja be a "mi az időjárás?" kifejezést.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ: "melyik város?"
5. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a "Denver" kifejezést.
6. Kattintson a "pontszám műveletek" gombra.
7. Válassza ki a választ, "a Denver időjárása valószínűleg napos."
8. Kattintson a "módosítások elküldése" gombra.

![](../media/T10_training_1.png)

Egy másik betanítási párbeszédablak létrehozásával kitanítjuk a modellt.

### <a name="second-model-train-dialog"></a>Második modell betanítása párbeszédpanel

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. A csevegés panelen, ahol a "írja be az üzenetet..." kifejezést írja be a "mit tehet?" kifejezésre.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ, "próbálkozzon az időjárás megkérdezésével".
5. A csevegés panelen, ahol a "írja be az üzenetet..." kifejezést, írja be a "mi az időjárás Seattle-ben?" kifejezést.
6. Kattintson a "Seattle", majd a "város" elemre az entitások listájáról.
7. Kattintson a "pontszám műveletek" gombra.
8. Válassza ki a választ, "a Seattle-i időjárás valószínűleg napos."
9. Kattintson a "módosítások elküldése" gombra.

![](../media/T10_training_2.png)

### <a name="third-model-train-dialog-using-alternative-input"></a>Harmadik modell betanítása párbeszédpanel alternatív bemenet használatával

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. A csevegési panelen írja be a "Súgó" kifejezést a következőre: "az üzenet beírása...".
3. Kattintson a "pontszám műveletek" gombra.
    - A modell nem biztos, hogy a legjobb lehetőség, így alapértelmezés szerint a legmagasabb százalékos érték lesz kiválasztva.
4. Kattintson az "elhagyás" gombra, majd a "megerősítés" gombra.

![](../media/T10_training_3.png)

A rendszerek jobb finomhangolása alternatív bemenetek használatával. Az oktatóanyag vagy a későbbiek során alternatív bevitelt is hozzáadhat.

1. A bal oldali panelen kattintson a "betanítási párbeszédpanelek" elemre, majd válassza a "mit tehet?" lehetőséget. a betanítási párbeszédpanelek listájából.
1. Kattintson a "mit tehet?" a csevegési panelen való Kimondás.
1. Az "alternatív bemenet hozzáadása..." mezőjébe írja be a "Súgó" kifejezést, és nyomja le az ENTER billentyűt.
1. Kattintson a "módosítások mentése" gombra.

![](../media/T10_training_4.png)

Vegyünk fel egy másik alternatív bemenetet a Houston kezelésére.

1. Kattintson a "mi az időjárás a Seattle-ben?" kifejezésre. a csevegési panelen való Kimondás.
1. Az "alternatív bemenet hozzáadása..." mezőbe írja be a "a Houston előrejelzése" kifejezést, és nyomja le az ENTER billentyűt.
   - A hibaüzenet kiemeli, hogy az alternatív bemeneti adatoknak szemantikailag egyenértékűnek kell lenniük, és ugyanazokat az entitásokat kell tartalmazniuk, mint az eredeti Kimondás; nem csak az entitások azonos értékei. Ugyanazon entitások jelenlétét kötelező megadni.
1. Kattintson a "Houston" elemre, és válassza a "város" lehetőséget az entitások listából.
1. Az "alternatív bemenet hozzáadása..." mezőjébe írja be a "előrejelzés Seattle-be" kifejezést, és nyomja meg az ENTER billentyűt.
1. Kattintson a "Seattle" elemre, és válassza a "város" lehetőséget az entitások listából.
1. Kattintson a "módosítások mentése" gombra.
1. Kattintson a "szerkesztés mentése" gombra.

![](../media/T10_training_5.png)

### <a name="testing-the-model"></a>A modell tesztelése

1. A bal oldali panelen kattintson a "naplók naplózása", majd az "új napló párbeszédpanel" elemre.
2. A csevegési panelen írja be a "Súgó" kifejezést, és írja be a következőt:.
3. A csevegési panelen írja be a következőt: "az üzenet beírása...", gépelje be a "a Denver előrejelzése" kifejezést.

![](../media/T10_logdialog.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Napló párbeszédpanelek](./11-log-dialogs.md)
