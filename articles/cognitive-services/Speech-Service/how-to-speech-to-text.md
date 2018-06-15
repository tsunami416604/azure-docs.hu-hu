---
title: Beszéd szöveg segítségével |} Microsoft Docs
description: A beszédfelismerés szolgáltatásban szöveg beszédfelismerés használata
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 878a31992415b1f8688afcfb186fcd94ce2567b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35349982"
---
# <a name="use-speech-to-text-in-the-speech-service"></a>""Beszéd a szöveg használja a beszédfelismerés szolgáltatásban

Használhat **szöveg beszédfelismerés** két különböző módon az alkalmazásokban.

| Módszer | Leírás |
|-|-|
| [SDK](speech-sdk.md) | C/C++, C# és Java * fejlesztőknek legegyszerűbb módja |
| [REST](rest-apis.md) | Ismeri fel a HTTP POST-kérelmet használatával rövid utterances | 

\* *A Java SDK része a [beszéd eszközök SDK](speech-devices-sdk.md).*

## <a name="using-the-sdk"></a>Az SDK használata

A [beszéd SDK](speech-sdk.md) biztosítja a legegyszerűbb használandó **szöveg beszédfelismerés** az alkalmazás teljes körű működését.

1. Hozzon létre egy beszéd gyári biztosítása a beszédfelismerés szolgáltatás előfizetés kulcs vagy egy engedélyezési jogkivonatot. Is konfigurálhatja, például a felismerési nyelvet vagy egy egyéni végpont saját Beszéd felismerés modellek ezen a ponton.

2. A felismerő a gyári beolvasása sikertelen. Három különböző típusú felismerő érhetők el. A felismerő típusonkénti használhatja az eszköz alapértelmezett mikrofon, hangadatfolyam vagy Hang fájlból.

    A felismerő | Függvény
    -|-
    Beszédfelismerési|Szöveg írjanak elő beszéd biztosít
    Leképezési felismerő|Beszélőfelismerési leképezés keresztül származik [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) felismerés után\*
    Fordítási felismerő|Lefordítja a transcribed szöveg másik nyelvet (lásd: [beszéd fordítási](how-to-translate-speech.md))

    \* *A leképezési felismerés kell használnia egy külön LUIS előfizetés kulcs a leképezési felismerő beszéd gyár létrehozásakor.*
    
4. Nagy terhelést jelent az események aszinkron művelet, ha szükséges. A felismerő majd meghívja a eseménykezelők, ha átmeneti és végleges eredmények. Ellenkező esetben az alkalmazás végső írjanak elő eredményt fog kapni.

5. Indítsa el a felismerési.

### <a name="sdk-samples"></a>SDK-minták

A minták legújabb készletét, tekintse meg a [kognitív szolgáltatások beszéd SDK minta GitHub-tárházban](https://aka.ms/csspeech/samples).

## <a name="using-the-rest-api"></a>A REST API használatával

A REST API is legegyszerűbb módja a beszéd felismerésére, ha nem használ az SDK támogatja. Elvégezte HTTP POST-kérelmet a szolgáltatási végpont a kérelem törzsében teljes utterance átadásakor. A felismert szöveget tartalmazó választ kapnak.

> [!NOTE]
> Utterances a REST API használata esetén legfeljebb 15 másodpercre vagy annál kisebb.


További információk a **szöveg beszédfelismerés** REST-API című [REST API-k](rest-apis.md#speech-to-text). Látja a művelet, le kell töltenie a [REST API minta](https://github.com/Azure-Samples/SpeechToText-REST) a Githubról.

## <a name="next-steps"></a>További lépések

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C# beszéd felismerése](quickstart-csharp-windows.md)
