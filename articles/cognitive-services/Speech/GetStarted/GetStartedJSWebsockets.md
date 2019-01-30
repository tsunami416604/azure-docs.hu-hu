---
title: A Bing Speech Recognition API a JavaScript használatának első lépései |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Cognitive Services a Bing Speech Recognition API használatával hozhat létre alkalmazásokat, amelyek folyamatosan beszélt hangot képes szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: c5869133d94f73e1d463a176614617df2587bdfb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226105"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>A Speech Recognition API a JavaScript használatának első lépései

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Alkalmazások, amelyek a beszélt hangot képes szöveggé a Speech Recognition API használatával is fejleszthet. A JavaScript-klienskódtár használja a [Speech Service WebSocket protokoll](../API-Reference-REST/websocketprotocol.md), egyidejűleg így a kommunikációhoz, és fogadjon megjelenített érzéseket szöveget. Ez a cikk segít a Speech Recognition API a JavaScript használatának első lépései.

## <a name="prerequisites"></a>Előfeltételek

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a Speech Recognition API és a egy ingyenes próba-előfizetését kulcs lekérése

A beszédfelismerő API a Cognitive Services részét képezi. Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

> [!IMPORTANT]
> Előfizetési kulcs lekérése. Ügyfélkódtárak beszéd használata előtt rendelkeznie kell egy [előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Bevezetés

Ebben a szakaszban a Microsoft végigvezeti egy minta HTML-oldalt betöltéséhez szükséges lépéseket. A mintában található a [GitHub-adattár](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Is **nyissa meg a minta közvetlenül** a tárházból, vagy **nyissa meg a mintát a helyi másolat** a tárház.

> [!NOTE]
> Egyes böngészők blokkolják a nem biztonságos eredet mikrofon-hozzáférés. Ezért javasoljuk a minta futtatásához / "alkalmazását" az összes támogatott böngésző működéséhez https.

### <a name="open-the-sample-directly"></a>Nyissa meg közvetlenül a minta

Egy előfizetési kulcsot beszerezni a fent leírtak szerint. Nyissa meg a [a minta mutató hivatkozás](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Ez az oldal betölti az alapértelmezett böngésző be (használatával [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Nyissa meg a mintát a helyi másolat

Próbálja ki a minta helyileg, klónozza a tárházat:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Fordítsa le a TypeScript-adatforrások és kötegeli őket egy egységes JavaScript-fájlt ([npm](https://www.npmjs.com/) telepítve kell lennie a gépen). Módosítsa a klónozott adattár gyökérkönyvtárában, és futtassa a parancsokat:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Nyissa meg `samples\browser\Sample.html` kedvenc böngészőjében.

## <a name="next-steps"></a>További lépések

Bővebben az SDK venni az saját weblapon érhető el [Itt](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Megjegyzések

- A Speech Recognition API támogatja a három [felismerés módok](../concepts.md#recognition-modes). A mód módosításával válthat a **Setup()** függvény a Sample.html fájlban található. A minta a mód beállítása `Interactive` alapértelmezés szerint. A mód módosításához frissítse a paraméter `SR.RecognitionMode.Interactive` egy másik módra. Módosítsa például a paraméter `SR.RecognitionMode.Conversation`.
- Támogatott nyelvek teljes listáját lásd: [támogatott nyelvek](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Kapcsolódó témakörök

- [A JavaScript Speech Recognition API-mintaadattár](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [A REST API használatának első lépései](GetStartedREST.md)
