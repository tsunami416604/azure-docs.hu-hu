---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122635"
---
<!-- N.B. no header, no intents here, language-agnostic -->

A Cognitive Services [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **Speech to Text** az összes funkciójával az alkalmazásban.

1. Egy beszéd-előállítót hoz létre, és adja meg a Speech service előfizetési kulcs (vagy egy engedélyezési jogkivonatot) és a egy [régió](~/articles/cognitive-services/speech-service/regions.md) paraméterekként. Adjon meg egy nem szabványos végpontot az egyéni végpontok is megadhatja.

1. A beszédfelismerő lekérheti a speech gyári. Beállíthatja, hogy a beviteli nyelv és a kimeneti formátumot. Egy felismerő használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha. Ellenkező esetben az alkalmazás csak a végleges átírást eredményt kap.

1. Indítsa el a felismerése. Egylépéses recognition, felismerés parancsot vagy lekérdezést, például használja a `RecognizeAsync()` metódust. Ez a módszer az első felismert utterance (kifejezés) adja vissza. A hosszú ideig futó felismerés például beszédátírási, használja a `StartContinuousRecognitionAsync()` metódust. Az események aszinkron felismerési eredményeket lefoglalhatnak.

Tekintse meg az alábbi kódrészleteket speech recognition forgatókönyvek, amelyek a beszéd SDK-val.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
