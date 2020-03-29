---
title: Régiók - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás elérhető régióinak és végpontjainak listája, beleértve a szövegfelolvasást, a szövegfelolvasást és a beszédfordítást.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220467"
---
# <a name="speech-service-supported-regions"></a>Beszédszolgáltatás által támogatott régiók

A Beszédszolgáltatás lehetővé teszi, hogy az alkalmazás a hangot szöveggé alakítsa, beszédfordítást és rejtett szöveget váltson beszédbeszédre. A szolgáltatás több régióban érhető el, a beszédsdk és a REST API-k egyedi végpontjaival.

A beszédfelismerési portál, amely az összes régió beszédélményének egyéni konfigurációit hajtja végre, itt érhető el:https://speech.microsoft.com

A beszédfelismerési szolgáltatás meghívásához győződjön meg arról, hogy a hívás megegyezik az előfizetés régiójával.

## <a name="speech-sdk"></a>Beszéd SDK

A [beszédsdkában](speech-sdk.md)a régiók karakterláncként vannak megadva (például a C#-hoz tartozó beszédsdk `SpeechConfig.FromSubscription` paramétereként).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Beszéd-szöveg, szövegfelolvasás és fordítás

A beszédtestreszabási portál itt érhető el:https://speech.microsoft.com

A beszédfelismerési szolgáltatás ezekben a régiókban áll rendelkezésre **beszédfelismerésre,** **szövegfelolvasásra**és **fordításra:**

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

A [beszédbeszéd SDK](speech-sdk.md)használata esetén a régiókat a **Régió azonosító** határozza `SpeechConfig.FromSubscription`meg (például paraméterként). Győződjön meg arról, hogy a régió megfelel az előfizetés régiójának.

### <a name="intent-recognition"></a>Szándékfelismerés

A beszédfelismerési SDK-n keresztül a **leképezésfelismeréshez** rendelkezésre álló régiók a következők:

| Globális régió | Régió           | Régió azonosítója |
| ------------- | ---------------- | -------------------- |
| Ázsia          | Kelet-Ázsia        | `eastasia`           |
| Ázsia          | Délkelet-Ázsia   | `southeastasia`      |
| Ausztrália     | Kelet-Ausztrália   | `australiaeast`      |
| Európa        | Észak-Európa     | `northeurope`        |
| Európa        | Nyugat-Európa      | `westeurope`         |
| Észak-Amerika | USA keleti régiója          | `eastus`             |
| Észak-Amerika | USA 2. keleti régiója        | `eastus2`            |
| Észak-Amerika | USA déli középső régiója | `southcentralus`     |
| Észak-Amerika | USA nyugati középső régiója  | `westcentralus`      |
| Észak-Amerika | USA nyugati régiója          | `westus`             |
| Észak-Amerika | USA nyugati régiója, 2.        | `westus2`            |
| Dél-Amerika | Dél-Brazília     | `brazilsouth`        |

Ez a [Language Understanding szolgáltatás (LUIS)](/azure/cognitive-services/luis/luis-reference-regions)által támogatott közzétételi régiók egy részhalmaza.

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A [beszédfelismerési SDK](speech-sdk.md) támogatja a **hangasszisztensi** képességeket ezekben a régiókban:

| Régió         | Régió azonosítója |
| -------------- | -------------------- |
| USA nyugati régiója        | `westus`             |
| USA nyugati régiója, 2.      | `westus2`            |
| USA keleti régiója        | `eastus`             |
| USA 2. keleti régiója      | `eastus2`            |
| Nyugat-Európa    | `westeurope`         |
| Észak-Európa   | `northeurope`        |
| Délkelet-Ázsia | `southeastasia`      |

## <a name="rest-apis"></a>REST API-k

A beszédszolgáltatás rest-végpontokat is elérhetővé tesz a beszéd-szöveg és a szöveg-beszéd kérések hez.

### <a name="speech-to-text"></a>Diktálás

A beszéd-szöveg hivatkozási dokumentációt a [Beszéd-szöveg REST API-ban](rest-speech-to-text.md)találja.

A REST API végpontja a következő formátummal rendelkezik:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje `<REGION_IDENTIFIER>` le az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert hozzá kell fűzni az URL-címhez, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó amerikai angol nyelv `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`például a következő: .

### <a name="text-to-speech"></a>Szövegfelolvasás

A szövegfelolvasó referenciadokumentációt a [Text-to-speech REST API (Szövegfelolvasó REST API) (Szövegfelolvasó REST API) (Szövegfelolvasó REST API) (Szövegfelolvasó REST API) (Szövegfelolvasó REST API) (Text-to-speech](rest-text-to-speech.md)

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
