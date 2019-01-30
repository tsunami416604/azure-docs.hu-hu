---
title: Többértékű entitások használata a beszélgetés Learner modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja a többértékű entitásokat a beszélgetés Learner modellel.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228304"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Beszélgetéstanuló modell többértékű entitások használata
Ez az oktatóanyag bemutatja az entitások a többértékű tulajdonság.

## <a name="video"></a>Videó

[![Többértékű entitások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek
Többértékű entitások gyűlnek értékek listáját ahelyett, hogy egyetlen érték tárolására.  Ezek az entitások akkor hasznos, ha a felhasználók egynél több értéket adhat meg. A példában egy kétpizzás toppings.

Entitások megjelölt többértékű egyes ismert példányok az entitás egy listát a Bot memóriában hozzáfűzi fog rendelkezni. Az entitás értéket, hanem felülírják a későbbi felismerés fűzi hozzá.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületén kattintson az "új Model" kifejezésekre.
2. A "Name" mezőben írja be a "MultiValueEntities" és az enter.
3. A "Létrehozás" gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. A bal oldali panelen kattintson a "Entitás", majd az "Új entitás" gombra.
2. Válassza ki a "Custom betanított" a "entitástípus."
3. Írja be a "toppings" a "entitás neve."
4. Jelölje be a "Többértékű" jelölőnégyzetet.
    - Többértékű entitások gyűlnek az entitás egy vagy több értéket.
5. Jelölje be a "Negatable" jelölőnégyzetet.
    - A "Negatable" tulajdonság egy másik oktatóanyagban vonatkozik.
6. A "Létrehozás" gombra.

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "az alábbiakban a toppings: $toppings"
    - A vezető dollárjel azt jelzi, hogy az entitások bemutatása
3. A "Létrehozás" gombra.

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban..." mezőbe írja be a "milyen toppings szeretné használni?"
3. A "Kizárásának feljogosítja a" mezőbe írja be a "toppings."
4. A "Létrehozás" gombra.

Most már két műveletet.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>A modell betanítását

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hi."
3. A "Score műveletek" gombra.
4. Válassza ki a választ, a "milyen toppings szeretné?"
    - A PERCENTILIS 100 %-os megegyezik az egyetlen érvényes művelet a korlátok alapján.
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "sajtok iránti szenvedélyének és gomba" típusra
6. Kattintson a "sajtok iránti szenvedélyének", és válassza ki a címke "+ toppings"
7. Kattintson a "gomba", és válassza ki a címke "+ toppings"
8. A "Score műveletek" gombra.
9. Válassza ki a választ, "az alábbiakban a toppings: $toppings"
10. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "add bors" típusa
11. Kattintson a "bors", és válassza ki a címke "+ toppings"
12. A "Score műveletek" gombra.
13. Válassza ki a választ, "az alábbiakban a toppings: $toppings"
14. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", írja be a "remove sajtok iránti szenvedélyének"
15. Kattintson a "sajtok iránti szenvedélyének", és válassza ki a címke "-toppings"
16. A "Score műveletek" gombra.
17. Válassza ki a választ, "az alábbiakban a toppings: $toppings"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre betanított entitások](./08-pre-trained-entities.md)
