---
title: Beszéd szolgáltatás régiók |} Microsoft Docs
description: A beszédfelismerés szolgáltatás régiókhoz hivatkozás.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1eb3768f5a5c5a27a45dde3f62f862f36fa3e8ac
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098498"
---
# <a name="regions-of-the-speech-service"></a>A beszédfelismerés szolgáltatás régiók

A beszédfelismerés szolgáltatás különböző régiókban érhető el.
Ha olyan előfizetést hoz létre egy rendelkezésre álló terület igényeitől függően választhatja ki.

Ha az előfizetés használatára akkor fiókot használja a régiót, amelyet kivételezett.

## <a name="rest-api"></a>REST API

A REST API használatával, válassza ki a jobb oldali régióspecifikus végpontok.
Lásd: [REST API-k](rest-apis.md) részleteiről.



## <a name="speech-sdk"></a>Beszéd SDK

Az a [beszéd SDK](speech-sdk.md), régiók megadott karakterlánc (például paramétereként [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) a beszédfelismerés SDK a C#).

Az alábbi táblázat felsorolja az elérhető régiók beszédfelismerés és:

Régió| A beszédfelismerés SDK régió paraméter értéke
-|-
USA nyugati régiója| `westus`
Kelet-Ázsia| `eastasia`
Észak-Európa| `northeurope`

A leképezési nevéből felismert tartománynév használatával a beszédfelismerés SDK elérhető régiók jelennek meg a [nyelvi ismertetése régió oldalát](/azure/cognitive-services/luis/luis-reference-regions).
Minden felsorolt közzétételi régió a megfelelő beszéd SDK régió paraméter határozza meg, a tartománynév, a végpont első részeként.
Tegyük fel például, `westus` az USA nyugati régiója közzétételi régió megadásához.
