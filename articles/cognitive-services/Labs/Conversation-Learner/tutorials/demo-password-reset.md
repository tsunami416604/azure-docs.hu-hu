---
title: Bemutató Conversation Learner modell, jelszó-visszaállítás – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre bemutató Conversation Learner modellt.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703399"
---
# <a name="demo-password-reset"></a>Bemutató: Jelszó alaphelyzetbe állítva
Ez az oktatóanyag egy egyszerű technikai támogatási robotot mutat be, amely segíthet a Conversation Learner által működtetett jelszavak visszaállításában. A robot modellje a nem triviális párbeszédpanel-folyamatokat és a többfázisú sorozatokat is képes tanulni, beleértve a tartományon kívüli osztályokat. A feladat kód vagy entitások nélkül is elvégezhető.

## <a name="video"></a>Videó

[![Bemutató jelszavának előzetes verziója](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy a jelszó-visszaállítási robot fusson

    npm run demo-password

### <a name="open-the-demo"></a>A bemutató megnyitása

A webes felhasználói felület modellek listájában kattintson az oktatóanyag-bemutató jelszavának alaphelyzetbe állítása elemre. 

### <a name="actions"></a>Műveletek

A modell olyan műveleteket tartalmaz, amelyek célja, hogy segítsen a felhasználóknak a gyakori jelszavas problémák megoldásában.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Betanítási párbeszédpanelek

A modell több betanítási párbeszédpanelt is tartalmaz, köztük olyanokat is, amelyek kimutatják a tartományi osztály betanítását. Például olyan felhasználók, akik igénybe vehetnek útvonaltervet. A minta robot néhány bemutató célra lett kitanítva, és egyszerűen válaszol a "nem tud segíteni ezzel". A meglévő betanítási párbeszédablakok listája a bal oldali panelen a "betanítási párbeszédablakok" alatt található.

![](../media/tutorial_pw_reset_entities.PNG)

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. A csevegési panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "elveszett a jelszavam".
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ: "Ez a helyi fiók vagy a Microsoft-fiók?"
5. A csevegési panelen írja be az "üzenet beírása..." kifejezést, írja be a következőt: "helyi fiók – kérjük"
6. Kattintson a "pontszám műveletek" gombra.
7. Válassza ki a választ: "a Windows melyik verziója van?"
8. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a "Windows XP" kifejezést.
9. Kattintson a "pontszám műveletek" gombra.
10. Kattintson a "+ művelet" gombra.
11. A "bot válaszában..." mezőjébe írja be a "megoldás: Jelszó alaphelyzetbe állítása Windows XP rendszeren...
12. Kattintson a "létrehozás" gombra.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>A tartományon kívüli forgatókönyvek betanítási párbeszédpanelei

1. A bal oldali panelen kattintson a "betanítási párbeszédpanelek" elemre, majd a meglévő "Toy Stores" ("játékbolt") gombra.
2. A csevegés panelen kattintson a "Toy Stores" kifejezésre.
3. Az "alternatív bemenet hozzáadása..." mezőjébe írja be a "Web Search" kifejezést, és nyomja le az ENTER billentyűt.
4. Az "alternatív bemenet hozzáadása..." mezőbe írja be a "Flight Booking" kifejezést, és nyomja le az ENTER billentyűt.
5. Kattintson a "módosítások mentése" gombra.
6. Kattintson a "szerkesztés mentése" gombra.
7. A bal oldali panelen kattintson a "naplózási párbeszédpanelek" elemre, majd az "új napló párbeszédpanel" gombra.
8. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "nem találom a jelszavam"
9. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a következőt: "Microsoft-fiók"
10. A csevegés panelen, ahol a "beírja az üzenetet..." kifejezést, írja be a következőt: "Thanks"
11. Kattintson a "kész tesztelés" gombra.
12. Kattintson a "nem találom a jelszavam" napló párbeszédpanelt a rács nézetből.
13. A csevegés panelen kattintson a nem megfelelően megjelenített "megoldásra: A Microsoft-fiók jelszavának alaphelyzetbe állítása "válasz.
14. Kattintson a "+ művelet" gombra.
15. A "bot válaszában..." mezőbe írja be az "Ön szívesen" kifejezést.
16. Kattintson a "létrehozás" gombra.
17. Kattintson a "Mentés a betanításra" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató – pizza Order](./demo-pizza-order.md)
