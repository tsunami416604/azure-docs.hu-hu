---
title: Hang-szöveg transzformációs Azure Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Hang-szöveg transzformációs Azure Speech Services, más néven hang-szöveg transzformációs, lehetővé teszi, hogy a valós idejű beszédátírási-audiostreamek lejátszásával, szöveg, amelyet felhasználhat az alkalmazások, eszközök és eszközök, az megjelenítéséhez, és a művelet végrehajtása a parancs bemenetként. Ez a szolgáltatás ugyanazon felismerés technológia, amely a Microsoft Cortana és az Office-termékek használ, és zökkenőmentesen együttműködik a fordítás és a szöveg-hang transzformációs működteti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 7aa4492a22c8aca1e54570adb2811e4cb13caf7b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900777"
---
# <a name="what-is-speech-to-text"></a>Mit jelent a hang-szöveg transzformációs?

Hang-szöveg transzformációs Azure Speech Services, más néven hang-szöveg transzformációs, lehetővé teszi, hogy a valós idejű beszédátírási-audiostreamek lejátszásával, szöveg, amelyet felhasználhat az alkalmazások, eszközök és eszközök, az megjelenítéséhez, és a művelet végrehajtása a parancs bemenetként. Ez a szolgáltatás ugyanazon felismerés technológia, amely a Microsoft Cortana és az Office-termékek használ, és zökkenőmentesen együttműködik a fordítás és a szöveg-hang transzformációs működteti.

Alapértelmezés szerint a hang-szöveg transzformációs szolgáltatás univerzális nyelvi modellt használja. Ez a modell a Microsoft tulajdonában lévő adatok felhasználásával lett betanított, és a felhőben üzembe helyezett. Az optimális legyen természetes nyelvi és Diktálás forgatókönyvek. Hang-szöveg transzformációs használatakor felismerése és a egy egyedi környezetben beszédátírási létrehozása, és címet a zajszint vagy iparág-specifikus szókincsből eredőket egyéni írásmódja, akusztikai és nyelvi modelleket taníthat be.

