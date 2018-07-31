---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: d7d73d9c5e85ac550b24c73797d536dc4d0fc6ed
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346271"
---
<!-- N.B. no header, no intents here, language-agnostic -->

A [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **Speech to Text** az összes funkciójával az alkalmazásban.

1. A beszédfelismerési szolgáltatás előfizetési kulcs vagy egy engedélyezési jogkivonatot, beszéd-előállító létrehozásához és a egy [régió](~/articles/cognitive-services/speech-service/regions.md). Megadhat egy egyéni végpontot, adjon meg egy nem szabványos végpontot is.

1. A beszédfelismerő lekérheti a speech gyári. Beállíthatja, hogy a beviteli nyelv, valamint a kimeneti formátumot.  Egy felismerő használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha.  Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

1. Indítsa el a felismerése. Használja a egylépéses recognition, felismerés parancsot vagy lekérdezést, például `RecognizeAsync()`, amely visszaadja az első utterance (kifejezés) felismert. A hosszú ideig futó felismerés beszédátírási, például használja `StartContinuousRecognitionAsync()` és az események aszinkron felismerési eredményeket lefoglalhatnak.

Tekintse meg az alábbi kódrészleteket speech recognition forgatókönyvek esetén a Speech SDK-val.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
