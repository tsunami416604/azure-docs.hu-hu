---
title: A Speech SDK – beszédfelismerés szolgáltatás ismertetése
titleSuffix: Azure Cognitive Services
description: A Speech szoftverfejlesztői készlet (SDK) számos beszédfelismerési szolgáltatást tesz elérhetővé, így egyszerűbbé válik a beszédfelismerést támogató alkalmazások fejlesztése.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 79f7924b021de9426eeb66adf2ec12f8033efcea
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056856"
---
# <a name="about-the-speech-sdk"></a>A Speech SDK ismertetése

A Speech szoftverfejlesztői készlet (SDK) számos Speech Service-képességet tesz elérhetővé, így lehetővé teszi a beszédfelismerésre képes alkalmazások fejlesztését. A Speech SDK számos programozási nyelven és minden platformon elérhető.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Forgatókönyv-képességek

A Speech SDK számos funkciót tesz elérhetővé a beszédfelismerési szolgáltatásból, de nem mindegyiket. A Speech SDK képességeit gyakran társítják forgatókönyvekhez. A Speech SDK ideális a valós idejű és nem valós idejű forgatókönyvekhez, helyi eszközök, fájlok, Azure Blob Storage és akár bemeneti és kimeneti adatfolyamok használatával. Ha egy forgatókönyv nem érhető el a Speech SDK-val, keressen egy REST API alternatívát.

### <a name="speech-to-text"></a>Diktálás

A [beszéd – szöveg](speech-to-text.md) (más néven *beszédfelismerés*) a hangstreameket olyan szöveggé írja át, amelyet az alkalmazások, eszközök vagy eszközök képesek felhasználni vagy megjeleníteni. A beszéd-szöveg és a [Language Understanding (Luis)](../luis/index.yml) használatával származtatott beszédből származó felhasználói leképezéseket származtathat, és hangparancsokat alkalmazhat. A [beszédfelismerés](speech-translation.md) használatával más nyelvre is lefordíthatja a hangbevitelt egyetlen hívással. További információ: [beszédfelismerés és szöveg közötti alapismeretek](speech-to-text-basics.md).

### <a name="text-to-speech"></a>Szövegfelolvasás

