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
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 8427417c9b579c7dfa21f834ce1ca77099159eb2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072605"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Hang-és felhőközpontú virtuális asszisztensek előzetes verzió: Gyakori kérdések

Ha a kérdésekre adott válaszok a jelen dokumentum nem találja, tekintse meg az [más támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: Mit jelent a közvetlen vonal Speech?**

**V:** A `SpeechBotConnector` beszéd SDK biztosítja a kétirányú, aszinkron kommunikációt, amely kapcsolódik a közvetlen vonal beszédfelismerő csatornát a Bot Framework a robotokat. Ez a csatorna koordinált hozzáférést a hang-szöveg és a szöveg-hang transzformációs Azure Speech Services, robotok lesz teljes mértékben a hangalapú, hangtípus végre természetes nyelvi funkciókat biztosít. Ébresztési szavakat és a Word-ellenőrzési ébresztési támogatása révén a megoldás lehetővé teszi hozhat létre nagymértékben testre szabható hang-és felhőközpontú virtuális asszisztensek a márkáról vagy termék.

**K: Hogyan kezdhetem?**

**V:** A legjobb módja kezd létrehozása egy hang-és felhőközpontú virtuális asszisztensek van a következővel kell kezdődnie [egy alapszintű Bot Framework robot létrehozásának](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Ezután csatlakoztassa a robot a [közvetlen vonal beszédfelismerő csatornát](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Hibakeresés

**K: 401 – hibaüzenetet kapom kapcsolódáskor, és semmi nem működik. Tudom a speech előfizetési kulcs érvénytelen. mi történik?**

**V:** Előzetes verzióban elérhető a közvetlen vonal Speech rendelkezik jellemző korlátozásai használt előfizetés. Győződjön meg arról, használja a **Speech** erőforrás (Microsoft.CognitiveServicesSpeechServices, "Speech") és *nem* a **Cognitive Services** erőforrás () Microsoft.CognitiveServicesAllInOne, "A Cognitive Services összes"). Továbbá ne feledje, hogy csak a **westus2** régió jelenleg támogatott.

![Javítsa ki az előfizetést a közvetlen vonal beszédfelismerés](media/voice-first-virtual-assistants/faq-supported-subscription.png "kompatibilis Speech előfizetés – példa")

**K: Vissza kapok felismerés szöveget a közvetlen vonal Speech, de egy "1011" hiba történt, és semmi nem látom a saját robot. Hogy miért?**

**V:** Ez a hiba azt jelzi, hogy a robot és a közvetlen vonal Speech közötti kommunikációs probléma. Győződjön meg arról, hogy [csatlakoztatva a közvetlen vonal beszédfelismerő csatornát](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [Streamelési protokoll támogatása a](https://aka.ms/botframework/addstreamingprotocolsupport) , majd ellenőrizze, hogy a robot válaszol a bejövő és bot (a kapcsolódó Web Socket-támogatás) a csatorna kéréseit.

**K: Ez továbbra sem működik és/vagy más hiba kapok egy SpeechBotConnector használatakor pedig nem egyértelmű milyen feladatokat kell elvégezni. Mi *kell* elvégezni?**

**V:** Fájlalapú naplózási közel teljes értékesítése miatt további részleteket biztosít, és támogatási kérések lerövidítve. Ennek engedélyezéséhez tekintse meg a [fájl naplózás használata](how-to-use-logging.md).

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kibocsátási megjegyzések](releasenotes.md)
