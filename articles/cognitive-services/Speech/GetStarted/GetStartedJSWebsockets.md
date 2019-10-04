---
title: Ismerkedés a Bing Speech-felismerési API-val a JavaScriptben | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Cognitive Services a Bing Speech felismerési API-val fejlesztheti azokat az alkalmazásokat, amelyek folyamatosan szöveggé alakítják a beszélt hangot.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965815"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Ismerkedés a Speech Recognition API-val a JavaScriptben

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A beszédfelismerési API használatával kifejlesztheti azokat az alkalmazásokat, amelyek a beszélt hangot szöveggé alakítják. A JavaScript ügyféloldali kódtár a [Speech Service WebSocket protokollt](../API-Reference-REST/websocketprotocol.md)használja, amely lehetővé teszi az átirattal rendelkező szöveg egyidejű beszélgetését és fogadását. Ez a cikk segítséget nyújt a beszédfelismerési API JavaScript-ben való használatának megkezdéséhez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Feliratkozás a Speech Recognition API-ra, és ingyenes próbaverziós előfizetési kulcs beszerzése

A Speech API Cognitive Services része. A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

> [!IMPORTANT]
> Előfizetési kulcs beszerzése. A Speech Client kódtárak használata előtt rendelkeznie kell egy [előfizetési kulccsal](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Bevezetés

Ebben a szakaszban végigvezeti a minta HTML-lapok betöltéséhez szükséges lépéseken. A minta a [GitHub-tárházban](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)található. **A mintát közvetlenül** a tárházból is megnyithatja, vagy **megnyithatja a mintát a tárház helyi példányáról** .

> [!NOTE]
> Néhány böngésző blokkolja a mikrofonhoz való hozzáférést a nem biztonságos forrásokon. Ezért javasoljuk, hogy a "Sample"/"Your app" szolgáltatást a HTTPS-en tárolja, hogy az összes támogatott böngészőben működjön.

### <a name="open-the-sample-directly"></a>A minta megnyitása közvetlenül

Szerezzen be egy előfizetési kulcsot a fentiekben leírtak szerint. Ezután nyissa meg a [minta hivatkozását](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Ezzel betölti a lapot az alapértelmezett böngészőbe (a [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)használatával megjelenítve).

### <a name="open-the-sample-from-a-local-copy"></a>A minta megnyitása helyi másolatból

A minta helyi kipróbálásához a következő adattár klónozása:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

fordítsa le az írógéppel-forrásokat, és csomagolja őket egyetlen JavaScript-fájlba ([NPM](https://www.npmjs.com/) kell telepíteni a számítógépre). Váltson át a klónozott tárház gyökerére, és futtassa a következő parancsokat:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Nyissa meg `samples\browser\Sample.html` a kedvenc böngészőjét.

## <a name="next-steps"></a>További lépések

További információ az SDK saját weboldalra való felvételéről [itt](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)található.

## <a name="remarks"></a>Megjegyzések

- A Speech Recognition API három [felismerési módot](../concepts.md#recognition-modes)támogat. A módot a sample. html fájlban található **Setup ()** függvény frissítésével állíthatja át. A minta alapértelmezés `Interactive` szerint beállítja a módot. A mód módosításához frissítse a paramétert `SR.RecognitionMode.Interactive` egy másik módra. Módosítsa például a paramétert `SR.RecognitionMode.Conversation`a következőre:.
- A támogatott nyelvek teljes listáját lásd: [támogatott nyelvek](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Kapcsolódó témakörök

- [JavaScript Speech Recognition API minta adattár](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Ismerkedés a REST API](GetStartedREST.md)