Egyszerűen mikrofon hangjának rögzítéséhez, olvassa el a streamből vagy hang fájlok elérésére a Speech SDK és a REST API-k a storage-ból. A beszédfelismerés SDK támogatja a WAV/PCM 16 bites, 16 kHz, egyetlen-channel hangot beszédfelismerési. További hangformátumok támogatottak használatával a [hang-szöveg transzformációs REST-végpont](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-formats) vagy a [a batch szolgáltatás beszédátírási](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

## <a name="core-features"></a>Alapfunkciók

Íme a Speech SDK és a REST API-kon keresztül elérhető szolgáltatások:

| Használati eset | SDK | REST |
|----------|-----|------|
| Rövid utterances lefényképezze (< 15 másodperc). Csak a végleges átírást eredmény támogatja. | Igen | Igen |
| Folyamatos beszédátírási hosszú kimondott szöveg és a streamelési hang (> 15 másodperc). Közbenső és végleges átírást eredmények támogatja. | Igen | Nem |
| Leképezések származtatást felismerési eredményeket az [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis). | Igen | nem\* |
| Batch-hangfájlok beszédátírási aszinkron módon történik. | Nem | igen\** |
| Létrehozhat és kezelhet beszédfelismerési modelleket. | Nem | igen\** |
| Hozzon létre, és egyéni modell központi telepítések felügyeletéhez szükséges. | Nem | igen\** |
| Hozzon létre egyéni modellek és a modell pontosságát mérésére pontossági tesztek. | Nem | igen\** |
| Előfizetések kezelése. | Nem | igen\** |

\* *A LUIS szándékok és entitások egy külön LUIS-előfizetése lehet nyerni. Ennek az előfizetésnek az SDK LUIS hívja meg, és adja meg az entitás- és szándékának eredményeket. A LUIS hívása a REST API-val saját magának, hogy szándékokat és entitásokat a LUIS-előfizetéshez.*

\** *Ezek a szolgáltatások érhetők el a cris.ai végpont használatával. Lásd: [referencia Swagger](https://westus.cris.ai/swagger/ui/index).*

## <a name="get-started-with-speech-to-text"></a>Ismerkedés a hang-szöveg

Útmutatók a népszerű programozási nyelvek, minden egyes, a kód futtatása kevesebb mint 10 perc múlva úgy tervezték, hogy biztosítunk. Ez a táblázat nyelv szerint vannak rendezve, beszéd SDK rövid útmutatók teljes listáját tartalmazza.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| [C#, .NET Core](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnetcore-windows) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, .NET-keretrendszer](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-dotnet-windows) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-csharp-uwp) | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-windows) | Windows | [Tallózás](https://aka.ms/csspeech/cppref)|
| [C++](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-cpp-linux) | Linux | [Tallózás](https://aka.ms/csspeech/cppref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-android) | Android | [Tallózás](https://aka.ms/csspeech/javaref) |
| [Java](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-java-jre) | Windows, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| [A JavaScript, a böngésző](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-browser) | Böngésző, Windows, Linux, macOS | [Tallózás](https://aka.ms/AA434tv) |
| [Javascript, Node.js](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-js-node) | Windows, Linux, macOS | [Tallózás](https://aka.ms/AA434tv) |
| [Objective-C](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-objectivec-ios) | iOS | [Tallózás](https://aka.ms/csspeech/objectivecref) |
| [Python](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python) | Windows, Linux, macOS | [Tallózás](https://aka.ms/AA434tr)  |

Ha inkább a hang-szöveg transzformációs REST-szolgáltatás használatára, lásd: [REST API-k](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

## <a name="tutorials-and-sample-code"></a>Útmutatók és Kódminták

Miután már kellett, hogy egy alkalommal a beszédszolgáltatások használatát, a jelen oktatóanyagban, amely bemutatja, hogyan ismerhetik fel a szándék fog vonatkozni, a beszéd, a beszéd SDK és a LUIS segítségével próbálja meg.

* [Oktatóanyag: A beszédfelismerés SDK és a LUIS, beszédfelismerés, szándék felismeréseC#](how-to-recognize-intents-from-speech-csharp.md)

Mintakód a Speech SDK a Githubon érhető el. Ezek a minták például egy fájlhoz vagy adatfolyamhoz, folyamatos és egylépéses felismerés, a hang olvasásakor és egyéni modellek használata gyakori forgatókönyveket fednek le.

* [Hang-szöveg transzformációs minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [A Batch beszédátírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Testreszabás

A beszéd-szolgáltatások által használt univerzális modell mellett hozhat létre egyéni írásmódja, akusztikai és nyelvi modelleket adott a használatával. A következő testreszabási lehetőségek listáját:

| Modell | Leírás |
|-------|-------------|
| [Akusztikai modell](how-to-customize-acoustic-models.md) | Egy egyéni akusztikai modell létrehozása akkor hasznos, ha az alkalmazást, eszközöket vagy eszközök szerepelnek az adott környezetben, például egy autó vagy a gyári adott felvétel feltételekkel. A példák közé tartozik az akcentussal való beszéd, a meghatározott háttérzajok vagy egy adott mikrofon használata a felvételhez. |
| [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modell, iparág-specifikus szóhasználatának és nyelvtani, például terminológiát vagy informatikai szakkifejezéseivel beszédátírási javítása érdekében. |
| [Kiejtési modell](how-to-customize-pronunciation.md) | Egy egyéni írásmódja modellel meghatározhatja fonetikus formában és megjelenített egy szó vagy kifejezés. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt. |

> [!NOTE]
> Testreszabási lehetőségek minden nyelvi és területi beállítása szerint (lásd: [támogatott nyelvek](supported-languages.md)).

## <a name="migration-guides"></a>Áttelepítési útmutatók

> [!WARNING]
> Bing Speech elvesznek a 2019. október 15.

Ha az alkalmazásokat, eszközöket és termékeket használ, a Bing Speech API-k vagy a Custom Speech, létrehoztuk útmutatók segítséget beszédszolgáltatások át.

* [A Speech Services a Bing Speech áttelepítése](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Egyéni Beszédmodell áttelepít a beszédszolgáltatások](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg-hang transzformációs](rest-text-to-speech.md)
* [REST API: A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
* [A beszédfelismerés SDK beszerzése](speech-sdk.md)
