---
title: A Beszéd SDK - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A Beszédszoftver-fejlesztő imtárkészlet (SDK) számos beszédfelismerési szolgáltatás-szolgáltatást kínál, így könnyebben fejleszthető beszédalapú alkalmazások.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a2ff4a94c1b2941f645cd7032ef476d33dffdb00
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656505"
---
# <a name="about-the-speech-sdk"></a>A Speech SDK ismertetése

A Beszédszoftver-fejlesztő készlet (SDK) számos beszédfelismerési szolgáltatás-szolgáltatást tesz elérhetővé, így beszédfelismerést támogató alkalmazásokat fejleszthet. A beszédstak kedélyek számos programozási nyelven és minden platformon elérhetők.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Forgatókönyv-képességek

A beszédstabk számos funkciót tesz elérhetővé a beszédfelismerési szolgáltatásból, de nem mindegyiket. A beszédbeszéd SDK képességei gyakran társított forgatókönyvek. A beszédbeszéd SDK ideális mind a valós idejű és nem valós idejű forgatókönyvek, a helyi eszközök, fájlok, Azure blob storage, és még a bemeneti és kimeneti adatfolyamok. Ha egy forgatókönyv nem érhető el a speech SDK, keressen egy REST API-alternatívát.

### <a name="speech-to-text"></a>Diktálás

[A beszédfelismerés](speech-to-text.md) (más néven *beszédfelismerés)* átírja a hangadatfolyamokat olyan szöveggé, amelyet az alkalmazások, eszközök vagy eszközök használhatnak vagy jeleníthetnek meg. A [language understanding (LUIS)](../luis/index.yml) szöveggé bírja a felhasználó szándékait az átírásból, és a hangparancsok alapján jár el. A [Beszédfordítás segítségével](speech-translation.md) egyetlen hívással lefordíthatja a beszédbevitelt egy másik nyelvre. További információt a [Szövegfelolvasás alapjai című témakörben talál.](speech-to-text-basics.md)

### <a name="text-to-speech"></a>Szövegfelolvasás

