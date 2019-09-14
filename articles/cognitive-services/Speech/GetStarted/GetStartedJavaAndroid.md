---
title: Ismerkedés a Microsoft Speech Recognition API-val Java-on Androidon | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Microsoft Speech API-val olyan Android-alkalmazásokat fejleszthet, amelyek szöveggé alakítják ki a szóbeli hangot.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 77fee2ecee9cfabe3fad9c1df2c41c7803c3367e
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966833"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Gyors útmutató: A Bing Speech felismerési API használata Java-on Android rendszeren

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Bing Speech felismerési API-val olyan Android-alkalmazásokat fejleszthet, amelyek a felhőalapú Bing Speech szolgáltatást használják a beszélt hang szöveggé alakításához. Az API támogatja a valós idejű folyamatos átvitelt, így az alkalmazása egyidejűleg és aszinkron módon is megkaphatja a részleges felismerési eredményeket, miközben a hangot küldi a szolgáltatásnak.

Ez a cikk egy minta alkalmazást használ annak bemutatására, hogyan használhatók az Android rendszerhez készült beszédfelismerési ügyfélkódtár a Java for Android-eszközökhöz készült beszéd-szöveg alkalmazások fejlesztéséhez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

A mintát [Android Studio](https://developer.android.com/sdk/index.html) a javában fejlesztettük ki.

### <a name="get-the-client-library-and-sample-application"></a>Az ügyféloldali kódtár és a minta alkalmazás beszerzése

Az Androidhoz készült beszédfelismerési ügyfélkódtár és minták az [androidos Speech Client SDK](https://github.com/microsoft/cognitive-speech-stt-android)-ban érhetők el. A minta/SpeechRecoExample könyvtár alatt megtalálhatja a felépíthető mintát. A saját alkalmazásaiban használt két kódtárat a armeabi és az x86 mappában található SpeechSDK/libs alatt találja. A libandroid_platform mérete. így a fájl 22 MB, de a telepítéskor 4 MB-ra csökken.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Feliratkozás a Speech API-ra, és ingyenes próbaverziós előfizetési kulcs beszerzése

A Speech API Cognitive Services (korábban Project Oxford) része. A [Cognitive Services előfizetés](https://azure.microsoft.com/try/cognitive-services/) oldaláról ingyenes próbaverziós előfizetési kulcsokat szerezhet be. Miután kiválasztotta a Speech API-t, válassza az **API-kulcs beolvasása** elemet a kulcs lekéréséhez. Egy elsődleges és egy másodlagos kulcsot ad vissza. Mindkét kulcs ugyanahhoz a kvótához van kötve, így bármelyik kulcsot használhatja.

Ha az *elismerést*kívánja használni szándékkal, regisztrálnia kell a [Language Understanding Intelligent Service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)is.

> [!IMPORTANT]
>* Előfizetési kulcs beszerzése. A Speech Client kódtárak használata előtt rendelkeznie kell egy [előfizetési kulccsal](https://azure.microsoft.com/try/cognitive-services/).
>
>* Használja az előfizetési kulcsot. A megadott androidos minta alkalmazással frissítse a Samples/SpeechRecoExample/res/Values/Strings. xml fájlt az előfizetési kulcsaival. További információ: [minták készítése és futtatása](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>A beszédfelismerési ügyfélkódtár használata

Az ügyfél függvénytárának az alkalmazásban való használatához kövesse az [utasításokat](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Az Androidhoz készült ügyféloldali kódtár-referenciát az [Androidhoz készült Speech Client SDK](https://github.com/microsoft/cognitive-speech-stt-android)docs mappában találja.

## <a name="build-and-run-samples"></a>Minták készítése és futtatása

A minták létrehozásával és futtatásával kapcsolatos további információkért tekintse meg ezt a [readme oldalt](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Minták magyarázata

### <a name="create-recognition-clients"></a>Felismerő ügyfelek létrehozása

Az alábbi példában szereplő kód azt mutatja be, hogyan hozhatók létre felismerési ügyfél-osztályok felhasználói forgatókönyvek alapján:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

Az ügyféloldali kódtár előre megvalósított felismerési ügyféloldali osztályokat biztosít a beszédfelismerés jellemző forgatókönyvei esetében:

* `DataRecognitionClient`: Beszédfelismerés PCM-adatokkal (például fájlból vagy Hangforrásból). Az adathalmazok a pufferekben vannak felosztva, és minden puffer elküldése a Speech Service-nek. A pufferek módosítása nem történt meg, így a felhasználó igény szerint saját csend-észlelést alkalmazhat. Ha az adatok WAV-fájlokból származnak, az adatok a fájlból a Speech Service-be való jogosultsággal küldhetők el. Ha a nyers adatok például a Bluetooth-kapcsolaton keresztül érkeznek, akkor először egy Format fejlécet kell küldenie a Speech Service-nek, amelyet az adatok követnek.
* `MicrophoneRecognitionClient`: Beszédfelismerés a mikrofonból érkező hangokkal. Győződjön meg arról, hogy a mikrofon be van kapcsolva, és a rendszer a mikrofonból származó adatok elküldése a beszédfelismerési szolgáltatásba. A rendszer egy beépített "Silence detektor"-t alkalmaz a mikrofonba az adatfelismerési szolgáltatásba való elküldés előtt.
* `DataRecognitionClientWithIntent`és `MicrophoneRecognitionClientWithIntent`: Ezek az ügyfelek az elismerés szövege mellett strukturált információkat adnak vissza a beszélő szándékáról, amely az alkalmazások további műveleteinek elvégzésére használható. A "szándék" használatához először a [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)használatával kell betanítania a modellt.

### <a name="recognition-language"></a>Felismerési nyelv

Amikor az- `SpeechRecognitionServiceFactory` ügyfél létrehozására használja, ki kell választania egy nyelvet. A Speech Service által támogatott nyelvek teljes listájáért lásd: [támogatott nyelvek](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Azt is meg kell adnia `SpeechRecognitionMode` , hogy mikor hozza létre az `SpeechRecognitionServiceFactory`ügyfelet a következővel:

* `ShortPhrase`: A teljes érték 15 másodperc. Ahogy a szolgáltatás elküldi az adattovábbítást, az ügyfél több részleges eredményt kap, és egy végeredményt több n-legjobb lehetőség közül.
* `LongDictation`: Akár két percet is igénybe vehet. Ahogy a szolgáltatás elküldi az adatküldés eredményeit, az ügyfél több részleges eredményt kap, és több végső eredményt fog kapni, amely alapján a szolgáltatás azonosítja a mondatot.

### <a name="attach-event-handlers"></a>Eseménykezelők csatolása

A létrehozott ügyfélhez különböző eseménykezelőket is csatolhat:

* **Részleges eredmények eseményei**: Ez az esemény minden alkalommal megjelenik, amikor a Speech Service előre jelzi, hogy mit mond, még mielőtt elvégezte a beszédet (ha használja `MicrophoneRecognitionClient`), vagy befejezi az adatok küldését (ha használja `DataRecognitionClient`).
* **Hiba eseményei**: Hívása, ha a szolgáltatás hibát észlel.
* **Leképezési események**: A rendszer a végső felismerési eredmény elemzése után `ShortPhrase` a "WithIntent" ügyfeleket (csak a módban) hívja meg egy strukturált JSON-szándékba.
* **Eredmény eseményei**:
  * `ShortPhrase` Ebben az esetben ez az esemény meghívásra kerül, és a beszéd befejezése után az n-legjobb eredményt adja vissza.
  * A `LongDictation` módban az eseménykezelőt többször kell meghívni, attól függően, hogy a szolgáltatás hogyan azonosítja a mondatot.
  * **Minden n-legjobb választás esetében**a rendszer egy megbízhatósági értéket és a felismert szöveg néhány különböző formáját adja vissza. További információ: [kimeneti formátum](../Concepts.md#output-format).

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Ügyféloldali függvénytár-referenciák Android rendszerhez](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Ismerkedés a Microsoft Speech API- C# val a Windows rendszerben a .net-ben](GetStartedCSharpDesktop.md)
* [Ismerkedés a Microsoft Speech API-val a Objective-C-ben iOS rendszeren](Get-Started-ObjectiveC-iOS.md)
* [Ismerkedés a Microsoft Speech API-val a JavaScriptben](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft Speech API-val REST használatával](GetStartedREST.md)
