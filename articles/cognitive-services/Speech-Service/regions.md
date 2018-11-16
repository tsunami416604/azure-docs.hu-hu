---
title: Beszéd szolgáltatási régiók
titlesuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: 8d36036332e939075ffac8763bec9c23d8e4a3f7
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712891"
---
# <a name="speech-service-supported-regions"></a>Beszédszolgáltatás támogatott régiók

A Speech service lehetővé teszi, hogy az alkalmazás hanganyagot alakíthat át szöveggé, beszédalapú fordítási és fedett szöveg-beszéd átalakítás elvégzéséhez. A szolgáltatás az egyedi végpontok a Speech SDK és a REST API-k több régióban érhető el.

Győződjön meg arról, hogy használja-e a végpontot, amely megfelel annak a régiónak az előfizetéshez.

## <a name="speech-sdk"></a>Beszéd SDK

Az a [Speech Service SDK](speech-sdk.md), régiók karakterláncként vannak megadva (például, hogy paraméterként `SpeechConfig.FromSubscription` beszéd használata a C# SDK-ban).

### <a name="speech-recognition-and-translation"></a>A beszédfelismerés és fordítás

A beszédfelismerés SDK érhető el ezekben a régiókban a **beszédfelismerés** és **fordítási**:

  Régió | Beszéd SDK paraméter | Speech-portál testreszabása
 ------|-------|--------
 USA nyugati régiója | `westus` | https://westus.cris.ai
 USA 2. nyugati régiója | `westus2` | https://westus2.cris.ai
 USA keleti régiója | `eastus` | https://eastus.cris.ai
 USA 2. keleti régiója | `eastus2` | https://eastus2.cris.ai
 Kelet-Ázsia | `eastasia` | https://eastasia.cris.ai
 Délkelet-Ázsia | `southeastasia` | https://southeastasia.cris.ai
 Észak-Európa | `northeurope` | https://northeurope.cris.ai
 Nyugat-Európa | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>Szándékfelismerés

**Szándékfelismerés** a Speech SDK megosztások régió támogatja az intelligens hangfelismerési szolgáltatással. Az elérhető régiók teljes listáját lásd: [régiók és a végpontok – intelligens HANGFELISMERÉSI közzététele](https://docs.microsoft.comazure/cognitive-services/luis/luis-reference-regions)

Választható régiók **szándékának felismerése** a Speech SDK-n keresztül jelennek meg a [Language Understanding szolgáltatásoldal régió](/azure/cognitive-services/luis/luis-reference-regions).

Minden felsorolt közzétételi régió, használja a megadott **API régió neve**. Például `westus` USA nyugati RÉGIÓJA számára.

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás is elérhetővé teszi a hang-szöveg és a szöveg-hang transzformációs kérések REST-végpontokat.

### <a name="speech-to-text"></a>Speech-to-text

Referencia a hang-szöveg transzformációs dokumentációjáért lásd: [REST API-k](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Text-to-speech

Szöveg-hang transzformációs dokumentációja, lásd: [REST API-k](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
