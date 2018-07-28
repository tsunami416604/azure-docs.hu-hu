---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1b55576581ebddc90c0af6b99a04dbe66d2e3b87
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330845"
---
<!-- N.B. no header, language-agnostic -->

A [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) lehetővé teszi a beszéd, a beszédfelismerési szolgáltatás működteti, a leképezések ismeri fel, és a [hangfelismerési szolgáltatás (LUIS)](https://luis.ai).

1. Hozzon létre egy beszéd-előállító, a Language Understanding service előfizetési kulcs megadása és [régió](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition).


1. Egy leképezési felismerő lekérheti a speech gyári.
   Egy felismerő használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges.
   A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha.
   Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

1. Indítsa el a felismerése.
   Használja a egylépéses recognition, felismerés parancsot vagy lekérdezést, például `RecognizeAsync()`, amely visszaadja az első utterance (kifejezés) felismert.
   A hosszú ideig futó felismerés beszédátírási, például használja `StartContinuousRecognitionAsync()` és az események aszinkron felismerési eredményeket lefoglalhatnak.

Az alábbiakban bemutatjuk, beszéd SDK-val szándékfelismerés forgatókönyvekhez számos kódrészleteket.

> [!NOTE]
> Először szerezze be egy előfizetési kulcsot.
> Szakembereket más, a beszéd SDK által támogatott szolgáltatások szándékfelismerés adott előfizetési kulcs szükséges.
> [Itt](https://www.luis.ai) talál további információt szeretne a szándékának felismerése, valamint egy előfizetési kulcsot beszerezni információ.
> Cserélje le a saját Language Understanding előfizetési kulcs, a [régió az előfizetés](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition), és a minták a megfelelő helyen a szándék modell alkalmazásazonosítója.
