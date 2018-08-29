---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144568"
---
<!-- N.B. no header, language-agnostic -->

A Microsoft Cognitive Services [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **beszédalapú fordítási** az alkalmazásban.
Az SDK szolgáltatás összes funkcióját biztosítja. Az alapvető folyamat beszédalapú fordítási végrehajtásához a következő lépésekből áll:

1. Egy beszéd-előállítót hoz létre, és adja meg a Speech service előfizetési kulcs és a egy engedélyezési jogkivonatot és a egy [régió](~/articles/cognitive-services/speech-service/regions.md) paraméterekként.
   
1. Hozzon létre egy fordítási felismerő speech gyári. Akkor is konfigurálja a forrás és cél szövegfordítási nyelv, valamint adja meg, hogy szöveg és beszéd kimeneti. Nincsenek fordítási felismerő a hangbemeneti forrás, amelyet használhat alapján különböző változataira jellemző.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és a végső eredmények, valamint a nem kötelező hangkimeneti egy összefoglaló esemény, ha. Ellenkező esetben az alkalmazás csak a végleges átírást eredményt kap.

1. Indítsa el a felismerése. Egylépéses fordítási használja a `RecognizeAsync()` metódussal, amely az első felismert utterance (kifejezés) adja vissza. A hosszú ideig futó fordításokat, használja a `StartContinuousRecognitionAsync()` metódust, és tie fel az eseményeket az aszinkron felismerési eredményeket.

Tekintse meg az alábbi kódrészleteket speech fordítási forgatókönyvet, amely a Speech SDK-val.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
