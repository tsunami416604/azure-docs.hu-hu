---
title: Mik az Azure Speech Services?
titleSuffix: Azure Cognitive Services
description: Az Azure Speech Services hang-szöveg transzformációs, szöveg-hang transzformációs és beszédalapú fordítási egy Azure-előfizetéssel való egyesítése is. Könnyedén adhat hozzá a beszédfelismerési, az alkalmazások, eszközök és -eszközök a Speech SDK, a beszéd Devices SDK-val vagy a REST API-k. Beszéd funkciók hozzáadása egy meglévő csevegőrobot, szöveg-hang transzformációs egy fordítási alkalmazásban konvertálni vagy nagy adatmennyiségek hívás center lefényképezze.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: dc0871c0b16246fa178072e9d9f266d4c02ba1c4
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002991"
---
# <a name="what-are-the-speech-services"></a>Mi az a Speech Services?

Az Azure Speech Services egy Azure-előfizetéssel egyesíti a hang-szöveg transzformációs, szöveg-hang transzformációs és tolmácsolás –. Beszéd könnyen engedélyezése az alkalmazások, eszközök és az eszközök a [beszéd SDK](speech-sdk-reference.md), [Speech Devices SDK-val](https://aka.ms/sdsdk-quickstart), vagy [REST API-k](rest-apis.md).

> [!IMPORTANT]
> Beszédszolgáltatások átvették a Bing Speech API, a Translator Speech és a Custom Speech. Lásd: *útmutató végigvezeti a > áttelepítési* áttelepítési utasításokért.

Ezeket a funkciókat az Azure Speech Services alkotják. Ebben a táblázatban hivatkozások segítségével tudjon meg többet a gyakori alkalmazási helyzetek az egyes szolgáltatásokhoz, vagy tallózással keresse meg az API-referencia.

| Szolgáltatás | Szolgáltatás | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Speech-to-text | Hang-szöveg transzformációs transcribes audio-adatfolyamokat valós idejű, amelyek az alkalmazások, eszközök és eszközök használata vagy megjelenítendő szöveg. Használja a hang-szöveg transzformációs a [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) átírt beszéd- és hangparancsokra reagálás származtassa felhasználói szándékot. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [A Batch Beszédátírási](batch-transcription.md) | Batch beszédátírási lehetővé teszi nagy mennyiségű adatot az aszinkron hang-szöveg transzformációs beszédátírási. Ez az egy REST-alapú szolgáltatás, amely ugyanazon végpontot használja, mint a Testreszabás és a modellkezelési. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| | [Beszélgetés Beszédátírási](conversation-transcription-service.md) | Lehetővé teszi a beszéd valós idejű felismerés, a beszélő felismerése és diarization. Tökéletes választás, a személyes értekezletek átírás lehetővé teszi az előadók megkülönböztetésére. | Igen | Nem |
| | [Egyéni beszédfelismerési modelleket hozhat létre](#customize-your-speech-experience) | Hang-szöveg transzformációs használatakor felismerése és a egy egyedi környezetben beszédátírási létrehozása, és címet a zajszint vagy iparág-specifikus szókincsből eredőket egyéni írásmódja, akusztikai és nyelvi modelleket taníthat be. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | Text-to-speech | Szöveg-hang transzformációs emberszerű szintetizált alakítja át a bemeneti szöveg. Válasszon a standard szintű beszédhangot és Neurális beszédhangot (lásd: [nyelvi támogatás](language-support.md)). | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Hozzon létre egyéni beszédhangot](#customize-your-speech-experience) | Hozzon létre egyéni hangtípust egyedi a márkáról vagy termék. | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Beszédfordítás](speech-translation.md) | Tolmácsolás | Beszédalapú fordítási lehetővé teszi az alkalmazások, eszközök és eszközök beszéd valós idejű, többnyelvű fordítás. A beszédfelismerés beszéd és hang-szöveg transzformációs fordítási szolgáltatás segítségével. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nem |
| [Hang-és felhőközpontú virtuális asszisztensek](voice-first-virtual-assistants.md) | Hang-és felhőközpontú virtuális asszisztensek | Az Azure Speech Services segítségével egyéni virtuális asszisztensek amelyekkel a fejlesztők természetes, emberi intelligenciára hasonlító párbeszédfelületeket a saját alkalmazások és az elemek létrehozásához. A Bot Framework közvetlen vonal beszédfelismerő csatornát ezeket a képességeket egy összehangolt, előkészített belépési pontot egy kompatibilis robot, amely lehetővé teszi a beszéd a hangalapú, alacsony késéssel és magas megbízhatósággal-szal való megadásával növeli. | [Igen](voice-first-virtual-assistants.md) | Nem |

## <a name="news-and-updates"></a>Hírek és frissítések

Ismerje meg, mi az az Azure Speech Services új.

* 2019. május - dokumentációja már elérhető a [beszélgetés Beszédátírási](conversation-transcription-service.md), [Center meghívása átírást](call-center-transcription.md), és [hang-és felhőközpontú virtuális asszisztensek](voice-first-virtual-assistants.md).
* 2019. május
    * Kiadás dátuma: SDK 1.5.1 Speech. Frissítéseket, fejlesztéseket és ismert problémák teljes listáját lásd: [kibocsátási megjegyzések](releasenotes.md).
    * Kiadás dátuma: Speech SDK 1.5.0. Frissítéseket, fejlesztéseket és ismert problémák teljes listáját lásd: [kibocsátási megjegyzések](releasenotes.md).
* Április a 2019 - kiadott beszéd SDK 1.4.0-s C++, a szöveg-hang transzformációs (bétaverzió) támogatásával C#, és a Windows és Linux rendszeren Java használatával. Emellett az SDK mostantól támogatja a MP3 és Opus/Ogg hangformátumok Pro C++ és C# Linux rendszeren. Frissítéseket, fejlesztéseket és ismert problémák teljes listáját lásd: [kibocsátási megjegyzések](releasenotes.md).
* Március 2019 - szövegfelolvasás, amely egy adott régióban elérhető beszédhangot teljes listáját adja vissza az új végpont már elérhető. Új régiók emellett mostantól használható az szöveg-beszéd átalakítás. További információkért lásd: [szöveg-hang transzformációs API-referencia (REST)](rest-text-to-speech.md).

## <a name="try-speech-services"></a>Próbálja ki a beszédszolgáltatások

Útmutatók a népszerű programozási nyelvek, minden egyes, a kód futtatása kevesebb mint 10 perc múlva úgy tervezték, hogy biztosítunk. Ez a táblázat tartalmazza a legnépszerűbb rövid útmutatók, az egyes szolgáltatásokhoz. A bal oldali navigációs használatával további nyelvek és platformok.

| Speech-to-text (SDK) | Szöveg-hang transzformációs (SDK) | Translation (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET-keretrendszer (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (böngésző)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET-keretrendszer (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Hang-szöveg és a szöveg-hang transzformációs is REST-végpontokat és kapcsolódó útmutatók.

Miután már kellett, hogy egy alkalommal a beszédszolgáltatások használatát, a jelen oktatóanyagban, amely bemutatja, hogyan ismerhetik fel a szándék fog vonatkozni, a beszéd, a beszéd SDK és a LUIS segítségével próbálja meg.

* [Oktatóanyag: A beszédfelismerés SDK és a LUIS, beszédfelismerés, szándék felismeréseC#](how-to-recognize-intents-from-speech-csharp.md)
* [Oktatóanyag: Szöveg fordítása, vélemények elemzése és a lefordított szöveg-beszéd átalakítás, REST szintetizálásához Flask alkalmazás készítése](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Mintakód letöltése

Mintakód az Azure Speech Services mindegyike elérhető a Githubon. Ezek a minták például egy fájlhoz vagy adatfolyamhoz, folyamatos és egylépéses felismerés, a hang olvasásakor és egyéni modellek használata gyakori forgatókönyveket fednek le. Ezek a hivatkozások segítségével megtekintheti SDK-t és a REST-minták:

* [Hang-szöveg transzformációs, felolvasást és tolmácsolást fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [A Batch beszédátírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Szöveg-hang transzformációs minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customize-your-speech-experience"></a>A beszédfelismerés testreszabásához

Az Azure Speech Services nagyszerűen működik a beépített modellek, azonban érdemes lehet további testre szabhatja és finomhangolása a termék vagy a környezet élményét. Testreszabási lehetőségek köre, a akusztikai modell finomhangolása a saját márkáját az egyedi hangtípusokkal. Miután létrehozott egy egyéni modell, használhatja az Azure Speech Services bármelyikével.

| Beszédszolgáltatás | Modell | Leírás |
|----------------|-------|-------------|
| Diktálás | [Akusztikai modell](how-to-customize-acoustic-models.md) | Alkalmazások, eszközök, egy egyéni akusztikai modell létrehozásához vagy eszközöket, amelyek a megadott többek között az autó vagy gyárban, mindegyike adott felvétel feltételek. Ilyenek például a ékezetes speech, adott háttér egy olyan vagy a rögzítés adott mikrofon segítségével. |
| | [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modell, mező-specifikus szóhasználatának és nyelvtani, például terminológiát vagy informatikai szakkifejezéseivel beszédátírási javítása érdekében. |
| | [Kiejtési modell](how-to-customize-pronunciation.md) | Egy egyéni írásmódja modellel meghatározhatja fonetikus formában és megjelenített egy szó vagy kifejezés. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt. |
| Szövegfelolvasás | [Hangtípusok](how-to-customize-voice-font.md) | Egyéni hangtípust lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját. Csak egy kis mennyiségű adatot a kezdéshez vesz igénybe. Minél több adatot ad meg, a további természetes és az emberi intelligenciára hasonlító elemeket az hangtípusú fog hang-, hogy. |

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg-hang transzformációs](rest-text-to-speech.md)
* [REST API: A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
