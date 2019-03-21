---
title: Az Azure Speech Services tolmácsolás
titlesuffix: Azure Cognitive Services
description: A beszédszolgáltatások lehetővé teszik az alkalmazások, eszközök és eszközök teljes körű, valós idejű, többnyelvű fordítás beszéd hozzá. Az azonos API-t a speech beszéd és a hang-szöveg transzformációs fordítás használható.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 95682612b4b0fdb1baa5038039630e74abddb1a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890471"
---
# <a name="what-is-speech-translation"></a>Mit jelent a beszédalapú fordítási?

Az Azure beszédszolgáltatások, tolmácsolás lehetővé teszi, hogy a valós idejű, többnyelvű speech beszéd és hang-szöveg fordítása audiostreamek lejátszásával. A beszédfelismerés SDK-val az alkalmazások, eszközök és eszközök hozzáférése forrás beszédátírás díjjal, míg a fordítási kimenetek megadott Audio. Beszéd észlel, és a döntő eredmények létrehozható szintetizált, a rendszer ideiglenes beszédátírási és fordítási eredményeket ad.

A Microsoft fordítási motor működteti, két különböző megközelítést: statisztikai gépi fordítási (SMT) és a Neurális gépi fordítás (NMT). SMT speciális statisztikai elemzésekhez használja becslésére a legjobb lehetséges fordításokat, adott néhány szó a környezetben. NMT, a Neurális hálózatok szolgálnak mondatokat teljes kontextusában szó lefordítandó használatával pontosabb és természetes hangzó fordítások.

Jelenleg a Microsoft NMT való legnépszerűbb nyelvek használja. Az összes [speech tolmácsolás elérhető nyelvek](language-support.md#speech-translation) NMT működteti. Hang-szöveg transzformációs fordítási attól függően, a nyelv pár SMT vagy NMT használhatja. A Célnyelv NMT támogatja, a teljes fordítás esetén NMT-alapú. A Célnyelv NMT által nem támogatott, ha a fordítás egy hibrid NMT és SMT, mint "kimutatást" angol között a két nyelv használatával.

## <a name="core-features"></a>Alapfunkciók

Íme a Speech SDK és a REST API-kon keresztül elérhető szolgáltatások:

| Használati eset | SDK | REST |
|----------|-----|------|
| Hang-szöveg fordítását felismerési eredményeket. | Igen | Nem |
| Beszéd-az-beszédalapú fordítási. | Igen | Nem |
| Ideiglenes felismerése és fordítási eredményeket. | Igen | Nem |

## <a name="get-started-with-speech-translation"></a>Tolmácsolás – első lépések

Rövid útmutatók, kevesebb mint 10 perc múlva a kódot futtató úgy tervezték, hogy biztosítunk. Ez a táblázat tartalma speech fordítási útmutatóink nyelv szerint vannak rendezve.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, .NET-keretrendszer](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Tallózás](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

Mintakód a Speech SDK a Githubon érhető el. Ezek a minták például egy fájlhoz vagy adatfolyamhoz, folyamatos és egylépéses felismerése és fordítási való olvasás hang és egyéni modellek használata gyakori forgatókönyveket fednek le.

* [Hang-szöveg és a fordítás minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Áttelepítési útmutatók

> [!WARNING]
> Fordítói beszéd elvesznek a 2019. október 15.

Ha az alkalmazásokat, eszközöket és termékeket használ a Translator Speech, létrehoztuk útmutatók segítséget nyújtanak a beszédszolgáltatások át.

* [A Translator Speech API áttelepít a beszédszolgáltatások](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg-hang transzformációs](rest-text-to-speech.md)
* [REST API: A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
* [A beszédfelismerés SDK beszerzése](speech-sdk.md)
