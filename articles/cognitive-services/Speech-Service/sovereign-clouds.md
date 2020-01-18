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
ms.openlocfilehash: 3b96ded52aa2148df9e96d6f1e878d50b821abab
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170467"
---
# <a name="speech-services-with-sovereign-clouds"></a>A Speech Services szuverén Felhőkkel

## <a name="azure-government-united-states"></a>Azure Government (Egyesült Államok)

Ezt a dedikált példányt csak az USA szövetségi, állami, helyi vagy törzsi kormányzati szervei és azok partnerei vehetik igénybe, a kezelést pedig alapos szűrésen átesett amerikai személyzet irányítja.
- Régiók: US Gov Virginia
- SR a SpeechSDK-ben:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us", "\<saját kulcsú\>");*
- TTS a SpeechSDK-ben: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.us", "\<saját kulcsú\>");*
- Hitelesítési jogkivonatok:[]()https://Virginia.API.Cognitive.microsoft.us/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Custom Speech-portál: https://virginia.cris.azure.us/Home/CustomSpeech
- Rendelkezésre álló SKU-i: S0
- Támogatott funkciók:
  - Hang-szöveg transzformáció
  - Custom Speech (akusztikus/nyelvi adaptáció)
  - Szöveg-hang transzformáció
  - Speech Translator
- Nem támogatott funkciók
  - Egyéni beszédhang
  - Neurális hangok a szöveg és a beszéd között
- Támogatott területi beállítások: a következő nyelvekhez tartozó területi beállítások támogatottak.
  - Arab (AR-*)
  - Kínai (zh-*)
  - Angol (en-*)
  - Francia (FR-*)
  - Német (de-*)
  - hindi
  - koreai
  - orosz
  - Spanyol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

Kínában, egy Azure-adatközpontban található, amely közvetlen hozzáférést biztosít a China Mobile, a China Telecom, a China Unicom és más nagy fuvarozói gerincű hálózathoz, a kínai felhasználók számára nagy sebességű és stabil helyi hálózati hozzáférési élményt biztosít.
- Régiók: Kelet-Kína 2 (Shanghai)
- SR a SpeechSDK-ben: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn", "\<saját kulcsú\>");*
- TTS a SpeechSDK-ben: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn", "\<saját kulcsú\>");*
- Hitelesítési jogkivonatok:[]()https://chinaeast2.API.Cognitive.microsoft.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Custom Speech-portál: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Rendelkezésre álló SKU-i: S0
- Támogatott funkciók:
  - Hang-szöveg transzformáció
  - Custom Speech (akusztikus/nyelvi adaptáció)
  - Szöveg-hang transzformáció
  - Speech Translator
- Nem támogatott funkciók
  - Egyéni beszédhang
  - Neurális hangok a szöveg és a beszéd között
- Támogatott területi beállítások: a következő nyelvekhez tartozó területi beállítások támogatottak.
  - Arab (AR-*)
  - Kínai (zh-*)
  - Angol (en-*)
  - Francia (FR-*)
  - Német (de-*)
  - hindi
  - koreai
  - orosz
  - Spanyol (es-*)

