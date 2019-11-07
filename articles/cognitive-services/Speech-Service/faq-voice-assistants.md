---
title: A hangasszisztensekkel kapcsolatos gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Választ kaphat a hangasszisztensekkel kapcsolatos legnépszerűbb kérdésekre az egyéni parancsok (előzetes verzió) vagy a közvetlen vonalas beszéd csatorna használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: eccf2a7a1b9c7ea7a21cd5d0cf0f60728284c05d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579667"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Hangvezérelt asszisztensek: gyakori kérdések

Ha a jelen dokumentumban nem talál választ a kérdéseire, tekintse meg az [egyéb támogatási lehetőségeket](support.md).

## <a name="general"></a>Általános kérdések

**K: Mi az a hangsegéd?**

**A:** A Cortana hasonlóan a hangsegéd olyan megoldás, amely figyeli a felhasználó által kimondott hosszúságú kimondott szöveg, elemzi a jelentésekben szereplő hosszúságú kimondott szöveg tartalmát, a teljes szándékra válaszul egy vagy több műveletet hajt végre, majd választ ad a felhasználónak, amely gyakran egy szóbeli összetevőt tartalmaz. Ez egy "hangvételt, hangvételt" kínál a rendszerekkel való interakcióhoz. a hangasszisztens szerzők létrehoznak egy on-Device alkalmazást a Speech SDK `DialogServiceConnector` használatával, hogy az [Egyéni parancsokkal (előzetes verzió)](custom-commands.md) vagy a bot Framework [közvetlen vonalas beszédfelismerési](direct-line-speech.md) csatornával létrehozott asszisztenssel kommunikáljanak. Ezek az asszisztensek egyéni kulcsszavakat, egyéni beszédeket és egyéni hangvételt használhatnak, hogy a márka vagy termék számára szabott élményt nyújtsanak.

**K: használhatok egyéni parancsokat (előzetes verzió) vagy közvetlen vonalas beszédet? Mi a különbség?**

**A:** az [egyéni parancsok (előzetes verzió)](custom-commands.md) egy alacsonyabb bonyolultságú eszközkészletet biztosítanak, amellyel könnyedén létrehozhatók és beállíthatók a feladat-végrehajtási forgatókönyvekhez jól illeszkedő asszisztensek. A [közvetlen vonalas beszéd](direct-line-speech.md) gazdagabb és kifinomultabb képességeket biztosít, amelyek lehetővé teszik robusztus társalgási forgatókönyvek használatát. További információért lásd a [segéd-megoldások összehasonlítását](voice-assistants.md#choosing-an-assistant-solution) ismertető témakört.

**K: Hogyan első lépések?**

**A:** A legjobb módszer az egyéni parancsok (előzetes verzió) alkalmazás vagy az alapszintű bot Framework bot létrehozásához.

* [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása](quickstart-custom-speech-commands-create-new.md)
* [Alapszintű robot-keretrendszer robot létrehozása](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Robot összekötése a közvetlen vonalas beszéd csatornával](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Hibakeresés

**K: hol van a Channel Secret?**

**A:** Ha a Direct line Speech előzetes verzióját használta, vagy a kapcsolódó dokumentációt olvasta, előfordulhat, hogy a Direct line Speech Channel regisztrációs lapján talál egy titkos kulcsot. A Speech SDK-ban a v 1.7 `DialogServiceConfig` Factory metódus `FromBotSecret` is ezt az értéket várja.

A Direct line Speech legújabb verziója leegyszerűsíti a robot egy eszközről való kapcsolatfelvételi folyamatát. A Channel Registration (csatorna regisztrálása) lapon a felül lévő legördülő lista a Direct line Speech Channel-regisztrációt egy beszédfelismerési erőforrással társítja. Ha társítva van, a v 1.8 Speech SDK tartalmaz egy `BotFrameworkConfig::FromSubscription` Factory metódust, amely egy `DialogServiceConnector` konfigurál, hogy kapcsolatba lépjen az előfizetéshez társított robottal.

Ha továbbra is áttelepíti az ügyfélalkalmazás alkalmazást a v 1.7-ről a v 1.8-ra, `DialogServiceConfig::FromBotSecret` továbbra is dolgozhat a Channel Secret paraméteréhez nem üres, nem null értékű értékkel, például a korábban használt titokkal. Ezt a rendszer egyszerűen figyelmen kívül hagyja, ha egy újabb csatornás regisztrációhoz társított beszédfelismerési előfizetést használ. Vegye figyelembe, hogy az érték *nem lehet null* és nem üres, mivel ezeket a rendszer az eszközön a szolgáltatási oldal társítása előtt ellenőrzi.


Részletesebb útmutatásért tekintse meg az [oktatóanyag című szakaszt](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) , amely végigvezeti a csatorna regisztrálásán.

**K: 401-es hibaüzenetet kapok a csatlakozáskor, és semmi nem működik. Tudom, hogy a Speech előfizetés kulcsa érvényes. mi történik?**

**A:** Az előfizetésnek a Azure Portal való kezelésekor ügyeljen arra, hogy a **beszédfelismerési** erőforrást (Microsoft. CognitiveServicesSpeechServices, "Speech") használja, és *ne* a **Cognitive Services** erőforrást ( Microsoft. CognitiveServicesAllInOne, "All Cognitive Services"). Kérjük, tekintse [meg a beszédfelismerési szolgáltatási régió támogatását a hangasszisztensek számára](regions.md#voice-assistants).

![megfelelő előfizetés a közvetlen vonalas beszédhez](media/voice-assistants/faq-supported-subscription.png "kompatibilis beszédfelismerési előfizetés – példa")

**K: a `DialogServiceConnector`em a felismerési szöveget, de "1011" hibát látok, és nem a saját robotom. miért?**

**A:** Ez a hiba kommunikációs problémát jelez a segéd és a hangsegéd szolgáltatás között.

* Egyéni parancsok (előzetes verzió) esetén győződjön meg arról, hogy az egyéni parancsok (előzetes verzió) alkalmazás közzé van téve
* A közvetlen vonalas beszédhez győződjön meg arról, hogy [csatlakoztatta a robotot a közvetlen vonalas hangcsatornához](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [hozzáadta a folyamatos átviteli protokoll támogatását](https://aka.ms/botframework/addstreamingprotocolsupport) a robothoz (a kapcsolódó webes szoftvercsatorna-támogatással), majd ellenőrizze, hogy a robot válaszol-e a bejövő kérelmekre a csatornáról.

**K: Ez a kód továbbra sem működik, és/vagy egy `DialogServiceConnector`használatakor más hibaüzenetet kapok. Mit tegyek?**

**A:** A fájl alapú naplózás lényegesen több részletet biztosít, és a támogatási kérelmek felgyorsítását is segíti. A funkció engedélyezéséhez tekintse meg a [fájlok naplózásának használata](how-to-use-logging.md)című témakört.

## <a name="next-steps"></a>További lépések

* [hibaelhárítással](troubleshooting.md)
* [Kibocsátási megjegyzések](releasenotes.md)
