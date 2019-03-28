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
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 06b2a5211c720d50a2f14e5fa56fa296cb80d41f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519629"
---
# <a name="what-are-the-speech-services"></a>Mi az a Speech Services?

Az Azure Speech Services egy Azure-előfizetéssel egyesíti a hang-szöveg transzformációs, szöveg-hang transzformációs és tolmácsolás –. Beszéd könnyen engedélyezése az alkalmazások, eszközök és az eszközök a [beszéd SDK](speech-sdk-reference.md), [Speech Devices SDK-val](speech-devices-sdk-qsg.md), vagy [REST API-k](rest-apis.md).

> [!IMPORTANT]
> Beszédszolgáltatások átvették a Bing Speech API, a Translator Speech és a Custom Speech. Lásd: *útmutató végigvezeti a > áttelepítési* áttelepítési utasításokért.

Ezeket a funkciókat az Azure Speech Services alkotják. Ebben a táblázatban hivatkozások segítségével tudjon meg többet a gyakori alkalmazási helyzetek az egyes szolgáltatásokhoz, vagy tallózással keresse meg az API-referencia.

| Szolgáltatás | Szolgáltatás | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Speech-to-Text](speech-to-text.md) | Diktálás | Hang-szöveg transzformációs transcribes audio-adatfolyamokat valós idejű, amelyek az alkalmazások, eszközök és eszközök használata vagy megjelenítendő szöveg. Használja a hang-szöveg transzformációs a [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) átírt beszéd- és hangparancsokra reagálás származtassa felhasználói szándékot. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [A Batch Beszédátírási](batch-transcription.md) | Batch beszédátírási lehetővé teszi nagy mennyiségű adatot az aszinkron hang-szöveg transzformációs beszédátírási. Ez az egy REST-alapú szolgáltatás, amely ugyanazon végpontot használja, mint a Testreszabás és a modellkezelési. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| | [Testreszabás](#customize-your-speech-experience) | Hang-szöveg transzformációs használatakor felismerése és a egy egyedi környezetben beszédátírási létrehozása, és címet a zajszint vagy iparág-specifikus szókincsből eredőket egyéni írásmódja, akusztikai és nyelvi modelleket taníthat be. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| [Text-to-Speech](text-to-speech.md) | Szövegfelolvasás | Szöveg-hang transzformációs emberszerű szintetizált alakítja át a bemeneti szöveg. Válasszon a standard szintű beszédhangot és Neurális beszédhangot (lásd: [nyelvi támogatás](language-support.md)). | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Testreszabás](#customize-your-speech-experience) | Hozzon létre egyéni hangtípust egyedi a márkáról vagy termék. | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Beszédfordítás](speech-translation.md) | Beszédfordítás | Beszédalapú fordítási lehetővé teszi az alkalmazások, eszközök és eszközök beszéd valós idejű, többnyelvű fordítás. A beszédfelismerés beszéd és hang-szöveg transzformációs fordítási szolgáltatás segítségével. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nem |

## <a name="news-and-updates"></a>Hírek és frissítések

Ismerje meg, mi az az Azure Speech Services új.

* Március 2019 - szöveg-beszéd átalakítás, amely egy adott régióban elérhető beszédhangot teljes listáját adja vissza egy új végpont már elérhető. További információkért lásd: [szöveg-hang transzformációs API-referencia (REST)](rest-text-to-speech.md).
* Február a 2019 - kiadott beszéd SDK 1.3.0 támogatása [Unity (bétaverzió)](quickstart-csharp-unity.md). Támogatása hozzáadva a `AudioInput` osztály, amely lehetővé teszi a streamelési forrás Audio kiválasztását. Fejlesztések és ismert problémák teljes listáját lásd: [kibocsátási megjegyzések](releasenotes.md).
* December 2018 - kiadott beszéd SDK 1.2.0-s vagy annál újabb támogatása [Python](quickstart-python.md) és [Node.js](quickstart-js-node.md), valamint Ubuntu 18.04 LTS. További információkért lásd: [kibocsátási megjegyzések](releasenotes.md).

## <a name="try-speech-services"></a>Próbálja ki a beszédszolgáltatások

Útmutatók a népszerű programozási nyelvek, minden egyes, a kód futtatása kevesebb mint 10 perc múlva úgy tervezték, hogy biztosítunk. Ez a táblázat tartalmazza a legnépszerűbb rövid útmutatók, az egyes szolgáltatásokhoz. A bal oldali navigációs használatával további nyelvek és platformok.

| Speech-to-text (SDK) | Translation (SDK) | Szöveg-hang transzformációs (REST) |
|-------------------|-------------------|-----------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) | [Python (Windows, Linux, macOS)](quickstart-python-text-to-speech.md) |
| [JavaScript (böngésző)](quickstart-js-browser.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) | [C#, .NET Core (Windows, Linux, macOS)](quickstart-dotnet-text-to-speech.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C#, .NET-keretrendszer (Windows)](quickstart-translate-speech-dotnetframework-windows.md) | [Node.js (Windows, Linux, macOS)](quickstart-nodejs-text-to-speech.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) | |

Miután már kellett, hogy egy alkalommal a beszédszolgáltatások használatát, a jelen oktatóanyagban, amely bemutatja, hogyan ismerhetik fel a szándék fog vonatkozni, a beszéd, a beszéd SDK és a LUIS segítségével próbálja meg.

* [Oktatóanyag: A beszédfelismerés SDK és a LUIS, beszédfelismerés, szándék felismeréseC#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="get-sample-code"></a>Mintakód letöltése

Mintakód az Azure Speech Services mindegyike elérhető a Githubon. Ezek a minták például egy fájlhoz vagy adatfolyamhoz, folyamatos és egylépéses felismerés, a hang olvasásakor és egyéni modellek használata gyakori forgatókönyveket fednek le. Ezek a hivatkozások segítségével megtekintheti SDK-t és a REST-minták:

* [Hang-szöveg és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
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
