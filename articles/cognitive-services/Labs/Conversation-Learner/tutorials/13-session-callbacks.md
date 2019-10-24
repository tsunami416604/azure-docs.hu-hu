---
title: Munkamenet-visszahívások használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja a munkamenet-visszahívásokat egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703950"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Munkamenet-visszahívások használata Conversation Learner modellel

Ez az oktatóanyag bemutatja a munkameneteket, azok kezelését és Conversation Learner onSessionStart és onSessionEnd visszahívásait.

## <a name="video"></a>Videó

[![Munkamenet-visszahívások oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy a "tutorialSessionCallbacks" robot fusson.

    npm run tutorial-session-callbacks

## <a name="details"></a>Részletek
Ez az oktatóanyag ismerteti a munkamenet fogalmát, a munkamenetek alapértelmezett kezelését, valamint azt, hogy miként lehet felülbírálni ezt a viselkedést.

Conversation Learner a munkamenet egy, megszakítás nélküli interaktív Exchange-t jelöl a robottal. A munkamenetek több fordulattal is rendelkezhetnek, de az inaktivitás miatt programozottan lezárult, ha a nagyobb, mint harminc perc lejárt.  Az alapértelmezett munkamenet időtúllépési időtartamának módosításával kapcsolatos információkért tekintse meg a "korlátok" Súgó oldalát.

Ez a hosszú ideig tartó inaktivitás hatására a robot új munkamenetet hoz létre, és visszaállítja az ismétlődő neurális hálózatot a kezdeti állapotába. Alapértelmezés szerint az összes entitás értéke törlődik. Az entitások értékének törlésének alapértelmezett viselkedése az alább látható módon módosítható.

### <a name="load-the-demo-model"></a>A bemutató modell betöltése

A webes felhasználói felületen kattintson az "oktatóanyagok importálása" elemre, és válassza ki a "tutorial-13-SessionCallbacks" nevű modellt.

### <a name="code-for-the-callbacks"></a>A visszahívások kódja

A modell két visszahívásának mintakód a következő címen található: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: Ez a metódus beállítja a BotName entitást.
- OnSessionEndCallback: megadhatja, hogy mit szeretne megőrizni. Ezzel törli az összes entitást a Felhasználónév és a felhasználói telefon kivételével.

Minden visszahívás nem kötelező.

### <a name="actions"></a>Műveletek

Négy művelet van definiálva a modellben. A meglévő műveletek a "műveletek" rács nézetében jelennek meg.

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Befejezési munkamenet-művelet létrehozása (visszahívás meghívásához)

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. Válassza az "ENDSESSION" lehetőséget az "entitás típusa" elemnél.
3. Az "adattábla"... mezőjébe írja be a "kész" értéket.
4. Kattintson a "létrehozás" gombra.

### <a name="edit-an-existing-action"></a>Meglévő művelet szerkesztése

1. Válassza ki a "so, $UserName $UserLocation" műveletet a rács nézetből.
2. Törölje a jelet a "Várakozás a válaszhoz" jelölőnégyzetből.
3. Kattintson a Save (Mentés) gombra.

### <a name="chaining-actions"></a>Láncolási műveletek

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hi" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ: "Hi, I 'm popsi. Mi a neve?
5. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "Lars"
6. Válassza ki a választ, "Szia Lars. Mi a telefonszáma? "
7. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "555-555-5555"
8. Kattintson a "pontszám műveletek" gombra.
9. Válassza ki a választ: "el tudja popsi a tartózkodási helyét, Lars?"
10. A csevegés panelen, ahol a "írja be az üzenetet..." kifejezést, írja be a "Seattle" kifejezést.
11. Kattintson a "pontszám műveletek" gombra.
12. Válassza ki a választ: "so, Lars, Ön Seattle-ben"
13. Válassza ki a választ, "kész"
14. Kattintson a Save (Mentés) gombra.

### <a name="testing-the-model"></a>A modell tesztelése

1. A bal oldali panelen kattintson a "naplózási párbeszédpanelek" elemre, majd az "új napló párbeszédpanel" gombra.
2. Írja be a "Hi" kifejezést a csevegés panelen, ahol a "begépelheti az üzenetet..." kifejezést.
3. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "Lars"
4. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "555-555-5555"
5. A csevegés panelen, ahol a "írja be az üzenetet..." kifejezést, írja be a "Seattle" kifejezést.
    - Ezen a ponton az összes entitás értékének meg kell maradnia a hely kivételével.
6. Írja be a "Hello" kifejezést a csevegés panelen, ahol az "üzenet beírása..." kifejezést írja be:
7. A csevegés panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "Detroit"
8. Kattintson a "munkamenet időkorlátja" gombra.
    - Ha erre a gombra kattint, a bot a hosszú ideig tartó inaktivitásra adott válaszát alkalmazza.
9. Kattintson az OK gombra.
10. Kattintson a "kész tesztelés" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [API-hívások](./14-api-calls.md)
