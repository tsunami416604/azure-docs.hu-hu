---
title: Diktálás használata
description: Diktálás használata a Speech service-ben
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 5e7916660275ab45c4556f1169f3e68fe1d3cb85
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282296"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>"Beszéd szöveggé" használata a Speech service-ben

Használhat **Speech to Text** két különböző módon az alkalmazásokban.

| Módszer | Leírás |
|-|-|
| [SDK](speech-sdk.md) | Legegyszerűbb módszer a C/C++, C# és Java-fejlesztőknek |
| [REST](rest-apis.md) | Ismeri fel a HTTP POST-kérelmet használatával rövid kimondott szöveg | 

## <a name="using-the-sdk"></a>Az SDK használata

A [beszéd SDK](speech-sdk.md) nyújt a használatának legegyszerűbb módja **Speech to Text** az összes funkciójával az alkalmazásban.

1. Biztosít egy beszéd service előfizetési kulcsot speech-előállító létrehozásához és [régió](regions.md) vagy egy engedélyezési jogkivonatot. Beállítások, például a felismerés nyelvet vagy egy egyéni végpontot a saját beszédfelismerési modelleket, ezen a ponton is konfigurálhatja.

2. Egy felismerő lekérheti az előállító. Három különböző típusú felismerő érhetők el. Minden egyes felismerő típus használhatja az eszköz alapértelmezett mikrofon, hang stream vagy Hang-fájlból.

    Felismerő | Függvény
    -|-
    Beszédfelismerő|Beszéd szöveges beszédátírási biztosít
    Leképezési felismerő|Beszélő szándékát keresztül használatából [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) után felismerése\*
    Fordítási felismerő|Az átírt szöveg lefordítása a egy másik nyelvre (lásd: [beszédalapú fordítási](how-to-translate-speech.md))

    \* *Szándékfelismerés LUIS külön előfizetési kulcs használatára, amikor egy beszéd-előállítót hoz létre a szándék felismerő szüksége.*
    
4. Az események aszinkron művelethez lefoglalhatnak, ha szükséges. A felismerő ekkor meghívja az eseménykezelőket, átmeneti és végső eredményt, ha. Ellenkező esetben az alkalmazás végleges átírást eredményt fog kapni.

5. Indítsa el a felismerése.
   Használja a egylépéses recognition, felismerés parancsot vagy lekérdezést, például `RecognizeAsync()`, amely visszaadja az első utterance (kifejezés) felismert.
   A hosszú ideig futó felismerés beszédátírási, például használja `StartContinuousRecognitionAsync()` és az események aszinkron felismerési eredményeket lefoglalhatnak.

### <a name="sdk-samples"></a>SDK-minták

A legújabb minták, lásd: a [Cognitive Services beszédfelismerő SDK minta GitHub-adattár](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>A REST API használatával

A REST API egy a legegyszerűbb módszer beszédfelismerő ismeri fel, ha nem használja az SDK támogatja. Választja ki egy HTTP POST-kérelmet a szolgáltatásvégpont átadja a kérelem törzsében lévő teljes utterance (kifejezés). A felismert szöveget tartalmazó választ kap.

> [!NOTE]
> Kimondott szöveg, legfeljebb 15 másodperc vagy annál kisebb, a REST API használatakor.

További információ a **Speech to Text** – REST API-t, lásd: [REST API-k](rest-apis.md#speech-to-text). Megtekintés működés közben, töltse le a [REST API minta](https://github.com/Azure-Samples/SpeechToText-REST) a Githubról.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Hogyan ismerhetik fel a beszéd, a C++](quickstart-cpp-windows.md)
- [Beszéd felismerése C# nyelven](quickstart-csharp-dotnet-windows.md)
- [Hogyan beszédfelismerést Java nyelven](quickstart-java-android.md)
