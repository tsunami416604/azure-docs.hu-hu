---
title: Mi az a beszédszolgáltatás?
titleSuffix: Azure Cognitive Services
description: A beszédszolgáltatás a beszéd-szöveg, a szövegfelolvasás és a beszédfordítás egyetlen Azure-előfizetésbe történő egyesítése. Beszédfelismerési műveleteket adhat alkalmazásaihoz, eszközeihez és eszközeihez a beszédfelismerési SDK,Beszédeszközök SDK-val vagy REST API-kkal.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 353e66c2d4ebb288244866b12a32e9acb7c765e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371567"
---
# <a name="what-is-the-speech-service"></a>Mi az a beszédszolgáltatás?

A beszédszolgáltatás a beszéd-szöveg, a szövegfelolvasás és a beszédfordítás egyetlen Azure-előfizetésbe történő egyesítése. A beszédfelismerési műveletek et egyszerűen lehetővé teszik az alkalmazások, eszközök és eszközök segítségével a [Beszéd sdk,](speech-sdk-reference.md) [a beszédfelismerési eszközök SDK](https://aka.ms/sdsdk-quickstart)vagy [a REST API-k segítségével.](rest-apis.md)

> [!IMPORTANT]
> A beszédszolgáltatás felváltotta a Bing Speech API-t és a Translator Speech.The Speech service has replaced Bing Speech API and Translator Speech. Az _áttelepítési utasításokhoz olvassa_ el az áttelepítési útmutatók > című témakört.

Ezek a funkciók alkotják a beszédfelismerési szolgáltatást. A táblázatban található hivatkozások segítségével többet megtudhat az egyes funkciók gyakori használati eseteiről, vagy tallózhat az API-hivatkozásban.

| Szolgáltatás | Szolgáltatás | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Beszéd-szöveg](speech-to-text.md) | Valós idejű beszédfelismerés-szöveg | A beszéd-szöveg adatírás vagy a hangadatfolyamok vagy a helyi fájlok valós idejű szöveggé fordítása, amelyet az alkalmazások, eszközök vagy eszközök használhatnak vagy jeleníthetnek meg. A [language understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) szöveggé bírja a felhasználó szándékait az átírásból, és a hangparancsok alapján jár el. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Kötegelt szöveggé bírja](batch-transcription.md) | A batch speech-to-text lehetővé teszi az Azure Blob Storage-ban tárolt nagy mennyiségű beszédhang-adat aszinkron beszéd-szöveg reszkripteltióját. A beszédhang szöveggé alakítása mellett a kötegelt beszédfelismerés szöveggé is lehetővé teszi a diarizációt és a hangulatelemzést. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| | [Többeszközes beszélgetés](multi-device-conversation.md) | Több eszköz vagy ügyfél csatlakoztatása egy beszélgetésben beszéd- vagy szöveges üzenetek küldéséhez, az átírás és a fordítás egyszerű támogatásával| Igen | Nem |
| | [Beszélgetés átírása](conversation-transcription-service.md) | Valós idejű beszédfelismerést, hangszóró-azonosítást és diarizációt tesz lehetővé. Tökéletes a személyes találkozók átírásához, és képes megkülönböztetni a hangszórókat. | Igen | Nem |
| | [Egyéni beszédfelismerési modellek létrehozása](#customize-your-speech-experience) | Ha a beszéd-szöveg felismerést és átírást használja egy egyedi környezetben, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zaj vagy az iparág-specifikus szókincs kezelésére. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| [Szövegfelolvasás](text-to-speech.md) | Szövegfelolvasás | A szövegfelolvasás a szöveget emberszerű szintetizált beszédmé alakítja a [Beszédszintetizáló jelölőnyelv (SSML)](speech-synthesis-markup.md)használatával. Válasszon a szabványos és neurális hangok közül [(lásd: Nyelvi támogatás](language-support.md)). | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Egyéni hangok létrehozása](#customize-your-speech-experience) | Hozzon létre egyedi hangbetűtípusokat, amelyek egyediek a márkáján vagy a termékén. | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Beszédfordítás](speech-translation.md) | Beszédfordítás | A beszédfordítás lehetővé teszi a beszéd valós idejű, többnyelvű fordítását alkalmazásaira, eszközeire és eszközeire. Ezt a szolgáltatást beszédfelolvasásra és beszéd-szövegre fordításra használhatja. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nem |
| [Hangvezérelt asszisztensek](voice-assistants.md) | Hangvezérelt asszisztensek | A Beszédszolgáltatást használó hangasszisztensek lehetővé teszik a fejlesztők számára, hogy természetes, emberszerű társalgási felületeket hozzanak létre alkalmazásaikhoz és felhasználói élményeikhez. A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy asszisztens megvalósítása között, amely a Bot Framework közvetlen vonalbeli beszédcsatornáját vagy az integrált egyéni parancsok (előzetes verzió) szolgáltatást használja a feladat befejezéséhez. | [Igen](voice-assistants.md) | Nem |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>Próbálja ki a Beszédszolgáltatást

Rövid útmutatókat kínálunk a legnépszerűbb programozási nyelveken, amelyek mindegyike úgy lett tervezve, hogy kevesebb mint 10 perc alatt futtatja a kódot. Ez a táblázat az egyes szolgáltatások legnépszerűbb rövid útmutatóit tartalmazza. A bal oldali navigációval további nyelveket és platformokat fedezhet fel.

| Beszéd-szöveg (SDK) | Szövegfelolvasás (SDK) | Fordítás (SDK) |
|----------------------|----------------------|-------------------|
| [Beszéd felismerése hangfájlból](quickstarts/speech-to-text-from-file.md) | [Beszédszintézis egy hangfájlba](quickstarts/text-to-speech-audio-file.md) | [Beszéd fordítása szöveggé](quickstarts/translate-speech-to-text.md) |
| [Beszéd felismerése mikrofonnal](quickstarts/speech-to-text-from-microphone.md) | [Beszédszintézis hangszóróra](quickstarts/text-to-speech.md) | [Beszéd fordítása több célnyelvre](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Blobtárolóban tárolt beszéd felismerése](quickstarts/from-blob.md) | [Aszinkron szintézis a hosszú formátumú hangokhoz](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Beszéd fordítása beszédre](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> A beszédfelismerés és a szövegfelolvasás rest-végpontokkal és a kapcsolódó rövid útmutatókkal is rendelkezik.

Miután lehetősége volt használni a beszédfelismerési szolgáltatást, próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédből származó szándékokat a beszédbeszédskés a LUIS használatával.

- [Oktatóanyag: A beszédbeszédstkés a LUIS, C beszédből származó szándékok felismerése #](how-to-recognize-intents-from-speech-csharp.md)
- [Oktatóanyag: A hang funkció valamezenis engedélyezi a robotot a Beszéd SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Oktatóanyag: Flask alkalmazás létrehozása a szöveg lefordításához, a hangulat elemzéséhez és a lefordított szöveg beszédre, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Mintakód letöltése

Mintakód érhető el a GitHubon a beszédszolgáltatás. Ezek a minták olyan gyakori forgatókönyveket fednek le, mint a hang fájlból vagy adatfolyamból történő olvasása, a folyamatos és az egyesélyes felismerés, valamint az egyéni modellek kezelése. Az alábbi hivatkozások segítségével megtekintheti az SDK- és REST-mintákat:

- [Szövegfelolvasás, szövegfelolvasás és beszédfordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Tételtranszkripciós minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Szövegfelolvasó minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Hangasszisztens-minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>A beszédélmény testreszabása

A beszédfelismerési szolgáltatás jól működik a beépített modellekkel, azonban érdemes tovább szabni és finomítani a termék vagy környezet élményét. A testreszabási lehetőségek az akusztikai modellhangolástól a márkához kapcsolódó egyedi hangbetűtípusokig terjednek.

| Beszédszolgáltatás | Platform | Leírás |
| -------------- | -------- | ----------- |
| Diktálás | [Egyéni beszédfelismerés](https://aka.ms/customspeech) | Igény szerint és a rendelkezésre álló adatokkal testreszabhatja a beszédfelismerési modelleket. A beszédfelismerés imázsának, például a beszédstílusnak, a szókincsnek és a háttérzajnak a leküzdése. |
| Szövegfelolvasás | [Egyéni hang](https://aka.ms/customvoice) | Létrehozhat egy felismerhető és egyedülálló hangot szövegfelolvasási alkalmazásaihoz az elérhető beszédadatokkal. A hangkimeneteket további finomhangolhatja a hangparaméterek beállításával. |

## <a name="reference-docs"></a>Referenciadokumentumok

- [Beszéd SDK](speech-sdk-reference.md)
- [Beszédeszközök SDK](speech-devices-sdk.md)
- [REST API: Beszéd-szöveg](rest-speech-to-text.md)
- [REST API: Szövegfelolvasás](rest-text-to-speech.md)
- [REST API: Kötegátírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
