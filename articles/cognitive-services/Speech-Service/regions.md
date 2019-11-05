---
title: Régiók – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speech Service régióira mutató hivatkozás.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 469dab093ed7a62171d232695af3258cc874b5f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481312"
---
# <a name="speech-service-supported-regions"></a>Beszédfelismerési szolgáltatás által támogatott régiók

A beszédfelismerési szolgáltatás lehetővé teszi, hogy az alkalmazás hangot Konvertáljon szöveggé, beszéd fordítást végezzen, és szövegeket készítsen a beszédhez. A szolgáltatás több régióban is elérhető a Speech SDK és a REST API-k egyedi végpontokkal.

Győződjön meg arról, hogy az előfizetéséhez tartozó régiónak megfelelő végpontot használja.

## <a name="speech-sdk"></a>Speech SDK

A [SPEECH SDK](speech-sdk.md)-ban a régiók karakterláncként vannak megadva (például a Speech SDK-ban `SpeechConfig.FromSubscription` paraméterként C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Beszéd – szöveg, szöveg – beszéd és fordítás

A Speech SDK a következő régiókban érhető el a beszédfelismerés, a **szöveg és a beszéd** **megjelenítéséhez**, valamint a **fordításhoz**:

  Régió | Speech SDK-paraméter | Beszédfelismerési testreszabási portál
 ------|-------|--------
 USA nyugati régiója | `westus` | https://westus.cris.ai
 USA 2. nyugati régiója | `westus2` | https://westus2.cris.ai
 USA keleti régiója | `eastus` | https://eastus.cris.ai
 USA 2. keleti régiója | `eastus2` | https://eastus2.cris.ai
 USA középső régiója | `centralus` | https://centralus.cris.ai
 USA északi középső régiója | `northcentralus` | https://northcentralus.cris.ai
 USA déli középső régiója | `southcentralus` | https://southcentralus.cris.ai
 Közép-India | `centralindia` | https://centralindia.cris.ai
 Kelet-Ázsia | `eastasia` | https://eastasia.cris.ai
 Délkelet-Ázsia | `southeastasia` | https://southeastasia.cris.ai
 Kelet-Japán | `japaneast` | https://japaneast.cris.ai
 Korea középső régiója | `koreacentral` | https://koreacentral.cris.ai
 Kelet-Ausztrália | `australiaeast` | https://australiaeast.cris.ai
 Közép-Kanada | `canadacentral` | https://canadacentral.cris.ai
 Észak-Európa | `northeurope` | https://northeurope.cris.ai
 Nyugat-Európa | `westeurope` | https://westeurope.cris.ai
 Egyesült Királyság déli régiója | `uksouth` | https://uksouth.cris.ai
 Közép-Franciaország | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Szándék-felismerés

Az elérhető régiók a beszédfelismerési SDK-n keresztül történő **szándék-felismeréshez** a következők:

 Globális régió | Régió | Speech SDK-paraméter
 ------|-------|--------
 Ázsia | Kelet-Ázsia | `eastasia`
 Ázsia | Délkelet-Ázsia | `southeastasia`
 Ausztrália | Kelet-Ausztrália | `australiaeast`
 Európa | Észak-Európa | `northeurope`
 Európa | Nyugat-Európa | `westeurope`
 Észak-Amerika | USA keleti régiója | `eastus`
 Észak-Amerika | USA 2. keleti régiója | `eastus2`
 Észak-Amerika | USA déli középső régiója | `southcentralus`
 Észak-Amerika | USA nyugati középső régiója | `westcentralus`
 Észak-Amerika | USA nyugati régiója | `westus`
 Észak-Amerika | USA 2. nyugati régiója | `westus2`
 Dél-Amerika | Dél-Brazília | `brazilsouth`

Ez a [Language Understanding szolgáltatás (Luis)](/azure/cognitive-services/luis/luis-reference-regions)által támogatott közzétételi régiók részhalmaza.

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A [SPEECH SDK](speech-sdk.md) a következő régiókban támogatja a **hangsegéd** képességeit:

Régió | Speech SDK-paraméter
-------|---------------------
USA nyugati régiója | `westus`
USA 2. nyugati régiója | `westus2`
USA keleti régiója | `eastus`
USA 2. keleti régiója | `eastus2`
Nyugat-Európa | `westeurope`
Észak-Európa | `northeurope`
Délkelet-Ázsia | `southeastasia`

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás a REST-végpontokat is elérhetővé teszi a beszéd – szöveg és a szöveg – beszéd kérésekhez.

### <a name="speech-to-text"></a>Diktálás

A beszéd – szöveg hivatkozási dokumentációja a [beszéd – szöveg REST API](rest-speech-to-text.md)című részben olvasható.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg – beszéd hivatkozási dokumentáció: [szöveg – beszéd REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]