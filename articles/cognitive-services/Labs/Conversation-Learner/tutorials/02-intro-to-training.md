---
title: Bevezetés a Conversation Learner Model betanítására – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan taníthat modelleket, beleértve a korábbi képzések elágazását és szerkesztését Conversation Learneron keresztül.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c657025ce588363cf76ce10868d809a9aff69222
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705612"
---
# <a name="introduction-to-training"></a>Bevezetés a betanításba

Ez az oktatóanyag a modell képzésének alapjait mutatja be, és egy korábbi képzésen alapuló új képzést rendez, valamint a bot-válasz szerkesztését a módosításhoz.

## <a name="video"></a>Videó

[![Bevezetés az oktatóanyagok előzetes verziójába](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek

- Műveletek Felhasználói bevitelre adott robot.
- Vonat Hogyan tanítunk egy robotot a felhasználói adatbevitelre való reagáláshoz.
- Elágazó Egy mentett betanítási párbeszédpanelen lévő felhasználói bevitel módosítása egy új, az eredetivel megegyező kiindulási panel létrehozásához, de a beszélgetést más irányba viszi.

## <a name="steps"></a>Lépések

### <a name="create-a-new-model"></a>Új modell létrehozása

1. A webes felhasználói felületen kattintson az új modell elemre.
2. A "név" mezőbe írja be az "INSPIRE bot" kifejezést. Ezután kattintson a Létrehozás gombra.

### <a name="create-an-action"></a>Művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válasza" mezőben adja meg a "Hi!" kifejezést. Szeretne még ma ihletet? "
    - Hagyja meg az összes többi mezőt, és törölje a jelölőnégyzeteket az alapértelmezett beállításnál.
3. Kattintson a Létrehozás gombra.

### <a name="first-training-and-creating-another-action-while-training"></a>Első betanítás és egy másik művelet létrehozása a képzés során

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hello" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést. 
    - Ez szimulálja a beszélgetés felhasználójának oldalát.
3. Kattintson a "pontszám műveletek" elemre.
4. Válassza ki a választ, "Hi! Szeretne még ma ihletet? "
5. A felhasználó a következővel válaszol: "yes".
6. Kattintson a "pontszám műveletek" elemre.
7. Kattintson a "+ művelet" gombra. 
    - Ekkor megnyílik az ismerős "művelet létrehozása" párbeszédpanel.
8. Írja be a robot válaszát a következőre: "You are Awesome!"
9. Kattintson a Létrehozás gombra.
10. Figyelje meg, hogy a robot azonnal válaszol.
11. Kattintson a Save (Mentés) gombra.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Az első betanítás második tanítása
1. Kattintson a rács sorra, amely összefoglalja az első képzést. 
    - Ez lehetővé teszi a meglévő képzés megtekintését és szerkesztését.
2. Kattintson az "igen" felhasználói válaszra. 
    - Ez a szerkesztési vezérlőket teszi elérhetővé.
3. Kattintson a ág ikonra. 
    - Ekkor megjelenik egy másik felhasználói adatbevitelt kérő üzenet egy új beszélgetéshez.
4. Írja be a "nem" értéket, nyomja meg az ENTER billentyűt, vagy kattintson a "létrehozás" gombra. 
    - Ezen a ponton a vonat párbeszédpanel egy új példánya lesz, amely az eredetivel változatlan marad.
5. Kattintson a "pontszám műveletek" elemre.
6. Kattintson a bot helytelen válaszára, amely most már megjelent.
7. Kattintson a "+ művelet" gombra 
    - annak érdekében, hogy egy új műveletet hozzunk létre a Robothoz való válaszadáshoz.
8. Írja be a robot válaszát a következőre: "nincs probléma! Jó napot! "
9. Kattintson a Létrehozás gombra
10. Figyelje meg, hogy a robot azonnal válaszol.
11. Kattintson a Save (Mentés) gombra.

### <a name="test-the-trainings"></a>A tréningek tesztelése
1. A bal oldali panelen kattintson a "naplók naplózása", majd az "új napló párbeszédpanel" elemre.
2. Írja be a "Hi" üzenetet. 
3. Figyelje meg, hogy a robot automatikusan reagál a betanított módon.
4. Írja be a "yes" (felhasználó) választ.
5. Figyelje meg, hogy az első betanítás működik.
6. Kattintson a "munkamenet időkorlátja" gombra. Ez azt jelzi, Conversation Learner szeretnénk újra kezdeni, figyelmen kívül hagyva a társalgási fordulatot, amely éppen lezajlott.
7. Írja be a "Hi" üzenetet. 
8. Figyelje meg, hogy a robot automatikusan reagál a betanított módon.
9. Írja be a felhasználói választ, "nem".
10. Figyelje meg, hogy a robotra adott válasz azt mutatja, hogy a második betanítás működik.
11. Kattintson a "kész tesztelés" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várakozás és nem várt műveletek](./03-wait-vs-nonwait-actions.md)
