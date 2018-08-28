---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: b46eba502462e0cdbcb222082c5c885f84a8d1e5
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090679"
---
<!-- N.B. no header, no intents here, language-agnostic -->

A Cognitive Services [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **Speech to Text** az összes funkciójával az alkalmazásban.

1. Egy beszéd-előállítót hoz létre, és adja meg a Speech service előfizetési kulcs (vagy egy engedélyezési jogkivonatot) és a egy [régió](~/articles/cognitive-services/speech-service/regions.md) paraméterekként. Adjon meg egy nem szabványos végpontot az egyéni végpontok is megadhatja.

1. A beszédfelismerő lekérheti a speech gyári. Beállíthatja, hogy a beviteli nyelv és a kimeneti formátumot. Egy felismerő használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha. Ellenkező esetben az alkalmazás csak a végleges átírást eredményt kap.

1. Indítsa el a felismerése. Egylépéses recognition, felismerés parancsot vagy lekérdezést, például használja a `RecognizeAsync()` metódust. Ez a módszer az első felismert utterance (kifejezés) adja vissza. A hosszú ideig futó felismerés például beszédátírási, használja a `StartContinuousRecognitionAsync()` metódust. Az események aszinkron felismerési eredményeket lefoglalhatnak.

Tekintse meg az alábbi kódrészleteket speech recognition forgatókönyvek, amelyek a beszéd SDK-val.

[!include[Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
