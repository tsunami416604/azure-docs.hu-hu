---
title: Beszéd szolgáltatási régiók
description: A beszédfelismerési szolgáltatás régiók referenciája.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379570"
---
# <a name="regions-of-the-speech-service"></a>A beszédfelismerési szolgáltatás régiók

A beszédfelismerési szolgáltatás különböző régiókban érhető el.
Előfizetés létrehozásakor választhat a rendelkezésre álló régiók, igényeitől függően.

Amikor az előfizetésével számolnia a kiválasztott régióban.

## <a name="rest-api"></a>REST API

A REST API-val, válassza ki a megfelelő régióspecifikus végpontok.
Lásd: [REST API-k](rest-apis.md) részleteiről.

## <a name="speech-sdk"></a>Beszéd SDK

Az a [beszéd SDK](speech-sdk.md), régiók karakterláncként vannak megadva (például, hogy paraméterként [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) beszéd használata a C# SDK-ban).

### <a name="regions-for-speech-recognition-and-translation"></a>A beszédfelismerés és -fordítási régiók

Az alábbi táblázat felsorolja az elérhető régiók **beszédfelismerés** és **fordítási**:

Régió| A beszédfelismerés SDK régió paraméter értéke| Portál
-|-
USA nyugati régiója| `westus`| https://westus.cris.ai
USA 2. nyugati régiója| `westus2`| https://westus2.cris.ai
USA keleti régiója| `eastus`| https://eastus.cris.ai
USA 2. keleti régiója| `eastus2`| https://eastus2.cris.ai
Kelet-Ázsia| `eastasia`| https://eastasia.cris.ai
Délkelet-Ázsia| `southeastasia`| https://southeastasia.cris.ai
Észak-Európa| `northeurope`| https://northeurope.cris.ai
Nyugat-Európa|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Régióban a szándékának felismerése

Az elérhető régiók **szándékának felismerése** a Speech SDK-n keresztül szerepelnek a [Language Understanding régió szolgáltatásoldal](/azure/cognitive-services/luis/luis-reference-regions).
Minden felsorolt közzétételi régió a végpont a tartománynév első része a megfelelő beszédfelismerési SDK régió paraméter határozza meg.
Például `westus` , adja meg az USA nyugati közzétételi régiója.
