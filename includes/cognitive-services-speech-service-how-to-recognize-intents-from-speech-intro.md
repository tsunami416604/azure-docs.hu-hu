---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a3cbfc3677c5b1a19b83a82da9bcd6bed3108152
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346247"
---
<!-- N.B. no header, language-agnostic -->

A [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) lehetővé teszi a ismeri fel a **leképezések a speech**kapcsolva a beszédfelismerési szolgáltatás, és a [hangfelismerési szolgáltatás (LUIS)](https://luis.ai).

1. Hozzon létre egy beszéd-előállító, a Language Understanding service előfizetési kulcs megadása és [régió](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition). A Language Understanding előfizetési kulcs nevezzük **végponti kulcs** a szolgáltatás dokumentációjára. A kulcs létrehozási hangfelismerési szolgáltatás nem használható. Lásd még a **Megjegyzés** alatt.

1. Egy leképezési felismerő lekérheti a speech gyári. Egy felismerő használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

1. A nyelvi megértése a modell az AppId alapján lekérése, és a szükséges leképezések hozzáadása. 

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredmények (beleértve a leképezések), ha. Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

1. Indítsa el a szándékának felismerése. Használja a egylépéses recognition, felismerés parancsot vagy lekérdezést, például `RecognizeAsync()`, amely visszaadja az első utterance (kifejezés) felismert. Hosszan futó felismerés használatához `StartContinuousRecognitionAsync()` és az események aszinkron felismerési eredményeket lefoglalhatnak.

Tekintse meg az alábbi kódrészleteket szándékfelismerés forgatókönyvek esetén a Speech SDK használatával. Cserélje le a saját Language Understanding előfizetési kulcs (végpont), a [régió az előfizetés](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition), és a minták a megfelelő helyen a szándék modell alkalmazásazonosítója.

> [!NOTE]
> Ellentétben más, a beszéd SDK által támogatott szolgáltatások szándékfelismerés szükséges egy adott előfizetés kulcs (Language Understanding szolgáltatási végpont). [Itt](https://www.luis.ai) talál további információt szeretne a szándékának felismerése. Hogyan lehet beszerezni a **végponti kulcs** leírt [Itt](https://docs.microsoft.com/en-us/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
