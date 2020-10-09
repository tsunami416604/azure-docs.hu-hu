---
title: Szuverén felhők – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A szuverén felhők használatának megismerése
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78228085"
---
# <a name="speech-services-with-sovereign-clouds"></a>A Speech Services szuverén Felhőkkel

## <a name="azure-government-united-states"></a>Azure Government (Egyesült Államok)

Csak az Egyesült Államok szövetségi, állami, helyi és törzsi kormányai és partnerei férhetnek hozzá ehhez a dedikált példányhoz az Egyesült államokbeli állampolgárok által vezérelt műveletekkel.
- Régiók: US Gov Virginia
- SR a SpeechSDK-ben:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us", " \<your-key\> ");*
- TTS a SpeechSDK-ben: *config. FromHost ("https []() ://Virginia.TTS.Speech.Azure.us", " \<your-key\> ");*
- Hitelesítési jogkivonatok: https []() ://Virginia.API.Cognitive.microsoft.us/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Custom Speech portál: https://virginia.cris.azure.us/Home/CustomSpeech
- Rendelkezésre álló SKU-i: S0
- Támogatott funkciók:
  - Speech-to-Text
  - Custom Speech (akusztikus/nyelvi adaptáció)
  - Text-to-Speech
  - Speech Translator
- Nem támogatott funkciók
  - Egyéni hang
  - Neurális hangok a szöveg és a beszéd között
- Támogatott területi beállítások: a következő nyelvekhez tartozó területi beállítások támogatottak.
  - Arab (AR-*)
  - Kínai (zh-*)
  - Angol (en-*)
  - Francia (FR-*)
  - Német (de-*)
  - Hindi
  - Koreai
  - Orosz
  - Spanyol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Kínában

Kínában, egy Azure-adatközpontban található, amely közvetlen hozzáférést biztosít a China Mobile, a China Telecom, a China Unicom és más nagy fuvarozói gerincű hálózathoz, a kínai felhasználók számára nagy sebességű és stabil helyi hálózati hozzáférési élményt biztosít.
- Régiók: Kelet-Kína 2 (Shanghai)
- SR a SpeechSDK-ben: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn", " \<your-key\> ");*
- TTS a SpeechSDK-ben:  *config. FromHost ("https []() ://chinaeast2.TTS.Speech.Azure.cn", " \<your-key\> ");*
- Hitelesítési jogkivonatok: https []() ://chinaeast2.API.Cognitive.Azure.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Custom Speech portál: https://speech.azure.cn/CustomSpeech
- Rendelkezésre álló SKU-i: S0
- Támogatott funkciók:
  - Speech-to-Text
  - Custom Speech (akusztikus/nyelvi adaptáció)
  - Text-to-Speech
  - Speech Translator
- Nem támogatott funkciók
  - Egyéni hang
  - Neurális hangok a szöveg és a beszéd között
- Támogatott területi beállítások: a következő nyelvekhez tartozó területi beállítások támogatottak.
  - Arab (AR-*)
  - Kínai (zh-*)
  - Angol (en-*)
  - Francia (FR-*)
  - Német (de-*)
  - Hindi
  - Koreai
  - Orosz
  - Spanyol (es-*)

