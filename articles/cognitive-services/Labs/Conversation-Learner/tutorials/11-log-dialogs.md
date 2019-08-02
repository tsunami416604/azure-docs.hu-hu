---
title: Párbeszédpanelek naplózása Conversation Learner modellben – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan naplózhat párbeszédpaneleket egy Conversation Learner modellben.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 388942e8195048d7528a54e5a290f1724c8e876b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703976"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Párbeszédpanelek naplózása Conversation Learner modellben

Ez az oktatóanyag azt mutatja be, hogyan használhatók a napló párbeszédpanelek, hogy jobban betanítsák Conversation Learner modelleket a valós felhasználók által készített rögzített interakciókkal.

## <a name="video"></a>Videó

[![A log párbeszédablakok oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

és az előző oktatóanyagokban létrehozott időjárási modellt.

## <a name="details"></a>Részletek
A naplók párbeszédpanelei rögzítik a robot interakcióját a végfelhasználók számára. A naplófájlok kihasználása révén kijavíthatja az entitások feliratait és a műveleti beállításokat a modell teljesítményének és a rendszer teljes teljesítményének javítása érdekében.

## <a name="steps"></a>Lépések

A webes felhasználói felületen kattintson az "oktatóanyagok importálása" elemre, és válassza ki a "tutorial-11-LogDialogs" nevű modellt.

Ez a modell egy "város" nevű entitást tartalmaz, és olyan műveletek, amelyek arra lettek kialakítva, hogy válaszoljanak az adott város időjárásával kapcsolatos kérdésekre. A modell betanításához két vonat-párbeszédpanelt használtak, így a teljesítményre vonatkozó elvárások némileg alacsonyak. A modell további képzéssel és a valós világ felhasználói interakciókkal való kitettséggel fog javulni.

### <a name="create-a-new-conversation"></a>Új beszélgetés létrehozása

1. A bal oldali panelen kattintson a "naplózási párbeszédpanelek" elemre, majd az "új napló párbeszédpanel" gombra.
2. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a "Austin Weather Forecast" kifejezést.
3. Kattintson a "kész tesztelés" gombra.
4. Kattintson a "Austin Weather Forecast" naplóra a listából.
5. Kattintson a "Austin Weather Forecast" kifejezésre a csevegés panelen.
6. Kattintson az "Austin", majd a "város" elemre az entitások listájáról.
7. Kattintson a "módosítások elküldése" gombra.
    - Az entitás értékének változása a beszélgetés alsóbb szintű módosításait eredményezi, mivel a memóriában új entitási értékek vannak. A későbbi műveletek valószínűleg érvénytelenek lesznek, különösen a "város" entitást is beleértve.
8. Kattintson a "melyik városra?" a csevegési panelen való Kimondás.
9. Válassza ki a választ: "az idő az Austinban valószínűleg napos."
10. Kattintson a "Mentés a betanításra" gombra.
    - A képzés azonnal kiindul

![](../media/T11_logdialog.png)

Egy utolsó megjegyzés. Az üzleti igényektől függően a beszélgetés naplózási funkciója kikapcsolható a beállítások lehetőségre kattintva, és a "naplóbeli beszélgetések" jelölésének törlésével.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitás észlelésének visszahívása](./12-entity-detection-callback.md)
