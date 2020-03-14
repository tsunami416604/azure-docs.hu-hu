---
title: Beszédfelismerési szolgáltatás fordítása
titleSuffix: Azure Cognitive Services
description: A Speech Service lehetővé teszi, hogy teljes körű, valós idejű, többnyelvű fordítást adjon az alkalmazásaihoz, eszközeihez és eszközeihez. Az azonos API-t a speech beszéd és a hang-szöveg transzformációs fordítás használható.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f2f0b277fb9dc6270e9015b5bf3205cc8aceaa0a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371239"
---
# <a name="what-is-speech-translation"></a>Mi az a beszédfordítás?

A beszédfelismerési szolgáltatásból való beszéd fordítás lehetővé teszi a hangadatfolyamok valós idejű, több nyelvű beszéd-beszéd és beszéd – szöveg fordítását. A Speech SDK-val az alkalmazások, eszközök és eszközök hozzáférhetnek a forrás-átírásokhoz és a fordítási kimenetekhez a megadott hanghoz. Az ideiglenes átírási és fordítási eredményeket a rendszer beszéd észlelésekor adja vissza, és a Final Results is konvertálható a szintetizált beszédbe.

A Microsoft fordítói motorját két különböző módszer látja el: statisztikai gépi fordítás (SMT) és neurális gépi fordítás (NMT). A SMT speciális statisztikai elemzést használ a lehetséges fordítások megbecslésére néhány szó kontextusában. A NMT-alapú neurális hálózatokkal pontosabb és természetesebb fordításokat biztosítanak a szavak fordításához a mondatok teljes kontextusával.

A Microsoft jelenleg a NMT használja a legnépszerűbb nyelvekre való fordításhoz. A [beszéd-beszéd fordításhoz elérhető összes nyelv](language-support.md#speech-translation) a NMT. Hang-szöveg transzformációs fordítási attól függően, a nyelv pár SMT vagy NMT használhatja. Ha a NMT támogatja a célként megadott nyelvet, a teljes fordítás NMT-alapú. Ha a NMT nem támogatja a célként megadott nyelvet, a fordítás a NMT és az SMT hibrid változata, amely az angol nyelvet használja a két nyelv közötti "pivot" kifejezéssel.

## <a name="core-features"></a>Alapvető funkciók

A Speech SDK és a REST API-k segítségével az alábbi funkciók érhetők el:

| Használati eset | SDK | REST |
|----------|-----|------|
| Beszéd és szöveg közötti fordítás felismerési eredményekkel. | Igen | Nem |
| Beszéd – beszéd fordítás. | Igen | Nem |
| Az ideiglenes felismerés és a fordítás eredményei. | Igen | Nem |

## <a name="get-started-with-speech-translation"></a>Ismerkedés a beszédfelismerési fordítással

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a beszéd fordítási útmutatóinak nyelv alapján rendezett listáját tartalmazza.

| Első lépések | Platform | API-referencia |
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

Ha alkalmazásai, eszközei vagy termékei a [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)használják, akkor a beszédfelismerési szolgáltatásba való Migrálás megkönnyítéséhez létrehozott útmutatót is készítettünk.

* [Migrálás a Translator Speech APIról a beszédfelismerési szolgáltatásba](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Beszéd SDK](speech-sdk-reference.md)
* [Beszédfelismerési eszközök SDK](speech-devices-sdk.md)
* [REST API: beszéd – szöveg](rest-speech-to-text.md)
* [REST API: szövegről beszédre](rest-text-to-speech.md)
* [REST API: kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
