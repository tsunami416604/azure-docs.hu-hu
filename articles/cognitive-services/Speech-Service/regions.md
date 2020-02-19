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
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431162"
---
# <a name="speech-service-supported-regions"></a>Beszédfelismerési szolgáltatás által támogatott régiók

A Speech service lehetővé teszi, hogy az alkalmazás hanganyagot alakíthat át szöveggé, beszédalapú fordítási és fedett szöveg-beszéd átalakítás elvégzéséhez. A szolgáltatás az egyedi végpontok a Speech SDK és a REST API-k több régióban érhető el.

A beszédfelismerési portál egyéni konfigurációkat hajt végre a beszédfelismerési élményben az összes régióban: https://speech.microsoft.com

A beszédfelismerési szolgáltatás hívása esetén győződjön meg arról, hogy a hívás megfelel az előfizetés régiójának.

## <a name="speech-sdk"></a>Beszéd SDK

A [SPEECH SDK](speech-sdk.md)-ban a régiók karakterláncként vannak megadva (például a Speech SDK-ban `SpeechConfig.FromSubscription` paraméterként C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Beszéd – szöveg, szöveg – beszéd és fordítás

A beszédfelismerési testreszabási portál itt érhető el: https://speech.microsoft.com

A beszédfelismerési szolgáltatás a következő régiókban érhető el a beszédfelismerés, a **szöveg és a beszéd** **megjelenítéséhez**, valamint a **fordításhoz**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Ha a [SPEECH SDK](speech-sdk.md)-t használja, a régiókat a **régió azonosítója** adja meg (például paraméterként `SpeechConfig.FromSubscription`). Győződjön meg arról, hogy a régió megfelel az előfizetés régiójának.

### <a name="intent-recognition"></a>Szándékfelismerés

Az elérhető régiók a beszédfelismerési SDK-n keresztül történő **szándék-felismeréshez** a következők:

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

Ez a [Language Understanding szolgáltatás (Luis)](/azure/cognitive-services/luis/luis-reference-regions)által támogatott közzétételi régiók részhalmaza.

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A [SPEECH SDK](speech-sdk.md) a következő régiókban támogatja a **hangsegéd** képességeit:

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

A beszédfelismerési szolgáltatás is elérhetővé teszi a hang-szöveg és a szöveg-hang transzformációs kérések REST-végpontokat.

### <a name="speech-to-text"></a>Diktálás

A beszéd – szöveg hivatkozási dokumentációja a [beszéd – szöveg REST API](rest-speech-to-text.md)című részben olvasható.

A REST API végpontjának formátuma a következő:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje le a `<REGION_IDENTIFIER>` az előfizetés régiójának megfelelő azonosítóra a következő táblázatból:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert az URL-címhez kell hozzáfűzni, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó angol nyelv például a következő: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg – beszéd hivatkozási dokumentáció: [szöveg – beszéd REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
