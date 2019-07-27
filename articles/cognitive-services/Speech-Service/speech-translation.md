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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cfcefd0b18831163324519b61dbea305f90f44bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552640"
---
# <a name="what-is-speech-translation"></a>Mi a beszéd fordítása?

Az Azure Speech Servicesből való beszéd-fordítás lehetővé teszi a hangadatfolyamok valós idejű, több nyelvű beszédfelismerési és beszéd-szöveges fordítását. A Speech SDK-val az alkalmazások, eszközök és eszközök hozzáférhetnek a forrás-átírásokhoz és a fordítási kimenetekhez a megadott hanghoz. Az ideiglenes átírási és fordítási eredményeket a rendszer beszéd észlelésekor adja vissza, és a Final Results is konvertálható a szintetizált beszédbe.

A Microsoft fordítói motorját két különböző módszer látja el: statisztikai gépi fordítás (SMT) és neurális gépi fordítás (NMT). A SMT speciális statisztikai elemzést használ a lehetséges fordítások megbecslésére néhány szó kontextusában. A NMT-alapú neurális hálózatokkal pontosabb és természetesebb fordításokat biztosítanak a szavak fordításához a mondatok teljes kontextusával.

A Microsoft jelenleg a NMT használja a legnépszerűbb nyelvekre való fordításhoz. Az összes [speech tolmácsolás elérhető nyelvek](language-support.md#speech-translation) NMT működteti. Hang-szöveg transzformációs fordítási attól függően, a nyelv pár SMT vagy NMT használhatja. Ha a NMT támogatja a célként megadott nyelvet, a teljes fordítás NMT-alapú. Ha a NMT nem támogatja a célként megadott nyelvet, a fordítás a NMT és az SMT hibrid változata, amely az angol nyelvet használja a két nyelv közötti "pivot" kifejezéssel.

## <a name="core-features"></a>Alapvető funkciók

A Speech SDK és a REST API-k segítségével az alábbi funkciók érhetők el:

| Használati eset | SDK | REST |
|----------|-----|------|
| Beszéd és szöveg közötti fordítás felismerési eredményekkel. | Igen | Nem |
| Beszéd – beszéd fordítás. | Igen | Nem |
| Az ideiglenes felismerés és a fordítás eredményei. | Igen | Nem |

## <a name="get-started-with-speech-translation"></a>Ismerkedés a beszédfelismerési fordítással

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a beszéd fordítási útmutatóinak nyelv alapján rendezett listáját tartalmazza.

| Gyors üzembe helyezés | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, .NET-keretrendszer](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket foglalnak magukban, mint például a hang olvasása egy fájlból vagy adatfolyamból, a folyamatos és az egylövéses felismerés/fordítás, valamint az egyéni modellek használata.

* [Beszéd – szöveg és fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Áttelepítési útmutatók

Ha alkalmazásai, eszközei vagy termékei a [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)használják, a rendszer útmutatók létrehozásával segít a Speech Services szolgáltatásba való Migrálás során.

* [Migrálás a Translator Speech APIról a Speech Services szolgáltatásba](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)
* [REST API: Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
