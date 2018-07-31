---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 74b9da1b3b81f9f35f231ef5caef8eafedc9aefc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343037"
---
<!-- N.B. no header, language-agnostic -->

A [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **beszédalapú fordítási** az alkalmazásban.
Az SDK szolgáltatás összes funkcióját biztosítja. Az alapvető folyamat beszédalapú fordítási végrehajtásához a következő lépésekből áll:

1. A beszédfelismerési szolgáltatás előfizetési kulcs vagy egy engedélyezési jogkivonatot, beszéd-előállító létrehozásához és a egy [régió](~/articles/cognitive-services/speech-service/regions.md).
   
1. Hozzon létre egy fordítási felismerő speech gyári. A forrás és cél szövegfordítási nyelv, valamint adja meg, hogy kívánja-e a szöveg és beszéd kimeneti konfigurálhatja. Nincsenek fordítási felismerő használ hangforrásról alapján különböző változataira jellemző.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és a végső eredmények, valamint a nem kötelező hangkimeneti egy összefoglaló esemény, ha. Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

1. Indítsa el a felismerése. Egylépéses fordítási használatra RecognizeAsync(), amely visszaadja az első utterance (kifejezés) felismert. Hosszan futó fordításokat StartContinuousRecognitionAsync() használjon, és az események aszinkron felismerési eredményeket lefoglalhatnak.

Tekintse meg az alábbi kódrészleteket a speech fordítási forgatókönyvet, a beszéd SDK használatával.

[!include[Get a Subscription Key](cognitive-services-speech-service-get-subscription-key.md)]
