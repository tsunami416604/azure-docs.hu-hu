---
title: Bemutató Beszélgetéstanuló modell, jelszó-visszaállítási – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre egy bemutató Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6436193dadb5933074fdce861a31672a0f4849de
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227335"
---
# <a name="demo-password-reset"></a>Bemutató: Új jelszó létrehozása
Ez az oktatóanyag bemutatja egy egyszerű technikai támogatás robot, amelyek segíthetnek a jelszó-visszaállítási Beszélgetéstanuló működteti. A robot modell további folyamatok nem triviális párbeszédpanel, és több kapcsolja többek között az out-a-domain osztályok feladatütemezések. A feladat kód vagy entitások nélkül is elvégezhető.

## <a name="video"></a>Videó

[![Bemutató jelszó előzetes verzió](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a jelszó alaphelyzetbe állítása robotot

    npm run demo-password

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a oktatóanyag bemutató jelszó alaphelyzetbe állítása. 

### <a name="actions"></a>Műveletek

A modell tartalmazza, amelyek segítségével a felhasználók közös jelszó kapcsolatos problémák megoldásához műveletek egy csoportját.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek

A modell is tartalmaz a több Train párbeszédpanelek, köztük olyanokat, amelyek bemutatják, tartományi osztály képzési kívül. Például a felhasználók, akik kérhet például útvonalak. A minta robot tanított bemutatási célokra néhány, és egyszerűen reagál, megállapításával "nem segíteni az adott." A meglévő Train-párbeszédpanelekhez listája a bal oldali panelen található a "Train-párbeszédpanelekhez".

![](../media/tutorial_pw_reset_entities.PNG)

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", írja be az "i jelszó elvész."
3. A "Score műveletek" gombra.
4. Válassza ki a választ, az "Is, amely a helyi fiók vagy a Microsoft-fiókkal?"
5. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", írja be a "helyi fiók."
6. A "Score műveletek" gombra.
7. Válassza ki a választ, a "Windows melyik verziója van?"
8. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "a windows XP" típusa
9. A "Score műveletek" gombra.
10. Kattintson a "+ művelet" gombra.
11. A "Bot a válaszban..." mezőbe írja be a "MEGOLDÁST: Útmutató a Windows XP-jelszó alaphelyzetbe állítása..."
12. A "Létrehozás" gombra.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Képzési párbeszédpanelek Out-a-Domain-forgatókönyvek

1. A bal oldali panelen, kattintson a "Train-párbeszédpanelekhez", majd a meglévő "kisfia stores" Train párbeszédpanel.
2. A Csevegés panelen kattintson a "kisfia stores" utterance (kifejezés).
3. "Hozzáadása alternatív beviteli...", "webes keresés" típust, majd nyomja le adja meg a mezőben.
4. "Hozzáadása alternatív beviteli...", "repülési foglalási" típust, majd nyomja le adja meg a mezőben.
5. A "Módosítások mentése" gombra.
6. Kattintson a "Mentés szerkesztése" gombra.
7. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez", majd az "új Log" gomb.
8. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", az "i a jelszó nem található" típusa
9. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Microsoft-fiók" típusra
10. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Köszönjük" típusra
11. Kattintson a "Végzett tesztelése" gombra.
12. Kattintson az "i a jelszó nem található" log párbeszédpanel a rácsnézet.
13. A Csevegés panelen kattintson a helytelenül megjelenített "megoldást: Útmutató a Microsoft-fiókja jelszavát alaphelyzetbe állítása"választ.
14. Kattintson a "+ művelet" gombra.
15. A "Bot a válaszban..." mezőbe írja be a "Teljesítése esetén nyugodtan"
16. A "Létrehozás" gombra.
17. A "Mentés másként Train párbeszédpanel" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bemutató – kétpizzás sorrend](./demo-pizza-order.md)
