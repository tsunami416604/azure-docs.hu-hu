---
title: Mi az a Speech Services?
titleSuffix: Azure Cognitive Services
description: A Speech Services a beszéd-szöveg, a szöveg és a beszéd egyesítése és a beszéd fordítása egyetlen Azure-előfizetésbe. Egyszerűen felveheti az alkalmazásokat, eszközöket és eszközöket a Speech SDK, a Speech Devices SDK vagy a REST API-k használatával. Beszédfelismerési funkciókat adhat hozzá egy meglévő csevegési robothoz, szöveg-beszéd átalakítást alakíthat át egy fordítási alkalmazásban, vagy nagy mennyiségű Call Center-adat átírásával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 993064a36bd12c95e83ffb7c9635c3e4dfcec0f0
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803345"
---
# <a name="what-are-the-speech-services"></a>Mi az a Speech Services?

A Speech Services a beszéd-szöveg, a szöveg és a beszéd egyesítése, valamint a beszédfelismerés egyetlen Azure-előfizetésbe való fordítása. A Speech [SDK](speech-sdk-reference.md), a [SPEECH Devices SDK](https://aka.ms/sdsdk-quickstart)vagy a [REST API](rest-apis.md)-k segítségével egyszerűen elvégezheti az alkalmazások, eszközök és eszközök használatát.

> [!IMPORTANT]
> A Speech Services Bing Speech API, Translator Speech és Custom Speech váltotta fel. Tekintse át az áttelepítési utasítások *> áttelepítési* útmutatókat.

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

* Szeptember 2019
  * Megjelent a Speech SDK 1.7.0. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
* Augusztus 2019
  * **Új oktatóanyag**: [Robot hangvezérlésének engedélyezése a Speech SDK, C# használatával](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Új, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles)stílusú, a `en-US-JessaNeural` hanghoz hozzáadott beszéd stílusa. 
* Június 2019
  * Megjelent a Speech SDK 1.6.0. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
* Május 2019 – a dokumentáció már elérhető a [beszélgetés átírásához](conversation-transcription-service.md), a [Call Center átírásához](call-center-transcription.md)és a [hangvezérelt virtuális asszisztensekhez](voice-first-virtual-assistants.md).
* 2019. május
  * Megjelent Speech SDK 1.5.1. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.
  * Megjelent Speech SDK 1.5.0. A frissítések, fejlesztések és ismert problémák teljes listáját a [kibocsátási megjegyzések](releasenotes.md)című részben tekintheti meg.

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
* [Oktatóanyag: Hang engedélyezése a robotnak a Speech SDK- C#val, ](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Oktatóanyag: Hozzon létre egy lombik-alkalmazást a szöveg fordításához, a hangulat elemzéséhez és a fordítás szövegének lefordításához, REST @ no__t-0

## <a name="get-sample-code"></a>Mintakód letöltése

A mintakód minden Azure Speech Services esetében elérhető a GitHubon. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

* [Beszéd – szöveg, szöveg-beszéd és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Hang – első virtuális asszisztensi minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>A beszédfelismerési élmény testreszabása

Az Azure Speech Services jól működik a beépített modellekkel, azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között. Miután létrehozott egy egyéni modellt, használhatja azt bármelyik Azure Speech Services-szolgáltatással.

| Beszédszolgáltatás | Platform | Leírás |
|----------------|-------------|-------------|
| Diktálás | [Egyéni beszédfelismerés](https://aka.ms/customspeech) | Testreszabhatja a beszédfelismerési modelleket igényeihez és rendelkezésre álló adataihoz. Kiküszöbölheti a beszédfelismerést akadályozó tényezőket, például a különféle beszédstílusok, a háttérzaj vagy a szókincs által okozott problémákat. |
| Szövegfelolvasás | [Egyéni hang](https://aka.ms/customvoice) | Felismerhető, egyfajta hangfelismerést készíthet a szöveg-beszéd alkalmazások számára, és elérhetővé teheti azokat. A hangkimeneteket tovább finomíthatja a hangparaméterek készletének módosításával. |

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)
* [REST API: Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
