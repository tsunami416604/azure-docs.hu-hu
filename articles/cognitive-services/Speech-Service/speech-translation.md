---
title: Beszédfordítás beszédszolgáltatással
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatás lehetővé teszi, hogy a beszéd végpontok között, valós idejű, többnyelvű fordítását adja hozzá alkalmazásaihoz, eszközeihez és eszközeihez. Ugyanez az API használható tolmácsoláshoz és beszéd lefordított szöveggé alakításához is.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052624"
---
# <a name="what-is-speech-translation"></a>Mi az a beszédfordítás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A beszédfelismerési szolgáltatásból származó beszédfordítás lehetővé teszi a hangfolyamok valós idejű, többnyelvű beszéd-beszéd és beszéd-szöveg fordítását. A beszédsdka segítségével az alkalmazások, eszközök és eszközök hozzáférhetnek a forrásátiratokhoz és a fordítási kimenetekhez a megadott hanghoz. A rendszer a beszédfelismerés észlelésekor ideiglenes transzkripciós és fordítási eredményeket ad vissza, a döntők eredményeit pedig szintetizált beszédté alakíthatja át.

A Microsoft fordítómotorját két különböző megközelítés hajtja: statisztikai gépi fordítás (SMT) és neurális gépi fordítás (NMT). Az SMT fejlett statisztikai elemzéssel becsüli meg a lehető legjobb fordításokat néhány szó kontextusában. Az NMT-vel a neurális hálózatok pontosabb, természetes hangzású fordításokat biztosítanak a mondatok teljes kontextusának használatával a szavak fordításához.

Ma a Microsoft az NMT-t használja a legnépszerűbb nyelvekre való fordításhoz. A [beszéd-beszéd fordításhoz rendelkezésre álló összes nyelvet](language-support.md#speech-translation) az NMT működteti. A beszéd-szöveg fordítás a nyelvpártól függően smt vagy NMT-t is használhat. Ha a célnyelvet az NMT támogatja, a teljes fordítás NMT-alapú. Ha az NMT nem támogatja a célnyelvet, a fordítás az NMT és az SMT hibridje, amely az angolt használja a két nyelv közötti "pivot"-ként.

## <a name="core-features"></a>Alapvető funkciók

A beszédfelismerési SDK- és REST API-kon keresztül elérhető funkciók a következők:

| Használati eset | SDK | REST |
|----------|-----|------|
| Beszéd-szöveg fordítás felismerési eredményekkel. | Igen | Nem |
| Beszéd-beszéd fordítás. | Igen | Nem |
| Időközi elismerés és fordítási eredmények. | Igen | Nem |

## <a name="get-started-with-speech-translation"></a>A beszédfordítás első lépései

Rövid útmutatókat kínálunk, amelyek célja, hogy kevesebb mint 10 perc alatt futtassa a kódot. Ez a táblázat a beszédfordítási rövid útmutatók listáját tartalmazza, nyelv szerint rendezve.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, .NET keretrendszer](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [Tallózás](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows, Linux, macOS | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

A beszédbeszéd SDK-hoz tartozó mintakód elérhető a GitHubon. Ezek a minták olyan gyakori forgatókönyveket fednek le, mint a hang fájlból vagy adatfolyamból történő olvasása, a folyamatos és az egyesélyes felismerés/fordítás, valamint az egyéni modellek kezelése.

* [Szöveggé és fordításra irányuló minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Migrálási útmutatók

Ha alkalmazásai, eszközei vagy termékei a [Translator Speech API-t](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)használják, útmutatókat hoztunk létre a beszédfelismerési szolgáltatásba való áttelepítéshez.

* [Áttelepítés a Fordító beszédfelismerési API-ról a beszédfelismerési szolgáltatásra](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referenciadokumentumok

* [Beszéd SDK](speech-sdk-reference.md)
* [Beszédeszközök SDK](speech-devices-sdk.md)
* [REST API: Beszéd-szöveg](rest-speech-to-text.md)
* [REST API: Szövegfelolvasás](rest-text-to-speech.md)
* [REST API: Kötegátírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
* [A beszédfelismerési SDK beolvasása](speech-sdk.md)
