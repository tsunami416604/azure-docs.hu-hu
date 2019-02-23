---
title: Ismerkedés a Bing Speech Recognition API Objective-C nyelven az iOS rendszeren |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech Recognition API használatával hozhat létre iOS-alkalmazásokat, amelyek a beszélt hangot képes szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 09b7e8961e59bd6fad49408c28e9ee9a4a209cae
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669431"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Gyors útmutató: A Bing Speech Recognition API használata az Objective-C iOS rendszeren

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Speech Recognition API beszédfelismerő felhő alapú szolgáltatás használja a beszélt hangot képes szöveggé alakítani iOS-alkalmazások fejleszthet. Az API-t támogatja, hogy valós idejű streamelési, így az alkalmazás egy időben, és aszinkron módon képes is részleges felismerési eredményeket hang, a szolgáltatás által küldött egy időben.

Ebben a cikkben egy mintaalkalmazás bemutatják, hogyan kezdheti el az iOS alkalmazások fejlesztése a Speech Recognition API-val alapjait. Egy teljes körű API-referencia, lásd: a [beszéd SDK ügyféloldali kódtár – referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Győződjön meg arról, hogy telepítve van-e a Mac XCode IDE.

### <a name="get-the-client-library-and-examples"></a>Az ügyfél első erőforrástár és a példákat

A beszédfelismerő ügyfélkódtárának és az iOS-es példák érhetők el a [Speech ügyfél-SDK IOS rendszerhez készült](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a Speech Recognition API és a egy ingyenes próba-előfizetését kulcs lekérése

A beszédfelismerő API a Cognitive Services-(korábban Project Oxford) részét képezi. Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

Ha a használni kívánt *leképezés a felismerés*, is regisztrálni kell a [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Előfizetési kulcs lekérése. Ügyfélkódtárak beszéd használata előtt rendelkeznie kell egy [előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
> * Az előfizetési kulcs használja. A megadott iOS-mintaalkalmazást a fájl Samples/SpeechRecognitionServerExample/settings.plist frissíteni az előfizetési kulccsal végzett kell. További információkért lásd: [hozza létre és futtassa a mintát](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>A beszédfelismerő ügyfélkódtárának használata

Az XCode-projektben adja hozzá az ügyféloldali kódtár, kövesse az alábbi [utasításokat](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Az ügyfél kódtár – referencia iOS-hez, lásd: Ez [weblap](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Hozhat létre és -minták futtatása

Információk a build és a futtatási minták: Ez [tájékoztató oldalon](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>A minták ismertetése

### <a name="create-recognition-clients"></a>Elismerés ügyfelek létrehozása

A következő kód a minta bemutatja, hogyan hozhat létre a felismerés ügyfélosztályok felhasználói esetek alapján:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

Az ügyféloldali kódtár biztosít előre megvalósított felismerés ügyfélosztályok Beszédfelismerés a tipikus forgatókönyvek:

* `DataRecognitionClient`: A beszédfelismerés PCM adatokkal (például a fájl- vagy audióanyagot forrás). Az adatok pufferek van osztva, és minden egyes puffer Speech szolgáltatásnak továbbítja. Bármilyen módosítás nélkül történik a pufferek, így alkalmazhatnak a saját csend észlelése, ha szükséges. Az adatok WAV-fájlokból áll rendelkezésre, ha a fájlt közvetlenül a kiszolgálóhoz küldhet adatokat. Ha olyan nyers adat, például hang, a Bluetooth keresztül érkező, először egy formátum fejléc elküldése a kiszolgálóra, majd az adatokat.
* `MicrophoneRecognitionClient`: A beszédfelismerés együtt származik a mikrofon hang. Ellenőrizze, hogy a mikrofon be van kapcsolva, és hogy az adatok a mikrofon a speech recognition szolgáltatásnak továbbítja. A beépített "csend detector használatával" mikrofon adatokra alkalmazza, a felismerés szolgáltatás továbbítás előtt.
* `DataRecognitionClientWithIntent` és `MicrophoneRecognitionClientWithIntent`: Elismerési szöveg mellett az ilyen ügyfelek a beszélő, amely az alkalmazások használható további műveletek alapjául a leképezés strukturált információt adja vissza. "Leképezés" használatához először betanítja a modellt használatával kell [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Nyelv felismerése

Ha használ `SpeechRecognitionServiceFactory` szeretne létrehozni az ügyfelet, ki kell választania egy nyelvet. Beszédfelismerési szolgáltatás által támogatott nyelvek teljes listáját lásd: [támogatott nyelvek](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Emellett meg kell adnia `SpeechRecognitionMode` létrehozásakor, az ügyfél és `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Az utterance (kifejezés), legfeljebb 15 másodperc hosszúságú. Mivel a szolgáltatás adatokat küld, az ügyfél megkapta a több részleges és több végső eredményt, több legjobb n választási lehetőség a.
* `SpeechRecognitionMode_LongDictation`: Az utterance (kifejezés), akár két perc hosszú. Mivel a szolgáltatás adatokat küld, a az ügyfél több részleges és több végső eredményt, ha a kiszolgáló azonosítja a mondaton belüli szünetet alapján kap.

### <a name="attach-event-handlers"></a>Eseménykezelők csatolása

Az ügyfél különböző eseménykezelők csatolhat létrehozott:

* **Részleges eredményeket események**: Ez az esemény meghívása megtörténik, minden alkalommal, amikor a Speech Service előrejelzi, előfordulhat, hogy el véleményét, beszéd befejezése előtt is (Ha `MicrophoneRecognitionClient`), vagy fejezze be az adatok küldésének (használatakor `DataRecognitionClient`).
* **Hibaesemények**: Meghívva, ha a szolgáltatás hibát észlel.
* **Leképezési események**: Neve az "WithIntent" ügyfeleken (csak a ShortPhrase üzemmódban) után a végső felismerése eredménye egy strukturált JSON-leképezés szűrőtulajdonság.
* **Események eredményeként**:
  * A `SpeechRecognitionMode_ShortPhrase` mód, ez az esemény nevezik, és n legjobb eredményeket ad vissza, és beszéljen befejezése után.
  * A `SpeechRecognitionMode_LongDictation` üzemmód, az eseménykezelő neve többször, ahol a szolgáltatás azonosítja a mondaton belüli szünetet alapján.
  * **A legjobb n lehetőségek mindegyike**, egy megbízhatósági érték és néhány másik formája a felismert szöveget adja vissza. További információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

## <a name="related-topics"></a>Kapcsolódó témakörök

* [IOS-hez készült ügyféloldali kódtár – referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Ismerkedés a Microsoft beszédfelismerési és/vagy a leképezést a Java Android rendszeren](GetStartedJavaAndroid.md)
* [A Microsoft Speech API a JavaScript használatának első lépései](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft Speech API REST-en keresztül](GetStartedREST.md)
