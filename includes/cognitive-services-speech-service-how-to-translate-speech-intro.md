---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2019
ms.locfileid: "57850233"
---
<!-- N.B. no header, language-agnostic -->

A Microsoft Cognitive Services [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) nyújt a használatának legegyszerűbb módja **beszédalapú fordítási** az alkalmazásban.
Az SDK szolgáltatás összes funkcióját biztosítja. Az alapvető folyamat beszédalapú fordítási végrehajtásához a következő lépésekből áll:

1. Hozzon létre egy beszéd fordítási konfigurációt, és adja meg a Speech service előfizetési kulcs (vagy egy engedélyezési jogkivonatot) és a egy [régió](~/articles/cognitive-services/speech-service/regions.md) paraméterekként. Szükség szerint változtassa meg a konfigurációt. Például akkor is konfigurálja a forrás és cél szövegfordítási nyelv, valamint adja meg, hogy szöveg és beszéd kimeneti.

1. Hozzon létre egy fordítási felismerő a speech fordítási konfigurációjából. Adjon meg egy hang-konfigurációt, ha azt szeretné, hogy ismeri fel az alapértelmezett mikrofon (például hang stream vagy hangfájl) eltérő forrásból.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és a végső eredmények, valamint a nem kötelező hangkimeneti egy összefoglaló esemény, ha. Ellenkező esetben az alkalmazás csak a végleges átírást eredményt kap.

1. Indítsa el a felismerése. Egylépéses fordítási használja a `RecognizeOnceAsync()` metódussal, amely az első felismert utterance (kifejezés) adja vissza. A hosszú ideig futó fordításokat, használja a `StartContinuousRecognitionAsync()` metódust, és tie fel az eseményeket az aszinkron felismerési eredményeket.

Tekintse meg az alábbi kódrészleteket speech fordítási forgatókönyvet, amely a Speech SDK-val.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
