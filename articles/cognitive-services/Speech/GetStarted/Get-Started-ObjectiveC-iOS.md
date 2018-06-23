---
title: Ismerkedés a Microsoft beszéd-felismerési API Objective-C IOS |} Microsoft Docs
description: A Microsoft beszéd felismerés API használatával, amelyek a szóbeli hang szöveg iOS-alkalmazások fejlesztéséhez.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: bbb8d3975cdab537135b97ca9bbf6e845aa3fa0e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347443"
---
# <a name="get-started-with-the-speech-recognition-api-in-objective-c-on-ios"></a>A beszéd-felismerési API Objective-C IOS első lépések

A beszédfelismerés felismerés API-val fejleszthet szóbeli hang átalakítása szöveges felhőalapú beszéd szolgáltatást használó iOS-alkalmazások. Az API támogatja valós idejű streamelési, így az alkalmazás egyszerre is és részleges felismerési eredmények aszinkron módon fogadásához azt a szolgáltatás hang küld egy időben.

Ebben a cikkben mintaalkalmazás az első lépések egy iOS-alkalmazások fejlesztéséhez Beszéd felismerés API-val alapjait bemutatásához. Tekintse meg a teljes API-referencia a [beszéd SDK ügyféloldali kódtár – referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

Győződjön meg arról, hogy telepítve van-e a Mac XCode IDE.

### <a name="get-the-client-library-and-examples"></a>Az ügyfél első könyvtár és példák

A beszédfelismerés ügyféloldali kódtár és példák az IOS-es érhetők el a [iOS SDK-ügyfél beszéd](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>A beszédfelismerés felismerés API előfizetni, és egy ingyenes próba-előfizetés kulcs beszerzése

A Diktálásfelismerési API (korábban projekt Oxford) kognitív szolgáltatások részét képezi. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

Ha a használni kívánt *biztonsági mentés felismerés*, is be kell jelentkeznie a a [nyelvi ismertetése intelligens szolgáltatás (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Egy előfizetés kulcs beszerzése. Beszéd klienskódtárak használata előtt rendelkeznie kell egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
> * Az előfizetés-kulcsot használ. A megadott iOS mintaalkalmazás módosítania a fájl Samples/SpeechRecognitionServerExample/settings.plist az előfizetés kulccsal. További információkért lásd: [létrehozása és futtatása a minták](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>A beszédfelismerés ügyféloldali kódtár használata

Az ügyféloldali kódtár az XCode projekt való hozzáadásához kövesse az alábbi [utasításokat](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Az ügyfél kódtár – dokumentáció iOS-hez, lásd: a [weblap](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Hozza létre és minták futtatása

Build és futtatási minták kapcsolatos információkért lásd: a [tájékoztató oldalon](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Minták alapján

### <a name="create-recognition-clients"></a>Hozzon létre felismerés ügyfelek

A minta a következő kód bemutatja, hogyan felhasználói forgatókönyvek alapján felismerés ügyfél osztályokat hozhatnak létre:

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

Az ügyféloldali kódtár biztosít előre megvalósított felismerés ügyfél osztályok Beszédfelismerés a jellemző forgatókönyvek:

* `DataRecognitionClient`: A beszédfelismerés PCM adatait (például egy fájl vagy a hallható forrás). Az adatok van felosztva pufferek, majd minden puffer beszéd szolgáltatásnak továbbítja. Módosítás nélkül azért van szükség, a buffers, így alkalmazhatnak saját csend észlelése, ha szükséges. Ha az adatokat a WAV-fájlokból áll rendelkezésre, a kiszolgáló fájl jobbról küldhet adatokat. Ha nyers adatok, például hang Bluetooth, keresztül érkező, először formátum fejléc küldése az adatokat pedig a kiszolgálóra.
* `MicrophoneRecognitionClient`: A Beszédfelismerés a hang a mikrofon származik. Győződjön meg arról, hogy a mikrofon be van kapcsolva, és hogy az adatok a mikrofon a beszédfelismerés felismerés szolgáltatáshoz érkezik. A beépített "csend, beállítás" alkalmazzák a mikrofon adatokat a felismerési szolgáltatás továbbítás előtt.
* `DataRecognitionClientWithIntent` és `MicrophoneRecognitionClientWithIntent`: mellett felismerés szöveg, ezek az ügyfelek információval strukturált a célt a hangalapú, amely annak további műveletek az alkalmazások használhatják a. "Cél" használatához először használatával a modell betanításához szüksége [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Beszédfelismerési nyelv

Amikor `SpeechRecognitionServiceFactory` az ügyfél létrehozása, ki kell választania egy nyelvet. A beszédfelismerés szolgáltatás által támogatott nyelvek teljes listájáért lásd: [támogatott nyelv](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Is meg kell adnia `SpeechRecognitionMode` létrehozásakor az ügyfél és `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Legfeljebb 15 egy utterance hosszú másodperc. A szolgáltatás adatokat küld, mivel az ügyfél megkapja a több részleges eredményt és egy végeredményt több n-legjobb választást.
* `SpeechRecognitionMode_LongDictation`: Egy utterance akár két perc hosszú. A szolgáltatás adatokat küld, mivel az ügyfél megkapja a több részleges eredményt, és több végső eredmény, ha a kiszolgáló azonosítja mondat szünetel.

### <a name="attach-event-handlers"></a>Az eseménykezelők csatolása

Az ügyfél különböző eseménykezelők csatolhat létrehozott:

* **Részleges eredmények események**: Ez az esemény minden alkalommal, amikor a beszédfelismerés Service előrejelzi mi, előfordulhat, hogy kell arról, még mielőtt beszéd menüelemnek (használatakor `MicrophoneRecognitionClient`) vagy a Befejezés adatküldés (használatakor `DataRecognitionClient`).
* **Hibaesemények**: hívható meg, ha a szolgáltatás hibát észlel.
* **Leképezési események**: nevű ügyfeleken "WithIntent" (csak a ShortPhrase üzemmódban) után utolsó felismerési eredmények strukturált JSON-leképezés van értelmezni.
* **Események eredménye**:
  * A `SpeechRecognitionMode_ShortPhrase` mód, ez az esemény neve, és n-legjobb eredményt ad vissza, és beszéljen befejezése után.
  * A `SpeechRecognitionMode_LongDictation` üzemmód, az eseménykezelő neve többször, ahol a szolgáltatás azonosítja a mondatok szünet alapján.
  * **Az egyes az n-legjobb lehetőségeket**, abban, hogy az érték, és néhány más űrlapok a felismert szöveg adott vissza. További információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Az IOS rendszerhez készült ügyféloldali kódtár – referencia](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Ismerkedés a Microsoft a beszédfelismerés és/vagy Java az Android a cél](GetStartedJavaAndroid.md)
* [A Microsoft-hang transzformációs API a JavaScript az első lépései](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft-hang transzformációs API REST-en keresztül](GetStartedREST.md)
