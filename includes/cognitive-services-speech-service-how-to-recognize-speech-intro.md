---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ca342a812f6a8de19c732b5df4fab14a825f6c51
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330851"
---
<!-- N.B. no header, no intents here, language-agnostic -->

A [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **Speech to Text** az összes funkciójával az alkalmazásban.

1. A beszédfelismerési szolgáltatás előfizetési kulcs vagy egy engedélyezési jogkivonatot, beszéd-előállító létrehozásához és a egy [régió](~/articles/cognitive-services/speech-service/regions.md).
   Is konfigurálhatja a beállításokat, például a beszédfelismerési nyelv vagy a saját beszédfelismerési modelleket egyéni végponthoz.

1. A beszédfelismerő lekérheti a speech gyári.
   Egy felismerő használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges.
   A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha.
   Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

1. Indítsa el a felismerése.
   Használja a egylépéses recognition, felismerés parancsot vagy lekérdezést, például `RecognizeAsync()`, amely visszaadja az első utterance (kifejezés) felismert.
   A hosszú ideig futó felismerés beszédátírási, például használja `StartContinuousRecognitionAsync()` és az események aszinkron felismerési eredményeket lefoglalhatnak.

Az alábbiakban bemutatjuk a speech recognition forgatókönyvek a Speech SDK-val számos kódrészleteket.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