[A szövegfelolvasás](text-to-speech.md) (más néven *beszédszintézis)* a szöveget emberszerű szintetizált beszédmé alakítja. A beviteli szöveg vagy karakterlánc-konstansok, vagy a [beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)nyelvet használja. A szabványos vagy neurális hangokkal kapcsolatos további információkért lásd: [Szövegfelolvasás nyelv és hangtámogatás.](language-support.md#text-to-speech)

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A beszédfelismerési SDK-t használó hangasszisztensek lehetővé teszik a fejlesztők számára, hogy természetes, emberszerű társalgási felületeket hozzanak létre alkalmazásaikhoz és felhasználói élményeikhez. A hangasszisztens-szolgáltatás gyors és megbízható interakciót biztosít az eszköz és az asszisztens között. A megvalósítás a Bot Framework közvetlen vonali beszédcsatornáját vagy az integrált egyéni parancsok (előzetes verzió) szolgáltatást használja a feladat befejezéséhez. Ezenkívül a hangasszisztensek az [egyéni hangportálhasználatával](https://aka.ms/customvoice) is létrehozható, így egyedi hangélményt hozhatnak létre.

#### <a name="keyword-spotting"></a>Kulcsszóészlelés

A [kulcsszófelismerés](speech-devices-sdk-create-kws.md) fogalmát a beszédfelismerési SDK támogatja. A kulcsszófelismerés a kulcsszó felismerése a beszédben, amelyet a kulcsszó meghallgatása után végrehajtanak. A "Hey Cortana" például aktiválná a Cortana-asszisztenst.

### <a name="meeting-scenarios"></a>Értekezleti forgatókönyvek

A beszédstabk tökéletes az értekezlet-forgatókönyvek átírásához, akár egyetlen eszközről, akár többeszközes beszélgetésről.

#### <a name="conversation-transcription"></a>Beszédátírás

[A Beszélgetésátírás](conversation-transcription.md) valós idejű (és aszinkron) beszédfelismerést, hangszóró-azonosítást és mondathozzárendelést tesz lehetővé az egyes előadóknak (más néven *diarizáció).* Tökéletes a személyes találkozók átírásához, és képes megkülönböztetni a hangszórókat.

#### <a name="multi-device-conversation"></a>Többeszközes beszélgetés

A [Többeszközes beszélgetés segítségével](multi-device-conversation.md)több eszközt vagy ügyfelet is csatlakoztathat egy beszélgetésben beszédalapú vagy szöveges üzenetek küldéséhez, és az átírás és a fordítás egyszerű támogatásával.

### <a name="custom--agent-scenarios"></a>Egyéni / ügynöki forgatókönyvek

A beszédsdk használható hívásközpont-forgatókönyvek átírására, ahol telefonos adatok jönnek létre.

#### <a name="call-center-transcription"></a>Telefonos ügyfélszolgálati felvételek átírása

[A hívásközpont-átírás](call-center-transcription.md) gyakori forgatókönyv a beszéd-szöveg íráshoz a különböző rendszerekből, például az interaktív hangválaszból (IVR) származó nagy mennyiségű telefonadat átírásához. A Beszédszolgáltatás legújabb beszédfelismerési modelljei még akkor is kitűnnek a telefonos adatok átírásában, ha az adatokat egy ember nehezen érti meg.

### <a name="codec-compressed-audio-input"></a>Kodek tömörített hangbemenet

A Speech SDK programozási nyelvek közül több is támogatja a kodek tömörített hangbemeneti adatfolyamait. További információt a <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">Tömörített hangbeviteli <span class="docon docon-navigate-external x-hidden-focus"> </span>formátumok használata című témakörben talál. </a>

## <a name="rest-api"></a>REST API

Míg a beszédbeszéd SDK a beszédszolgáltatás számos szolgáltatásfunkcióját lefedi, bizonyos esetekben érdemes lehet a REST API-t használni. Például a végpontkezelés csak a REST API-n keresztül érhető el.

> [!TIP]
> Ha a REST API-ra támaszkodik, a Swagger-szerkesztő segítségével automatikusan létrehozhatja az ügyfélkódtárakat. Például egy Batch transzkripciós ügyfélkönyvtár létrehozásához.
> 1. Másolja az alábbi példa URL-címet:
>     ```http
>     https://westus.cris.ai/docs/v2.0/swagger
>     ```
> 1. Keresse meg a <a href="https://editor.swagger.io/" target="_blank">Swagger-szerkesztőt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>
> 1. Jelölje ki **a Fájlimportálás** > **URL-címét,** és illessze be az URL-címet
> 1. Válassza **az Ügyfél létrehozása lehetőséget,** és válassza ki a kívánt programozási nyelvet

### <a name="batch-transcription"></a>Kötegelt átírás

[A kötegelt átírás](batch-transcription.md) nagy mennyiségű adat aszinkron beszéd-szöveg átírását teszi lehetővé. A kötegtranszkripció csak a REST API-ból lehetséges.

## <a name="customization"></a>Testreszabás

A beszédszolgáltatás kiváló funkcionalitást biztosít az alapértelmezett modellekkel a beszéd-szöveg, a szövegfelolvasás és a beszédfordítás között. Néha érdemes növelni az alapteljesítményt, hogy még jobban működjön az egyedi használati esetével. A Beszédszolgáltatás számos kód nélküli testreszabási eszközzel rendelkezik, amelyek megkönnyítik a szolgáltatást, és lehetővé teszik, hogy saját adatai alapján egyéni modellekkel versenyelőnyt hozzon létre. Ezek a modellek csak az Ön és szervezete számára lesznek elérhetők.

### <a name="custom-speech-to-text"></a>Egyéni beszédfelismerési szöveg

Ha a beszéd-szöveg felismerést és átírást használja egy egyedi környezetben, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zaj vagy az iparág-specifikus szókincs kezelésére. A kód nélküli egyéni beszédfelismerési modellek létrehozása és kezelése az [egyéni beszédportálon](https://aka.ms/customspeech)keresztül érhető el. Az egyéni beszédfelismerési modell közzététele után a beszédbeszéd SDK-val használható fel.

### <a name="custom-text-to-speech"></a>Egyéni szövegfelolvasás

Az egyéni szövegfelolvasó, más néven egyéni hang olyan online eszközök készlete, amelyek lehetővé teszik, hogy felismerhető, egyedi hangot hozzon létre a márkája számára. A kód nélküli Egyéni hangmodellek létrehozása és kezelése az [egyéni hangportálon](https://aka.ms/customvoice)keresztül érhető el. Az egyéni hangmodell közzététele után a beszédbeszéd SDK-val használható fel.

## <a name="get-the-speech-sdk"></a>A beszédfelismerési SDK beolvasása

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[Macos](#tab/macos)

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

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszéd felismerése C-ben #](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
