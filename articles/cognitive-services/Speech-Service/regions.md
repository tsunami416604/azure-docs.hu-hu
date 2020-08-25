---
title: Régiók – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatáshoz elérhető régiók és végpontok listája, beleértve a beszéd szövegét, a szöveg és a beszéd fordítását, valamint a beszédfelismerést.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b0574c41042e172af78365bb273c81729ce204ab
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749310"
---
# <a name="speech-service-supported-regions"></a>Beszédfelismerési szolgáltatás által támogatott régiók

A beszédfelismerési szolgáltatás lehetővé teszi, hogy az alkalmazás hangot Konvertáljon szöveggé, beszéd fordítást végezzen, és szövegeket készítsen a beszédhez. A szolgáltatás több régióban is elérhető a Speech SDK és a REST API-k egyedi végpontokkal.

A beszédfelismerési portál egyéni konfigurációkat hajt végre a beszédfelismerési élményben az összes régióban: https://speech.microsoft.com

Vegye figyelembe a következő szempontokat, amikor a régiókat fontolgatja:

* Ha az alkalmazás egy [SPEECH SDK](speech-sdk.md)-t használ, akkor adja meg a régió azonosítóját, például a `westus` beszédfelismerési konfiguráció létrehozásakor.
* Ha az alkalmazás a beszédfelismerési szolgáltatás [REST API](rest-apis.md)-jának egyikét használja, akkor a régió a kérések végrehajtásakor használt végponti URI része.
* A régióhoz létrehozott kulcsok csak az adott régióban érvényesek. A más régiókkal való használatának megkísérlése hitelesítési hibákat eredményez.

## <a name="speech-sdk"></a>Beszéd SDK

A [SPEECH SDK](speech-sdk.md)-ban a régiók karakterláncként vannak megadva (például paraméterként `SpeechConfig.FromSubscription` a C# Speech SDK-ban).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Beszéd – szöveg, szöveg – beszéd és fordítás

A beszédfelismerési testreszabási portál itt érhető el: https://speech.microsoft.com

A beszédfelismerési szolgáltatás a következő régiókban érhető el a beszédfelismerés, a **szöveg és a beszéd** **megjelenítéséhez**, valamint a **fordításhoz**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Ha a [SPEECH SDK](speech-sdk.md)-t használja, a régiókat a **régió azonosítója** adja meg (például paraméterként `SpeechConfig.FromSubscription` ). Győződjön meg arról, hogy a régió megfelel az előfizetés régiójának.

### <a name="intent-recognition"></a>Szándékfelismerés

Az elérhető régiók a beszédfelismerési SDK-n keresztül történő **szándék-felismeréshez** a következők:

| Globális régió | Region           | Régió azonosítója |
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
| Észak-Amerika | USA 2. nyugati régiója        | `westus2`            |
| Dél-Amerika | Dél-Brazília     | `brazilsouth`        |

Ez a [Language Understanding szolgáltatás (Luis)](/azure/cognitive-services/luis/luis-reference-regions)által támogatott közzétételi régiók részhalmaza.

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A [SPEECH SDK](speech-sdk.md) a következő régiókban támogatja a **hangsegéd** képességeit:

| Region         | Régió azonosítója |
| -------------- | -------------------- |
| USA nyugati régiója        | `westus`             |
| USA 2. nyugati régiója      | `westus2`            |
| USA keleti régiója        | `eastus`             |
| USA 2. keleti régiója      | `eastus2`            |
| Nyugat-Európa    | `westeurope`         |
| Észak-Európa   | `northeurope`        |
| Délkelet-Ázsia | `southeastasia`      |

### <a name="speaker-recognition"></a>Beszélőfelismerés

Speaker Recognition jelenleg csak a régióban érhető el `westus` .

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás a REST-végpontokat is elérhetővé teszi a beszéd – szöveg és a szöveg – beszéd kérésekhez.

### <a name="speech-to-text"></a>Diktálás

A beszéd – szöveg hivatkozási dokumentációja a [beszéd – szöveg REST API](rest-speech-to-text.md)című részben olvasható.

A REST API végpontjának formátuma a következő:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje le az `<REGION_IDENTIFIER>` elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert az URL-címhez kell hozzáfűzni, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó angol nyelv például a következő: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg – beszéd hivatkozási dokumentáció: [szöveg – beszéd REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
