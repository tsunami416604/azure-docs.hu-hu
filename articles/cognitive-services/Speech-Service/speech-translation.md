---
title: Beszédfelismerési szolgáltatás fordítása
titleSuffix: Azure Cognitive Services
description: A Speech Service lehetővé teszi, hogy teljes körű, valós idejű, többnyelvű fordítást adjon az alkalmazásaihoz, eszközeihez és eszközeihez. Ugyanazt az API-t használhatja beszéd – beszéd és beszéd – szöveg fordításhoz is.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 17ad32cba18915bf7f83163cd876686d42323750
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468755"
---
# <a name="what-is-speech-translation"></a>Mi a beszéd fordítása?

Az Azure Speech Servicesből való beszéd-fordítás lehetővé teszi a hangadatfolyamok valós idejű, több nyelvű beszédfelismerési és beszéd-szöveges fordítását. A Speech SDK-val az alkalmazások, eszközök és eszközök hozzáférhetnek a forrás-átírásokhoz és a fordítási kimenetekhez a megadott hanghoz. Az ideiglenes átírási és fordítási eredményeket a rendszer beszéd észlelésekor adja vissza, és a Final Results is konvertálható a szintetizált beszédbe.

A Microsoft fordítói motorját két különböző módszer látja el: statisztikai gépi fordítás (SMT) és neurális gépi fordítás (NMT). A SMT speciális statisztikai elemzést használ a lehetséges fordítások megbecslésére néhány szó kontextusában. A NMT-alapú neurális hálózatokkal pontosabb és természetesebb fordításokat biztosítanak a szavak fordításához a mondatok teljes kontextusával.

A Microsoft jelenleg a NMT használja a legnépszerűbb nyelvekre való fordításhoz. A [beszéd-beszéd fordításhoz elérhető összes nyelv](language-support.md#speech-translation) a NMT. A beszédfelismerési és a szöveges fordítás a nyelvi pároktól függően SMT-t vagy NMT-t is használhat. Ha a NMT támogatja a célként megadott nyelvet, a teljes fordítás NMT-alapú. Ha a NMT nem támogatja a célként megadott nyelvet, a fordítás a NMT és az SMT hibrid változata, amely az angol nyelvet használja a két nyelv közötti "pivot" kifejezéssel.

## <a name="core-features"></a>Alapvető funkciók

A Speech SDK és a REST API-k segítségével az alábbi funkciók érhetők el:

| Használati eset | SDK | REST |
|----------|-----|------|
| Beszéd és szöveg közötti fordítás felismerési eredményekkel. | Igen | Nem |
| Beszéd – beszéd fordítás. | Igen | Nem |
| Az ideiglenes felismerés és a fordítás eredményei. | Igen | Nem |

## <a name="get-started-with-speech-translation"></a>Ismerkedés a beszédfelismerési fordítással

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a beszéd fordítási útmutatóinak nyelv alapján rendezett listáját tartalmazza.

| Gyors útmutató | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, .NET-keretrendszer](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Tallózás](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket foglalnak magukban, mint például a hang olvasása egy fájlból vagy adatfolyamból, a folyamatos és az egylövéses felismerés/fordítás, valamint az egyéni modellek használata.

* [Beszéd – szöveg és fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migrálási útmutatók

Ha alkalmazásai, eszközei vagy termékei a [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)használják, a rendszer útmutatók létrehozásával segít a Speech Services szolgáltatásba való Migrálás során.

* [Migrálás a Translator Speech APIról a Speech Services szolgáltatásba](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Beszédfelismerési eszközök SDK](speech-devices-sdk.md)
* [REST API: beszéd – szöveg](rest-speech-to-text.md)
* [REST API: szövegről beszédre](rest-text-to-speech.md)
* [REST API: kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
