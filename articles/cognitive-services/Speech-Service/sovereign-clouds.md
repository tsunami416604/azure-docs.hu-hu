---
title: Szuverén felhők - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan kell használni a szuverén felhőket
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228085"
---
# <a name="speech-services-with-sovereign-clouds"></a>Beszédszolgáltatások szuverén felhőkkel

## <a name="azure-government-united-states"></a>Azure Government (Egyesült Államok)

Csak az Egyesült Államok szövetségi, állami, helyi és törzsi kormányai és partnerei férhetnek hozzá ehhez a célzott esethez, az átvizsgált amerikai polgárok által ellenőrzött műveletekkel.
- Régiók: US Gov Virginia
- SR a SpeechSDK-ben:*config. FromHost("wss://virginia.stt.speech.azure.us",\<" your-key\>");*
- TTS a SpeechSDK: *config. FromHost("https[]()://virginia.tts.speech.azure.us",\<"\>your-key ");*
- Hitelesítési jogkivonatok: https://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken[]()
- Azure Portal:https://portal.azure.us  
- Egyéni beszédportál:https://virginia.cris.azure.us/Home/CustomSpeech
- Elérhető SKUs: S0
- Támogatott funkciók:
  - Diktálás
  - Egyéni beszéd (akusztikus/nyelvi adaptáció)
  - Szövegfelolvasás
  - Beszédfordító
- Nem támogatott szolgáltatások
  - Egyéni hang
  - Neurális hangok a szövegfelolvasáshoz
- Támogatott területi beállítások: A következő nyelvek területi beállításai támogatottak.
  - Arab (ar-*)
  - Kínai (zh-*)
  - Angol (en-*)
  - Francia (fr-*)
  - Német (de-*)
  - Hindi
  - Koreai
  - Orosz
  - Spanyol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Kína

A Kínában található Azure-adatközpont közvetlen hozzáféréssel rendelkezik a China Mobile,a China Telecom, a China Unicom és más jelentős szolgáltatói gerinchálózathoz, amely lehetővé teszi a kínai felhasználók számára, hogy nagy sebességű és stabil helyi hálózati hozzáférési élményt nyújtsanak.
- Régiók: Kína Kelet 2 (Sanghaj)
- SR a SpeechSDK-ben: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<" your-key\>");*
- TTS a SpeechSDK: *config. FromHost("https[]()://chinaeast2.tts.speech.azure.cn",\<"\>your-key ");*
- Hitelesítési tokenek: https://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken[]()
- Azure Portal:https://portal.azure.cn
- Egyéni beszédportál:https://speech.azure.cn/CustomSpeech
- Elérhető SKUs: S0
- Támogatott funkciók:
  - Diktálás
  - Egyéni beszéd (akusztikus/nyelvi adaptáció)
  - Szövegfelolvasás
  - Beszédfordító
- Nem támogatott szolgáltatások
  - Egyéni hang
  - Neurális hangok a szövegfelolvasáshoz
- Támogatott területi beállítások: A következő nyelvek területi beállításai támogatottak.
  - Arab (ar-*)
  - Kínai (zh-*)
  - Angol (en-*)
  - Francia (fr-*)
  - Német (de-*)
  - Hindi
  - Koreai
  - Orosz
  - Spanyol (es-*)

