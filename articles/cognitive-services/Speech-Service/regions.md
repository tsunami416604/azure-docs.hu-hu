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
ms.openlocfilehash: c026c636030b397da612b3c1409b80f0ff970f57
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284744"
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

Az alábbi táblázat felsorolja az elérhető régiók **beszédfelismerés** és **fordítási**:

Régió| A beszédfelismerés SDK régió paraméter értéke
-|-
USA nyugati régiója| `westus`
Kelet-Ázsia| `eastasia`
Észak-Európa| `northeurope`

Az elérhető régiók **szándékának felismerése** a Speech SDK-n keresztül szerepelnek a [Language Understanding régió szolgáltatásoldal](/azure/cognitive-services/luis/luis-reference-regions).
Minden felsorolt közzétételi régió a végpont a tartománynév első része a megfelelő beszédfelismerési SDK régió paraméter határozza meg.
Például `westus` , adja meg az USA nyugati közzétételi régiója.
