---
title: A hangasszisztensek gyakran feltett kérdések
titleSuffix: Azure Cognitive Services
description: Válaszok a hangasszisztensekkel kapcsolatos legnépszerűbb kérdésekre az egyéni parancsok (előzetes verzió) vagy a Közvetlen vonal beszédcsatornája használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 8480299c2c889a243150028ac9651f4b62656aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110342"
---
# <a name="voice-assistants-frequently-asked-questions"></a>A hangasszisztensek gyakran feltett kérdések

Ha ebben a dokumentumban nem talál választ kérdéseire, olvassa el [az egyéb támogatási lehetőségeket.](support.md)

## <a name="general"></a>Általános kérdések

**K: Mi az a hangasszisztens?**

**A.** Cortanához hasonlóan a hangasszisztens is olyan megoldás, amely figyeli a felhasználó kimondott kimondott szövegét, elemzi a kimondott szövegek tartalmát a jelentés szempontjából, egy vagy több műveletet hajt végre az utterance (kifejezés) szándékára válaszul, majd választ ad a felhasználónak, amely gyakran tartalmaz egy szóbeli összetevőt. Ez egy "voice-in, voice-out" tapasztalat kölcsönhatásban áll a rendszerrel. A hangsegéd-szerzők a beszédfelismerési `DialogServiceConnector` SDK-ban lévő alkalmazással hoznak létre egy egyéni [parancsokkal (előzetes verzióval)](custom-commands.md) vagy a Bot Framework Közvetlen [vonalbeszédcsatornával](direct-line-speech.md) létrehozott segéddel való kommunikációhoz. Ezek az asszisztensek egyéni kulcsszavakat, egyéni beszédfelismerést és egyéni hangot használhatnak a márkához vagy termékhez igazított élmény biztosításához.

**K: Egyéni parancsokat (előzetes verzió) vagy közvetlen vonalú beszédfelismerést használjak? Mi a különbség?**

**A:** [Az egyéni parancsok (előzetes verzió)](custom-commands.md) egy kisebb összetettségű eszközkészlet, amely könnyen létrehozhat és üzemeltethet egy olyan segédet, amely jól illeszkedik a feladatok befejezéséhez. [A Direct Line Speech](direct-line-speech.md) gazdagabb, kifinomultabb funkciókat biztosít, amelyek robusztus társalgási forgatókönyveket tesznek lehetővé. További információt az [asszisztensi megoldások összehasonlításában](voice-assistants.md#choosing-an-assistant-solution) talál.

**K: Hogyan kezdhetek?**

**A.** A legjobb módja annak, hogy először hozzon létre egy egyéni parancsok (előzetes verzió) alkalmazás vagy alapvető Bot Framework bot.

- [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása](quickstart-custom-speech-commands-create-new.md)
- [Hozzon létre egy alapvető Bot Framework bot bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
- [Robot csatlakoztatása a Közvetlen vonal beszédcsatornájához](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Hibakeresés

**K: Hol van a csatornatitkom?**

**A.** Ha már használta a Direct Line Speech előzetes verzióját, vagy a kapcsolódó dokumentációt olvassa, előfordulhat, hogy egy titkos kulcsot talál a Direct Line Speech channel regisztrációs lapján. A beszédsdka 1.7-es `DialogServiceConfig` gyári metódusa `FromBotSecret` is ezt az értéket várja.

A Direct Line Speech legújabb verziója leegyszerűsíti a robottal való kapcsolatfelvétel folyamatát egy eszközről. A csatorna regisztrációs lapján a felső legördülő lista a közvetlen vonalbeszéd-csatorna regisztrációját egy beszédfelismerési erőforráshoz társítja. A társkezelés után a v1.8-as beszédstidk tartalmaz egy `BotFrameworkConfig::FromSubscription` gyári módszert, amely konfigurálja `DialogServiceConnector` a kapcsolatot az előfizetéshez társított robottal.

Ha még mindig az ügyfélalkalmazást az 1.7-es verzióról `DialogServiceConfig::FromBotSecret` az 1.8-as verzióra telepíti át, előfordulhat, hogy továbbra is nem üres, nem null értékkel működik a csatornatitkos paraméterhez, például az előző titkos kulcsot. A rendszer egyszerűen figyelmen kívül hagyja, ha egy újabb csatornaregisztrációhoz társított beszéd-előfizetést használ. Kérjük, vegye figyelembe, hogy az _értéknek_ nem null értékűnek és nem üresnek kell lennie, mivel ezeket az eszközön ellenőrzi, mielőtt a szervizoldali társítás releváns lenne.

Részletesebb útmutatóért tekintse meg a csatornaregisztrációt bemutató [részt.](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel)

**K: Kapok egy 401 hiba, amikor csatlakozik, és semmi sem működik. Tudom, hogy a beszéd-előfizetési kulcs omlazsa. mi történik?**

**A.** Az Előfizetés azure-portálon történő kezelése során győződjön meg arról, hogy a Beszédfelismerés-erőforrást (Microsoft.CognitiveServicesSpeechServices, "Speech") használja, és _nem_ a **Cognitive Services-erőforrást** (Microsoft.CognitiveServicesAllInOne, "Minden Kognitív szolgáltatás"). **Speech** Ellenőrizze a [beszédszolgáltatás régióban a hangasszisztensek támogatását](regions.md#voice-assistants)is.

![helyes előfizetés a közvetlen vonalbeszédhez](media/voice-assistants/faq-supported-subscription.png "példa kompatibilis beszédfelismerési előfizetésre")

**K: Kapok felismerés szöveget `DialogServiceConnector`vissza az én , de látok egy "1011" hiba, és semmi az én bot. miért?**

**A.** Ez a hiba kommunikációs problémára utal a segéd és a hangasszisztens-szolgáltatás között.

- Egyéni parancsok (előzetes verzió) esetén győződjön meg arról, hogy az egyéni parancsok (előzetes verzió) alkalmazás közzététele
- A közvetlen vonalbeszéd, győződjön meg arról, hogy [csatlakoztatta a robot a Direct Line Speech channel,](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) [hozzáadta a streaming protokoll támogatása](https://aka.ms/botframework/addstreamingprotocolsupport) a bot (a kapcsolódó Web Socket támogatás), majd ellenőrizze, hogy a robot válaszol a bejövő kérelmeket a csatornáról.

**K: Ez a kód még mindig nem működik, és/ vagy `DialogServiceConnector`kapok egy másik hiba használatakor . Mit kell tennem?**

**A.** A fájlalapú naplózás lényegesen részletesebb, és segít felgyorsítani a támogatási kérelmeket. A funkció engedélyezéséhez olvassa el [a fájlnaplózás használatát.](how-to-use-logging.md)

## <a name="next-steps"></a>További lépések

- [hibaelhárítással](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)
