---
title: Mi az a Speech Service?
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg-beszéd és a beszéd fordításának egységesítése egyetlen Azure-előfizetésbe. Egyszerűen felveheti az alkalmazásokat, eszközöket és eszközöket a Speech SDK, a Speech Devices SDK vagy a REST API-k használatával. Beszédfelismerési funkciókat adhat hozzá egy meglévő csevegési robothoz, szöveg-beszéd átalakítást alakíthat át egy fordítási alkalmazásban, vagy nagy mennyiségű Call Center-adat átírásával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1d722d7e2886008aa5aa3acff8095fcf35ac38d8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554185"
---
# <a name="what-are-the-speech-services"></a>Mi az a Speech Services?

Az Azure Speech Services a beszéd-szöveg, a szöveg és a beszéd egyesítése, valamint a beszédfelismerés egyetlen Azure-előfizetésbe való fordítása. A Speech [SDK](speech-sdk-reference.md), a [SPEECH Devices SDK](https://aka.ms/sdsdk-quickstart)vagy a [REST API](rest-apis.md)-k segítségével egyszerűen elvégezheti az alkalmazások, eszközök és eszközök használatát.

> [!IMPORTANT]
> A Speech Services Bing Speech API, Translator Speech és Custom Speech váltotta fel. Tekintse át az áttelepítési utasítások *>* áttelepítési útmutatókat.

Ezek a funkciók alkotják az Azure Speech Services szolgáltatást. Az ebben a táblázatban található hivatkozásokkal többet tudhat meg az egyes funkciók általános használati eseteiről, vagy böngészhet az API-referenciában.

| Szolgáltatás | Funkció | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Beszéd – szöveg](speech-to-text.md) | Speech-to-text | A beszéd-szöveg szöveggé átmásolja a hangadatfolyamokat valós időben a szöveggé, hogy alkalmazásai, eszközei vagy eszközei képesek legyenek a felhasználásra vagy a megjelenítésre. A beszéd-szöveg és a [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) használatával származtatott beszédből származó felhasználói leképezéseket származtathat, és hangparancsokat alkalmazhat. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Kötegelt átírás](batch-transcription.md) | A Batch-átírás lehetővé teszi a nagy mennyiségű adatforgalom aszinkron beszéd-szöveggé átírását. Ez egy REST-alapú szolgáltatás, amely ugyanazt a végpontot használja, mint a Testreszabás és a modell kezelése. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| | [Beszélgetés átirata](conversation-transcription-service.md) | Lehetővé teszi a valós idejű beszédfelismerést, a hangszórók azonosítását és a diarization. A személyes értekezletek átírása tökéletes választás a hangszórók megkülönböztetésére. | Igen | Nem |
| | [Custom Speech modellek létrehozása](#customize-your-speech-experience) | Ha egy egyedi környezetben beszéd-szövegeket használ az elismeréshez és az átíráshoz, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zajok vagy az iparági specifikus szókincsek kezeléséhez. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| [Szöveg – beszéd](text-to-speech.md) | Szövegfelolvasás | A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi környezetbe, például a [Speech szintézis Markup Language (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml)használatával. A standard hangok és a neurális hangok közül választhat (lásd a [nyelvi támogatást](language-support.md)). | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Egyéni hangok létrehozása](#customize-your-speech-experience) | Egyedi hangbetűkészletek létrehozása a márka vagy a termék számára. | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Beszédfordítás](speech-translation.md) | Tolmácsolás | A beszédfelismerés valós idejű, többnyelvű fordítást tesz lehetővé alkalmazásaihoz, eszközeihez és eszközeihez. Használja ezt a szolgáltatást beszéd – beszéd és beszéd – szöveg fordításához. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nem |
| [Hang – első virtuális asszisztens](voice-first-virtual-assistants.md) | Hang – első virtuális asszisztens | Az Azure Speech Servicest használó egyéni virtuális asszisztensek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban. A bot Framework közvetlen vonalas beszédének csatornája a következő funkciókat biztosítja: egy koordinált, összehangolt belépési pont egy olyan kompatibilis robot számára, amely lehetővé teszi a hangfelismerést, az alacsony késéssel és a magas megbízhatósággal való interakciót. | [Igen](voice-first-virtual-assistants.md) | Nem |

## <a name="news-and-updates"></a>Hírek és frissítések

Ismerje meg az Azure Speech Services újdonságait.

* Június 2019
    * Megjelent a Speech SDK 1.6.0. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
* Május 2019 – a dokumentáció már elérhető a [beszélgetés](conversation-transcription-service.md)átírásához, a [Call Center](call-center-transcription.md)átírásához és a hangvezérelt [virtuális asszisztensekhez](voice-first-virtual-assistants.md).
* 2019. május
    * Megjelent Speech SDK 1.5.1. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
    * Megjelent Speech SDK 1.5.0. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
* Április 2019 – megjelent a Speech SDK 1.4.0 C++, amely támogatja a (z), C#és a Java-t a Windows és Linux rendszeren. Emellett az SDK mostantól támogatja az C++ MP3 és az Opus/OGG hangformátumot a és C# a Linux rendszeren. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
* Március 2019 – egy új, szöveg-beszéd (TTS) végpont, amely egy adott régióban elérhető hangok teljes listáját adja vissza. Emellett az új régiók már támogatottak a TTS-ben. További információ: [text-to-SPEECH API-referenciák (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>A Speech Services kipróbálása

A legnépszerűbb programozási nyelveken gyors útmutatókat ajánlunk, amelyek mindegyike kevesebb, mint 10 perc alatt futtathatja a kódot. Ez a táblázat az egyes szolgáltatásokhoz tartozó legnépszerűbb gyors útmutatókat tartalmazza. A bal oldali navigációs sávon további nyelveket és platformokat is felhasználhat.

| Beszéd – szöveg (SDK) | Szöveg – beszéd (SDK) | Translation (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET-keretrendszer (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (böngésző)](quickstart-js-browser.md) | [C++Windows](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++Linux](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET-keretrendszer (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++Windows](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> A beszédfelismerés és a szöveg beszéde is a REST-végpontokkal és a hozzájuk kapcsolódó gyors útmutatókkal is rendelkezik.

A Speech Services használatának lehetősége után próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédfelismerési szándékokat a Speech SDK és a LUIS használatával.

* [Oktatóanyag: Beszédfelismerési szándék felismerése a Speech SDK és a LUIS használatávalC#](how-to-recognize-intents-from-speech-csharp.md)
* [Oktatóanyag: Hozzon létre egy lombik-alkalmazást a szöveg fordításához, a hangulat elemzéséhez és a lefordított szöveg kiépítéséhez beszédre, REST-re](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Mintakód letöltése

A mintakód minden Azure Speech Services esetében elérhető a GitHubon. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

* [Beszéd – szöveg, szöveg-beszéd és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>A beszédfelismerési élmény testreszabása

Az Azure Speech Services jól működik a beépített modellekkel, azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között. Miután létrehozott egy egyéni modellt, használhatja azt bármelyik Azure Speech Services-szolgáltatással.

| Beszédszolgáltatás | Modell | Leírás |
|----------------|-------|-------------|
| Diktálás | [Akusztikai modell](how-to-customize-acoustic-models.md) | Hozzon létre egy egyéni akusztikai modellt olyan alkalmazásokhoz, eszközökhöz vagy eszközökhöz, amelyeket az adott környezetben, például autóban vagy gyári szinten használnak, és amelyek külön rögzítési feltételekkel rendelkeznek. Ilyenek például az ékezetes beszédek, a konkrét háttérzajok, vagy egy adott mikrofon használata a rögzítéshez. |
| | [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modellt, amellyel javítható a mező-specifikus szókincs és nyelvtan, például az orvosi terminológia vagy az informatikai zsargon átirata. |
| | [Kiejtési modell](how-to-customize-pronunciation.md) | Egyéni kiejtési modellel megadhatja egy szó vagy kifejezés fonetikus formáját és megjelenítését. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt. |
| Szövegfelolvasás | [Hangtípusok](how-to-customize-voice-font.md) | Az egyéni hangbetűkészletek lehetővé teszik a márka felismerhető, egyfajta hangjának létrehozását. A kezdéshez csak kis mennyiségű adat szükséges. Minél több, az Ön által megadott információ, annál természetesebb és emberi – például a hangbetűkészletek is megszólalnak. |

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)
* [REST API: Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
