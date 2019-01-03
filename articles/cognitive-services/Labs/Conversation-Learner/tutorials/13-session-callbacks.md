---
title: Munkamenet-visszahívások használata Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használja a munkamenet-visszahívások Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5458d83fcc9fad50a2a52273ea6b5573ed90a97e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796773"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Munkamenet-visszahívások Beszélgetéstanuló modell használatával

Ez az oktatóanyag bemutatja a munkamenetek, hogyan kezeli őket, és Beszélgetéstanuló a onSessionStart és onSessionEnd visszahívások.

## <a name="video"></a>Videó

[![Munkamenet visszahívások oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a "tutorialSessionCallbacks" robot.

    npm run tutorial-session-callbacks

## <a name="details"></a>Részletek
Ez az oktatóanyag bemutatja a munkamenet, milyen munkamenetek alapértelmezett kezel, és hogyan adott viselkedést felülírhatja fogalmát.

Beszélgetéstanuló az egy munkamenetet jelöl egy, a robottal folyamatos interaktív exchange. Munkamenetek rendelkezhet több bekapcsolja, de ha harminc percnél nagyobb programozott módon inaktivitás miatt leállított kapcsolódjanak.  Lásd: információ a "Korlátok" súgóoldalt módosítására az alapértelmezett munkamenet időtúllépési időtartama.

A robot számára hozzon létre egy új munkamenetet, és állítsa vissza az ismétlődő Neurális hálózat kezdeti állapotában ez hosszú inaktív időtartam után miatt. Alapértelmezés szerint minden entitás érték törlődik. Ez az alapértelmezett viselkedés, törölhetnek entitás értékeket módosíthatja az alább látható módon.

### <a name="load-the-demo-model"></a>A bemutató modell betöltése

A webes felhasználói felületen kattintson a "Oktatóanyagokban importálása", és válassza a "Oktatóanyag – 13-SessionCallbacks" nevű modell.

### <a name="code-for-the-callbacks"></a>A visszahívások programkódja

Mintakód a két visszahívások ebben a modellben található: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Ez a módszer a BotName entitás állítja be.
- OnSessionEndCallback: megadhatja a szeretné megőrizni. Ezzel törli a felhasználónév és a felhasználó telefont kivételével minden entitás.

Minden egyes visszahívási nem kötelező.

### <a name="actions"></a>Műveletek

Négy művelet a modellben vannak meghatározva. A meglévő műveleteket jelennek meg a rácsnézetben "Műveletek"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Létrehozása egy teljes munkamenet műveletet (visszahívási hívás)

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. Válassza ki a "ENDSESSION" a "entitástípus."
3. Az "Adatok..." mezőben írja be a "Kész"
4. A "Létrehozás" gombra.

### <a name="edit-an-existing-action"></a>Egy meglévő művelet szerkesztése

1. Válassza ki a "tehát $UserName, Ön $UserLocation" a rácsnézet műveletet.
2. Ellenőrzés megszünteti a "Várakozás a Response" jelölőnégyzetet.
3. A "Mentés" gombra.

### <a name="chaining-actions"></a>Láncolás – műveletek

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hi."
3. A "Score műveletek" gombra.
4. Válassza ki a válasz "Üdvözöljük, vagyok Botty. Mi a neve?"
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Lars" típusra
6. Válassza ki a választ, "szia Lars. Mi a telefonszáma?"
7. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "555-555-5555" típusra
8. A "Score műveletek" gombra.
9. Válassza ki a választ, az "Állapítható meg Botty tartózkodási Lars?"
10. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Seattle" típusra
11. A "Score műveletek" gombra.
12. "Így, Lars, egyes Seattle", válassza ki a válasz
13. Válassza ki a választ, az "Kész"
14. A "Mentés" gombra.

### <a name="testing-the-model"></a>A modell tesztelése

1. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez", majd az "új Log" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hi"
3. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Lars" típusra
4. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "555-555-5555" típusra
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Seattle" típusra
    - Ezen a ponton kivéve a hely összes entitás értéket kell megőrződnek.
6. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", típus a "hello"
7. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Detroit" típusra
8. A "Időtúllépés" gombra.
    - Erre a gombra kattintva gyakorolja a robot inaktív időszakaik hosszú válasz
9. Az "OK" gombra.
10. Kattintson a "Végzett tesztelése" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API-hívások](./14-api-calls.md)