A [szöveg és a beszéd](text-to-speech.md) (más néven *beszéd szintézis*) szöveggé alakítja át a szöveget az emberi, például a szintetizált beszédbe. A bemeneti szöveg karakterlánc-literál vagy a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használata. A standard vagy neurális hangokkal kapcsolatos további információkért lásd: [szöveg – beszéd nyelv és hangalapú támogatás](language-support.md#text-to-speech).

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A Speech SDK-t használó [hangsegédek](voice-assistants.md) lehetővé teszik, hogy a fejlesztők természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban. A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy segéd között. A megvalósítás a bot Framework közvetlen vonalas beszédfelismerési csatornáját vagy az integrált egyéni parancsok (előzetes verzió) szolgáltatást használja a feladatok befejezéséhez. Emellett a hangsegédek az [Egyéni Hangportálon](https://aka.ms/customvoice) létrehozott egyéni hangokat is felhasználhatják egy egyedi hangkimeneti élmény hozzáadásához.

#### <a name="keyword-spotting"></a>Kulcsszavas bepecsételés

[A beszédfelismerés](speech-devices-sdk-create-kws.md) fogalma támogatott a Speech SDK-ban. A kulcsszó-bepecsételés a kulcsszónak a beszédben való azonosítására szolgáló művelet, amelyet a kulcsszó meghallgatása után kell végezni. A "Hey Cortana" például aktiválja a Cortana Segédet.

### <a name="meeting-scenarios"></a>Értekezleti forgatókönyvek

A Speech SDK kiválóan alkalmas az értekezlet-forgatókönyvek átírására, akár egyetlen eszközről, akár több eszközről is beszélget.

#### <a name="conversation-transcription"></a>Beszédátírás

A [beszélgetés átirata](conversation-transcription.md) lehetővé teszi a valós idejű (és aszinkron) beszédfelismerést, a hangszórók azonosítását és a mondatok kiosztását az egyes hangszórókhoz (más néven *diarization*). A személyes értekezletek átírása tökéletes választás a hangszórók megkülönböztetésére.

#### <a name="multi-device-conversation"></a>Több eszközre kiterjedő beszélgetés

A [többeszközes beszélgetéssel](multi-device-conversation.md)több eszköz vagy ügyfél összekapcsolásával beszéd-vagy szöveges üzeneteket küldhet az átíráshoz és a fordításhoz egyszerű támogatással.

### <a name="custom--agent-scenarios"></a>Egyéni/ügynöki forgatókönyvek

A Speech SDK használható a Call Center-forgatókönyvek továbbítására, ahol a telefonos adatok jönnek létre.

#### <a name="call-center-transcription"></a>Telefonos ügyfélszolgálati felvételek átírása

A [Call Center átírása](call-center-transcription.md) gyakori forgatókönyv a beszédek közötti kommunikációhoz, amely különböző rendszerekről származó, nagy mennyiségű telefonos adatok (például interaktív hangválasztó (IVR)) beolvasására használható. A Speech Service Excel legújabb beszédfelismerési modelljei a telefonos adatok átírásakor, még abban az esetben is, ha az adatok nehezen érthetők.

### <a name="codec-compressed-audio-input"></a>Codec tömörített hangbemenet

A Speech SDK programozási nyelvei közül több is támogatja a kodekkel tömörített hangbemeneti streameket. További információ: <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">tömörített hangformátumok <span class="docon docon-navigate-external x-hidden-focus"></span> használata </a>.

## <a name="rest-api"></a>REST API

Míg a Speech SDK számos funkciót tartalmaz a Speech Service szolgáltatásban, bizonyos esetekben érdemes lehet használni a REST API.

### <a name="batch-transcription"></a>Kötegelt átírás

A [Batch-átírás](batch-transcription.md) lehetővé teszi a nagy mennyiségű adatforgalom aszinkron beszéd-szöveggé átírását. A Batch átírása csak a REST API lehetséges. A beszédfelismerési hang szövegre való konvertálása mellett a Batch beszéd-szöveg is lehetővé teszi a diarization és a hangulat-elemzést.

## <a name="customization"></a>Testreszabás

A beszédfelismerési szolgáltatás kiváló funkcionalitást biztosít az alapértelmezett modelljeivel a beszéd-szöveg, a szöveg és a beszéd között, valamint a beszédfelismerési fordításban. Esetenként érdemes lehet az alapkonfigurációt úgy megnövelni, hogy az egyedi használati esettel még jobban működjön. A beszédfelismerési szolgáltatás számos olyan, kód nélküli testreszabási eszközt tartalmaz, amely megkönnyíti a használatát, és lehetővé teszi, hogy saját adatai alapján versenyképes előnyt hozzon létre egyéni modellekkel. Ezek a modellek csak Ön és a szervezet számára lesznek elérhetők.

### <a name="custom-speech-to-text"></a>Custom Speech – szöveg

Ha a beszédfelismerés és az átírás egy egyedi környezetben, egyéni akusztikai, nyelvi és kiejtési modelleket hoz létre és tanít a környezeti zajok vagy az iparági specifikus szókincs kezelésére. A kód nélküli Custom Speech modellek létrehozása és kezelése a [Custom Speech portálon](https://aka.ms/customspeech)keresztül érhető el. Miután közzétette a Custom Speech modellt, felhasználhatja azt a Speech SDK is.

### <a name="custom-text-to-speech"></a>Egyéni szöveg – beszéd

Az egyéni hangvezérelt szöveg-beszéd, más néven az egyéni hang olyan online eszközök összessége, amelyek lehetővé teszik a márka számára felismerhető, egyfajta hang létrehozását. A kód nélküli egyéni hangmodellek létrehozása és kezelése az [Egyéni Hangportálon](https://aka.ms/customvoice)keresztül érhető el. Miután közzétette az egyéni hangmodellt, azt a Speech SDK is felhasználhatja.

## <a name="get-the-speech-sdk"></a>A Speech SDK beszerzése

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Böngésző](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>További lépések

* [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
* [Lásd: beszéd felismerése a C-ben #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
