---
title: Mi az a beszédszolgáltatás?
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg-beszéd és a beszéd fordításának egységesítése egyetlen Azure-előfizetésbe. Beszédet adhat hozzá alkalmazásaihoz, eszközeihez és eszközeihez a Speech SDK, a Speech Devices SDK vagy a REST API-k használatával.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: 664b71c56ae63ea73478923171731e343dfa5cbe
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417225"
---
# <a name="what-is-the-speech-service"></a>Mi az a beszédszolgáltatás?

A beszédfelismerési szolgáltatás a beszéd-szöveg, a szöveg és a beszéd egyesítése, valamint a beszédfelismerés egyetlen Azure-előfizetésbe való fordítása. A Speech [SDK](speech-sdk-reference.md), a [SPEECH Devices SDK](https://aka.ms/sdsdk-quickstart)vagy a [REST API](rest-apis.md)-k segítségével egyszerűen elvégezheti az alkalmazások, eszközök és eszközök használatát.

> [!IMPORTANT]
> A Speech szolgáltatás leváltotta Bing Speech API és Translator Speech. Tekintse át az áttelepítési utasítások _> áttelepítési_ útmutatókat.

Ezek a funkciók alkotják a beszédfelismerési szolgáltatást. Az ebben a táblázatban található hivatkozásokkal többet tudhat meg az egyes funkciók általános használati eseteiről, vagy böngészhet az API-referenciában.

| Szolgáltatás | Funkció | Leírás | SDK | REST |
|---------|---------|-------------|-----|------|
| [Beszéd – szöveg](speech-to-text.md) | Valós idejű beszéd – szöveg | A beszéd-szöveg szöveggé alakítja át vagy lefordítja a hangadatfolyamokat vagy helyi fájlokat valós időben, hogy az alkalmazások, eszközök vagy eszközök képesek legyenek a felhasználásra vagy a megjelenítésre. A beszéd-szöveg és a [Language Understanding (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/) használatával származtatott beszédből származó felhasználói leképezéseket származtathat, és hangparancsokat alkalmazhat. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch – beszéd – szöveg](batch-transcription.md) | A Batch-beszédfelismerés lehetővé teszi, hogy az Azure Blob Storageban tárolt nagy mennyiségű hangalapú beszéd-szöveg átírása aszinkron módon történjen. A beszédfelismerési hang szövegre való konvertálása mellett a Batch beszéd-szöveg is lehetővé teszi a diarization és a hangulat-elemzést. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| | [Több eszközre kiterjedő beszélgetés](multi-device-conversation.md) | Több eszköz vagy ügyfél csatlakoztatása egy beszélgetésben beszéd-vagy szöveges üzenetek küldéséhez egyszerű támogatással a transzkripció és a fordítás számára| Igen | Nem |
| | [Beszélgetés átirata](conversation-transcription-service.md) | Lehetővé teszi a valós idejű beszédfelismerést, a hangszórók azonosítását és a diarization. A személyes értekezletek átírása tökéletes választás a hangszórók megkülönböztetésére. | Igen | Nem |
| | [Custom Speech modellek létrehozása](#customize-your-speech-experience) | Ha egy egyedi környezetben beszéd-szövegeket használ az elismeréshez és az átíráshoz, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zajok vagy az iparági specifikus szókincsek kezeléséhez. | Nem | [Igen](https://westus.cris.ai/swagger/ui/index) |
| [Szöveg – beszéd](text-to-speech.md) | Szövegfelolvasás | A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi környezetbe, például a [Speech szintézis Markup Language (SSML)](speech-synthesis-markup.md)használatával. A standard hangok és a neurális hangok közül választhat (lásd a [nyelvi támogatást](language-support.md)). | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Egyéni hangok létrehozása](#customize-your-speech-experience) | Egyedi hangbetűkészletek létrehozása a márka vagy a termék számára. | Nem | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Beszéd fordítása](speech-translation.md) | Beszédfordítás | A beszédfelismerés valós idejű, többnyelvű fordítást tesz lehetővé alkalmazásaihoz, eszközeihez és eszközeihez. Használja ezt a szolgáltatást beszéd – beszéd és beszéd – szöveg fordításához. | [Igen](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Nem |
| [Hangvezérelt asszisztensek](voice-assistants.md) | Hangvezérelt asszisztensek | A beszédfelismerési szolgáltatást használó hangsegédek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban. A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy asszisztens implementáció között, amely a bot Framework közvetlen vonalas vagy integrált egyéni parancsait (előzetes verzió) használja a feladatok befejezéséhez. | [Igen](voice-assistants.md) | Nem |
| [Beszélőfelismerés](speaker-recognition-overview.md) | A hangszórók ellenőrzése & azonosítása | A Speaker Recognition szolgáltatás olyan algoritmusokat biztosít, amelyek egyedi hangtulajdonságokkal rendelkeznek a hangszórók ellenőrzéséhez és azonosításához. Speaker Recognition a "ki beszél?" kérdésre válaszol. | Igen | [Igen](https://docs.microsoft.com/rest/api/speakerrecognition/) |


[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="try-the-speech-service"></a>A beszédfelismerési szolgáltatás kipróbálása

A legnépszerűbb programozási nyelveken gyors útmutatókat ajánlunk, amelyek mindegyike kevesebb, mint 10 perc alatt futtathatja a kódot. Ez a táblázat az egyes szolgáltatásokhoz tartozó legnépszerűbb gyors útmutatókat tartalmazza. A bal oldali navigációs sávon további nyelveket és platformokat is felhasználhat.

| Beszéd – szöveg (SDK) | Szöveg – beszéd (SDK) | Fordítás (SDK) |
|----------------------|----------------------|-------------------|
| [Beszéd felismerése hangfájlból](quickstarts/speech-to-text-from-file.md) | [Beszédszintézis egy hangfájlba](quickstarts/text-to-speech-audio-file.md) | [Beszéd fordítása szöveggé](quickstarts/translate-speech-to-text.md) |
| [Beszéd felismerése mikrofonnal](quickstarts/speech-to-text-from-microphone.md) | [Beszédszintézis hangszóróra](quickstarts/text-to-speech.md) | [Beszéd fordítása több célnyelvre](quickstarts/translate-speech-to-text-multiple-languages.md) |
| [Blobtárolóban tárolt beszéd felismerése](quickstarts/from-blob.md) | [Aszinkron szintézis a hosszú formátumú hangokhoz](quickstarts/text-to-speech/async-synthesis-long-form-audio.md) | [Beszéd fordítása beszédre](quickstarts/translate-speech-to-speech.md) |

> [!NOTE]
> A beszédfelismerés és a szöveg beszéde is a REST-végpontokkal és a hozzájuk kapcsolódó gyors útmutatókkal is rendelkezik.

A beszédfelismerési szolgáltatás használatának lehetősége után próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédfelismerési szándékokat a Speech SDK és a LUIS használatával.

- [Oktatóanyag: beszédfelismerési szándékok felismerése a Speech SDK és a LUIS, C használatával #](how-to-recognize-intents-from-speech-csharp.md)
- [Oktatóanyag: hang engedélyezése a robotnak a Speech SDK-val, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
- [Oktatóanyag: a szöveg fordítását, a hangulat elemzését és a fordítás szövegét beszédre, REST-re alakító lombik-alkalmazás létrehozása](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Mintakód letöltése

Mintakód elérhető a GitHubon a Speech Service-hez. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

- [Beszéd – szöveg, szöveg-beszéd és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
- [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>A beszédfelismerési élmény testreszabása

A beszédfelismerési szolgáltatás a beépített modellekkel jól működik, azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között.

| Beszédszolgáltatás | Platform | Leírás |
| -------------- | -------- | ----------- |
| Diktálás | [Custom Speech](https://aka.ms/customspeech) | Testreszabhatja a beszédfelismerési modelleket igényeihez és rendelkezésre álló adataihoz. Leküzdheti a beszédfelismerési akadályokat, például a beszéd stílusát, a szókincset és a háttérzajt. |
| Szövegfelolvasás | [Egyéni hang](https://aka.ms/customvoice) | Létrehozhat egy felismerhető és egyedülálló hangot szövegfelolvasási alkalmazásaihoz az elérhető beszédadatokkal. A hangkimeneteket tovább finomíthatja a hangparaméterek készletének módosításával. |

## <a name="reference-docs"></a>Dokumentációs dokumentumok

- [Beszéd SDK](speech-sdk-reference.md)
- [Beszédeszközök SDK](speech-devices-sdk.md)
- [REST API: beszéd – szöveg](rest-speech-to-text.md)
- [REST API: szövegről beszédre](rest-text-to-speech.md)
- [REST API: kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
