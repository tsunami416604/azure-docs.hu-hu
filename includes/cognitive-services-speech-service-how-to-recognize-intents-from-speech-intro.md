---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 80bf9247bbb07fa61b7153e321b1991b82a9d616
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2019
ms.locfileid: "57902103"
---
<!-- N.B. no header, language-agnostic -->

A Microsoft Cognitive Services [beszéd SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) lehetővé teszi a felismerni **leképezések a speech** és a Cognitive Services által támogatott [hangfelismerési szolgáltatás (LUIS)](https://www.luis.ai/home).

1. Hozzon létre egy beszéd konfigurációt LUIS előfizetési kulccsal és [régió](~/articles/cognitive-services/speech-service/regions.md#intent-recognition) paraméterekként. A LUIS előfizetési kulcs nevezzük **végponti kulcs** a szolgáltatás dokumentációjára. A LUIS szerzői kulcs nem használható. (Lásd a megjegyzést a szakasz későbbi részében).

1. Hozzon létre egy leképezési felismerő a speech-konfigurációból. Adjon meg egy hang-konfigurációt, ha azt szeretné, hogy ismeri fel az alapértelmezett mikrofon (például hang stream vagy hangfájl) eltérő forrásból.

1. A nyelvi ismertetése modell alapján lekérése a **AppId**. A szükséges leképezések hozzáadása.

1. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha (szándékok tartalmazza). Nem, az események lefoglalhatnak, az alkalmazás csak a végleges átírást eredményt kap.

1. Indítsa el a szándékának felismerése. Egylépéses recognition, felismerés parancsot vagy lekérdezést, például használja a `RecognizeOnceAsync()` metódust. Ez a módszer az első felismert utterance (kifejezés) adja vissza. A hosszú ideig futó recognition, használja a `StartContinuousRecognitionAsync()` metódust. Az események aszinkron felismerési eredményeket lefoglalhatnak.

Tekintse meg az alábbi kódrészleteket szándékfelismerés forgatókönyvek, amelyek a beszéd SDK-val. A mintában szereplő értékeket cserélje le a saját LUIS előfizetési kulcs (végpont), a [régió az előfizetés](~/articles/cognitive-services/speech-service/regions.md#intent-recognition), és a **AppId** a szándék modell.

> [!NOTE]
> Más, a beszéd SDK által támogatott szolgáltatások, szakembereket szándékfelismerés szükséges egy adott előfizetési kulcsot (LUIS végponti kulcs). A szándékának felismerése technológiával kapcsolatos információkért lásd: a [LUIS webhely](https://www.luis.ai). Információt beszerezni a **végponti kulcs**, lásd: [LUIS végponti kulcs létrehozása](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription).
