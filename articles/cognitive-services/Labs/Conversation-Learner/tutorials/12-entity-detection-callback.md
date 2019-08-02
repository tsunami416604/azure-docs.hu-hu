---
title: Az entitások észlelési visszahívásának használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja az entitások észlelésének visszahívását egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3f6094da7b103f1c99e9bb3fa9313354fcb2a8bc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703993"
---
# <a name="how-to-use-entity-detection-callback"></a>Az entitások észlelési visszahívásának használata

Ez az oktatóanyag az entitások észlelésének visszahívását mutatja be, és bemutatja az entitások feloldásának általános mintáját.

## <a name="video"></a>Videó

[![Entitás-észlelés visszahívási oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az `tutorialEntityDetectionCallback` szükséges, hogy a robot fusson.

    npm run tutorial-entity-detection

## <a name="details"></a>Részletek
Az entitások észlelésének visszahívásával engedélyezhető az entitás-felismerési viselkedés és az entitások átalakítása programkódon keresztül. Ezt a funkciót bemutatjuk egy tipikus, az entitások észlelésére szolgáló visszahívás kialakítási mintájának bejárásával. Például a "Big Apple" feloldása a "New York" értékre.

## <a name="steps"></a>Lépések

### <a name="create-the-model"></a>A modell létrehozása

1. A webes felhasználói felületen kattintson az "új modell" elemre.
2. A "név" mezőbe írja be a "EntityDetectionCallback" kifejezést, és nyomja le az ENTER billentyűt.
3. Kattintson a "létrehozás" gombra.

Ebben a példában három entitásra van szükség, ezért hozzuk létre a hármat.

### <a name="create-the-custom-trained-entity"></a>Az egyéni betanított entitás létrehozása

1. A bal oldali panelen kattintson az "entitások", majd az "új entitás" gombra.
2. Válassza a "Custom betanítva" lehetőséget az "entitás típusa" elemnél.
3. Írja be a "város" kifejezést az "entitás neve" értékre.
4. Kattintson a "létrehozás" gombra.

### <a name="create-the-first-programmatic-entity"></a>Az első programozott entitás létrehozása

1. Kattintson az "új entitás" gombra.
2. Válassza a "programozott" lehetőséget az "entitás típusa" elemnél.
3. Írja be az "CityUnknown" kifejezést az "entitás neve" értékre.
4. Kattintson a "létrehozás" gombra.

### <a name="create-the-first-programmatic-entity"></a>Az első programozott entitás létrehozása

1. Kattintson az "új entitás" gombra.
2. Válassza a "programozott" lehetőséget az "entitás típusa" elemnél.
3. Írja be az "CityResolved" kifejezést az "entitás neve" értékre.
4. Kattintson a "létrehozás" gombra.

Most hozzon létre három műveletet.

### <a name="action-creation"></a>Művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőbe írja be a "melyik várost kívánja?" kifejezést
3. A "várt entitás a felhasználói válaszban..." mezőjébe írja be a "város" kifejezést.
4. A "nem feljogosító entitások" mezőben írja be a "City" kifejezést.
5. Kattintson a "létrehozás" gombra.
6. Kattintson az "új művelet" gombra.
7. A "bot válaszában..." mezőbe írja be a "melyik várost kívánja?" kifejezést
8. A "várt entitás a felhasználói válaszban..." mezőjébe írja be a "nem tudom, hogy ez a város" kifejezést.
9. Kattintson a "létrehozás" gombra.
10. Kattintson az "új művelet" gombra.
11. A "bot válaszában..." mezőjébe írja be a "$CityResolved nagyon szép" értéket.
12. Kattintson a "létrehozás" gombra.

Itt látható a visszahívási kód:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>A modell betanítása

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hi" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ, hogy "melyik várost kívánja?"
5. A csevegés panelen írja be az "üzenet beírása..." kifejezést, írja be a "Big Apple" kifejezést.
6. Kattintson a "pontszám műveletek" gombra.
    - A gombra kattintva elindíthatja az entitás észlelésének visszahívását
    - A visszahívási kód helyesen állítja be a CityResolved-entitás értékét a "New York" értékre.
7. Válassza ki a választ: "New York nagyon szép."

Ez a minta számos bot-forgatókönyvre jellemző. A felhasználói hosszúságú kimondott szöveg és a kinyert entitások az üzleti logikához vannak megadva, és ez a logika átalakítja a teljes felhasználót a kanonikus formára, amelyet ezután a program a párbeszédpanel későbbi bekapcsolása céljából ment a programozott entitásokra.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Munkamenet-visszahívások](./13-session-callbacks.md)
