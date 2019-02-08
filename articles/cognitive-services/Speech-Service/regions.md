---
title: Régió – beszédszolgáltatások
titlesuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: 1a291a54389b678647f4a08ea03ec8c2f85a5917
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872833"
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

Az elérhető régiók **szándékának felismerése** a Speech SDK-n keresztül tartoznak a következők:

 Globális régió | Régió | Beszéd SDK paraméter
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
 Észak-Amerika | USA nyugati régiója, 2. | `westus2`
 Dél-Amerika | Dél-Brazília | `brazilsouth`

Ez a közzétételi által támogatott régiók részhalmazát a [hangfelismerési szolgáltatás (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás is elérhetővé teszi a hang-szöveg és a szöveg-hang transzformációs kérések REST-végpontokat.

### <a name="speech-to-text"></a>Hang-szöveg

Referencia a hang-szöveg transzformációs dokumentációjáért lásd: [REST API-k](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg-hang transzformációs dokumentációja, lásd: [REST API-k](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
