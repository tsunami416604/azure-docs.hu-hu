---
title: A Microsoft beszéd felismerés API a JavaScript beolvasása használatába |} Microsoft Docs
description: Kognitív szolgáltatások a Microsoft beszéd felismerés API használatával, amely folyamatosan szóbeli hang konvertálható szöveges alkalmazások fejlesztéséhez.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 12/21/2017
ms.author: zhouwang
ms.openlocfilehash: 56c41fd7f6a00d80bc6bccd61894654e057e926e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347410"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>A beszédfelismerés felismerés API a JavaScript az első lépései

Az alkalmazásfejlesztések szóbeli hang szöveggé alakítani a beszédfelismerés felismerés API használatával. A JavaScript ügyféloldali kódtár használja a [beszéd szolgáltatás WebSocket protokoll](../API-Reference-REST/websocketprotocol.md), amelyik lehetővé teszi, talk és fogadhasson, egyszerre kért szöveg. Ez a cikk segítséget nyújt a beszédfelismerés felismerés API a JavaScript használatába.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>A beszédfelismerés felismerés API előfizetni, és egy ingyenes próba-előfizetés kulcs beszerzése

A hang transzformációs API kognitív szolgáltatások részét képezi. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

> [!IMPORTANT]
> Egy előfizetés kulcs beszerzése. Beszéd klienskódtárak használata előtt rendelkeznie kell egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Bevezetés

Ebben a szakaszban a Microsoft végigvezeti egy minta HTML-weblap betölteni a szükséges lépéseket. A minta található a [github-tárházban](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Is **közvetlenül nyissa meg a minta** a tárházból vagy **nyissa meg a minta egy helyi példány** a tárház. 

> [!NOTE]
> Egyes böngészők blokkolják a mikrofon hozzáférést a nem biztonságos eredet. Igen, javasoljuk, hogy a minta gazdagép / "az alkalmazás" az összes támogatott böngésző működéséhez https. 

### <a name="open-the-sample-directly"></a>Nyissa meg a minta közvetlenül

Szerezzen be egy előfizetés kulcs, fent leírt módon. Nyissa meg a [a minta mutató hivatkozás](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Ez a lap betöltve az alapértelmezett böngésző (használatával [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Nyissa meg a minta egy helyi példány

Próbálja meg a minta helyileg, ebben a tárházban klónozása:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Fordítsa le a géppel források és a köteg/browserfy őket egy JavaScript-fájl ([npm](https://www.npmjs.com/) a számítógépre telepíteni kell). Váltson át a klónozott tárház gyökérkönyvtárában, és futtassa a parancsokat:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Nyissa meg `samples\browser\Sample.html` kedvenc böngészőjével.

## <a name="next-steps"></a>További lépések

További információ a saját képernyőn látható weblapon amit fel lehetne venni az SDK-val való [Itt](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Megjegyzések

- A beszédfelismerés felismerés API támogatja három [felismerés módok](../concepts.md#recognition-modes). Frissítésével átválthatja a módot a **Setup()** függvény a Sample.html fájlban található. A minta a mód beállítása `Interactive` alapértelmezés szerint. Állítsa át a módot, frissítse a paraméter `SR.RecognitionMode.Interactive` módra. Módosítsa a paraméter megadásával például `SR.RecognitionMode.Conversation`.
- Támogatott nyelvek teljes listáját lásd: [támogatott nyelv](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Kapcsolódó témakörök

- [JavaScript Beszéd felismerés API minta tárház](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [A REST API-t az első lépései](GetStartedREST.md)
