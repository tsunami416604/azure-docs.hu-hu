---
title: Ismerkedés a Bing Speech felismerési API-val a Objective-C-ben iOS-en | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Bing Speech felismerési API-val olyan iOS-alkalmazásokat fejleszthet, amelyek szöveggé alakítják a beszélt hangot.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e42784e6d2751f7e76aec8caf1d6e1f9f09a9fd1
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965931"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Gyors útmutató: Az iOS-es Objective-C Bing Speech-felismerési API használata

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A beszédfelismerési API használatával olyan iOS-alkalmazásokat fejleszthet, amelyek felhőalapú beszédfelismerési szolgáltatást használnak a beszélt hang szöveggé alakításához. Az API támogatja a valós idejű folyamatos átvitelt, így az alkalmazása egyidejűleg és aszinkron módon is megkaphatja a részleges felismerési eredményeket, miközben a hangot küldi a szolgáltatásnak.

Ez a cikk egy minta alkalmazást használ a beszédfelismerési API használatának első lépéseihez az iOS-alkalmazások fejlesztéséhez. A teljes API-referenciáért tekintse meg a [SPEECH SDK ügyféloldali függvénytár-referenciáját](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Győződjön meg arról, hogy a Mac XCode IDE telepítve van.

### <a name="get-the-client-library-and-examples"></a>Az ügyféloldali kódtár és példák beszerzése

Az iOS rendszerhez készült beszédfelismerési ügyfélkódtár és példák az [iOS-hez készült Speech Client SDK](https://github.com/microsoft/cognitive-speech-stt-ios)-ban érhetők el.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Feliratkozás a Speech Recognition API-ra, és ingyenes próbaverziós előfizetési kulcs beszerzése

A Speech API Cognitive Services (korábban Project Oxford) része. A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

Ha az *elismerést*kívánja használni szándékkal, regisztrálnia kell a [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)is.

> [!IMPORTANT]
> * Előfizetési kulcs beszerzése. A Speech Client kódtárak használata előtt rendelkeznie kell egy [előfizetési kulccsal](https://azure.microsoft.com/try/cognitive-services/).
>
> * Használja az előfizetési kulcsot. A megadott iOS-minta alkalmazással frissítenie kell a file Samples/SpeechRecognitionServerExample/Settings. plist fájlt az előfizetési kulccsal. További információ: [minták készítése és futtatása](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>A beszédfelismerési ügyfélkódtár használata

Az ügyféloldali kódtár XCode-projektbe való felvételéhez kövesse az alábbi [utasításokat](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Az iOS-hez készült ügyféloldali kódtár-hivatkozás megkereséséhez tekintse meg ezt a [weblapot](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Minták készítése és futtatása

A minták létrehozásával és futtatásával kapcsolatos információkért tekintse meg ezt a [readme oldalt](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Minták magyarázata

### <a name="create-recognition-clients"></a>Felismerő ügyfelek létrehozása

A példában szereplő következő kód bemutatja, hogyan hozhatók létre felismerési ügyfél-osztályok felhasználói forgatókönyvek alapján:

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

Az ügyféloldali kódtár előre megvalósított felismerési ügyféloldali osztályokat biztosít a beszédfelismerés jellemző forgatókönyvei esetében:

* `DataRecognitionClient`: Beszédfelismerés PCM-adatokkal (például fájlból vagy Hangforrásból). Az adathalmazok a pufferekben vannak felosztva, és minden puffer elküldése a Speech Service-nek. A pufferek módosítása nem történt meg, így a felhasználók a saját csend észlelését is alkalmazhatják, ha szükséges. Ha az adatok WAV-fájlokból vannak megadva, akkor a fájlból közvetlenül a kiszolgálóra küldheti az adatait. Ha a nyers adatok például a Bluetooth-kapcsolaton keresztül érkeznek, először egy Format fejlécet kell küldenie a kiszolgálónak, amelyet az adatok követnek.
* `MicrophoneRecognitionClient`: Beszédfelismerés a mikrofonból érkező hangokkal. Győződjön meg arról, hogy a mikrofon be van kapcsolva, és a rendszer a mikrofonból származó adatok elküldése a beszédfelismerési szolgáltatásba. A rendszer egy beépített "Silence detektor"-t alkalmaz a mikrofonba az adatfelismerési szolgáltatásba való elküldés előtt.
* `DataRecognitionClientWithIntent`és `MicrophoneRecognitionClientWithIntent`: Az elismerés szövege mellett ezek az ügyfelek a beszélő szándékával kapcsolatos strukturált adatokat adnak vissza, amelyeket az alkalmazások a további műveletek elvégzésére használhatnak. A "szándék" használatához először a [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)használatával kell betanítania a modellt.

### <a name="recognition-language"></a>Felismerési nyelv

Amikor az- `SpeechRecognitionServiceFactory` ügyfél létrehozására használja, ki kell választania egy nyelvet. A Speech Service által támogatott nyelvek teljes listájáért lásd: [támogatott nyelvek](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Azt is meg kell adnia `SpeechRecognitionMode` , hogy mikor hozza létre az `SpeechRecognitionServiceFactory`ügyfelet a következővel:

* `SpeechRecognitionMode_ShortPhrase`: A teljes érték 15 másodperc. Ahogy a szolgáltatás elküldi az adattovábbítást, az ügyfél több részleges eredményt kap, és egy végeredményt több n-legjobb lehetőség közül.
* `SpeechRecognitionMode_LongDictation`: Akár két percet is igénybe vehet. Ahogy a szolgáltatás elküldi az adatküldés eredményeit, az ügyfél több részleges eredményt kap, és több végső eredményt fog kapni, attól függően, hogy a kiszolgáló hol azonosítja a mondatot.

### <a name="attach-event-handlers"></a>Eseménykezelők csatolása

A létrehozott ügyfélhez különböző eseménykezelőket is csatolhat:

* **Részleges eredmények eseményei**: Ez az esemény minden alkalommal megjelenik, amikor a beszédfelismerési szolgáltatás megjósolja, hogy mit kell mondania, még mielőtt elvégezte a beszédet (ha használja `MicrophoneRecognitionClient`), vagy befejezi az adatok küldését (ha használja `DataRecognitionClient`).
* **Hiba eseményei**: Hívása, ha a szolgáltatás hibát észlel.
* **Leképezési események**: A rendszer a végső felismerési eredmény elemzése után a "WithIntent" ügyfeleket (csak ShortPhrase módban) hívja meg egy strukturált JSON-szándékba.
* **Eredmény eseményei**:
  * `SpeechRecognitionMode_ShortPhrase` Ebben az esetben ez az esemény meghívásra kerül, és a beszéd befejezése után az n-legjobb eredményt adja vissza.
  * A `SpeechRecognitionMode_LongDictation` módban az eseménykezelőt többször kell meghívni, attól függően, hogy a szolgáltatás hogyan azonosítja a mondatot.
  * **Minden n-legjobb választás esetében**a rendszer egy megbízhatósági értéket és a felismert szöveg néhány különböző formáját adja vissza. További információ: [kimeneti formátum](../Concepts.md#output-format).

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Az iOS-hez készült ügyféloldali kódtár-referenciák](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Ismerkedés a Microsoft beszédfelismerési szolgáltatásával és/vagy szándékával a Java Androidon](GetStartedJavaAndroid.md)
* [Ismerkedés a Microsoft Speech API-val a JavaScriptben](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft Speech API-val REST használatával](GetStartedREST.md)
