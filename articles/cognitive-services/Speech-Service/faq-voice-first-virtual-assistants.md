---
title: A közvetlen vonal Speech kapcsolatos gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Hang-és felhőközpontú virtuális asszisztensek használatával a közvetlen vonal beszédfelismerő csatornát legnépszerűbb kérdésekre adott válaszok.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: bd7e1f87fea03d0aac7fd9f746b777e3b15e917e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606644"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Hang-és felhőközpontú virtuális asszisztensek előzetes verzió: Gyakori kérdések

Ha a kérdésekre adott válaszok a jelen dokumentum nem találja, tekintse meg az [más támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: Mit jelent a közvetlen vonal Speech?**

**V:** A `DialogServiceConnector` beszéd SDK biztosítja a kétirányú, aszinkron kommunikációt, amely kapcsolódik a közvetlen vonal beszédfelismerő csatornát a Bot Framework a robotokat. Ez a csatorna koordinált hozzáférést a hang-szöveg és a szöveg-hang transzformációs Azure Speech Services, robotok lesz teljes mértékben a hangalapú, hangtípus végre természetes nyelvi funkciókat biztosít. Ébresztési szavakat és a Word-ellenőrzési ébresztési támogatása révén a megoldás lehetővé teszi hozhat létre nagymértékben testre szabható hang-és felhőközpontú virtuális asszisztensek a márkáról vagy termék.

**K: Hogyan kezdhetem?**

**V:** A legjobb módja kezd létrehozása egy hang-és felhőközpontú virtuális asszisztensek van a következővel kell kezdődnie [egy alapszintű Bot Framework robot létrehozásának](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Ezután csatlakoztassa a robot a [közvetlen vonal beszédfelismerő csatornát](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Hibakeresés

**K: 401 – hibaüzenetet kapom kapcsolódáskor, és semmi nem működik. Tudom a speech előfizetési kulcs érvénytelen. mi történik?**

**V:** Előzetes verzióban elérhető közvetlen vonal Speech melyik előfizetések használhatja adott korlátozásokkal rendelkezik. Győződjön meg arról, használja a **Speech** erőforrás (Microsoft.CognitiveServicesSpeechServices, "Speech") és *nem* a **Cognitive Services** erőforrás () Microsoft.CognitiveServicesAllInOne, "A Cognitive Services összes"). Csak [beszédszolgáltatások régiók részhalmazát](regions.md#voice-first-virtual-assistants) közvetlen vonal Speech jelenleg támogatja.

![Javítsa ki az előfizetést a közvetlen vonal beszédfelismerés](media/voice-first-virtual-assistants/faq-supported-subscription.png "kompatibilis Speech előfizetés – példa")

**K: Vissza kapok felismerés szöveget a közvetlen vonal Speech, de egy "1011" hiba történt, és semmi nem látom a saját robot. Hogy miért?**

**V:** Ez a hiba azt jelzi, hogy a robot és a közvetlen vonal Speech közötti kommunikációs probléma. Győződjön meg arról, hogy [csatlakoztatva a közvetlen vonal beszédfelismerő csatornát](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [Streamelési protokoll támogatása a](https://aka.ms/botframework/addstreamingprotocolsupport) , majd ellenőrizze, hogy a robot válaszol a bejövő és bot (a kapcsolódó Web Socket-támogatás) a csatorna kéréseit.

**K: Ez a kód továbbra sem működik, és/vagy más hiba kapok egy DialogServiceConnector használatakor. Mit tegyek?**

**V:** Fájlalapú naplózási közel teljes értékesítése miatt további részleteket biztosít, és támogatási kérések lerövidítve. Ez a funkció engedélyezéséhez tekintse [fájl naplózás használata](how-to-use-logging.md).

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás](troubleshooting.md)
* [Kibocsátási megjegyzések](releasenotes.md)
