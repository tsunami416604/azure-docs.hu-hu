---
title: Bejelentkezés párbeszédpanelek Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan párbeszédpanelek jelentkezzen be egy Beszélgetéstanuló modellt.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 74ee04fb3d4044573a98535a9bdc26d5c593a222
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796807"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Bejelentkezés párbeszédpanelek Beszélgetéstanuló modell

Ez az oktatóanyag bemutatja, hogyan Log párbeszédpanelek alkalmazzák jobb a rögzített interakciók a való világból bármit felhasználókkal való Beszélgetéstanuló modelleket taníthat be.

## <a name="video"></a>Videó

[![Napló párbeszédpanelek oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

valamint az időjárási az előző oktatóanyagokban létrehozott.

## <a name="details"></a>Részletek
Napló párbeszédpanelek olyan végfelhasználók számára a robot kezelésének rögzített naplók. Ezek Log párbeszédpanelek harnessing megoldhatja, entitás címkék és a műveleti beállításokat jobb a modell teljesítmény és a rendszer általános teljesítménye.

## <a name="steps"></a>Lépések

A webes felhasználói felületen kattintson a "Oktatóanyagokban importálása", és válassza a "Az oktatóanyag-11-LogDialogs" nevű modell.

Ez a modell "city" nevű egy entitást tartalmaz, és az adott városban időjárási kapcsolatos lekérdezések megválaszolására tervezett műveletek. A modell betanításához, ezért a teljesítmény elvárások némileg alacsony két betanításához párbeszédpanelek használták. A modell javíthatják a további betanítási és a valós felhasználói interakció érdekében.

### <a name="create-a-new-conversation"></a>Hozzon létre egy új téma

1. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez", majd az "új Log" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "Austin időjárás-előrejelzés" típusra
3. Kattintson a "Végzett tesztelése" gombra.
4. Kattintson a "Austin időjárás-előrejelzés" log párbeszédpanel a listából.
5. Kattintson a csevegési panel "Austin időjárás-előrejelzés" utterance (kifejezés).
6. Kattintson a "Austin", majd kattintson a "city" entitás listájából.
7. A "Módosítások elküldése" gombra.
    - Ez a változás az entitás érték a beszélgetés alsóbb rétegbeli módosításai miatt, óta új entitásértékek rendelkezünk a memóriában. Későbbi műveletekben valószínűleg érvénytelen váltak különösen megjelennek a "city" entitás használata esetén.
8. Kattintson a "Melyik városban?" a Csevegés panelen utterance.
9. Válassza ki a választ, a "Austin az időjárás valószínűleg napsütéses."
10. A "Mentés másként Train párbeszédpanel" gombra.
    - Képzési azonnal megkezdődik

Utolsó megjegyzés. Üzleti szükségleteinek a beszélgetés naplózási funkció kikapcsolható úgy a beállításokat és az eszközhitelesítést "beszélgetések Log."

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás észlelési visszahívási](./12-entity-detection-callback.md)
