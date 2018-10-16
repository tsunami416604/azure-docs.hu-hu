---
title: Java Android rendszeren a Microsoft Speech Recognition API használatának első lépései |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Microsoft Speech API használatával hozhat létre Android-alkalmazásokat, amelyek a beszélt hangot képes szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 4c5243ec14a4494222168bb33b3e840b96f8465e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345254"
---
[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Gyors útmutató: A Bing Speech recognition API-t a Java használata Android rendszeren

A Bing Speech Recognition API-val Android-alkalmazások, amelyek a felhőalapú és a Bing Speech Service segítségével a beszélt hangot képes szöveggé alakítani fejleszthet. Az API-t támogatja, hogy valós idejű streamelési, így az alkalmazás egy időben, és aszinkron módon képes is részleges felismerési eredményeket hang, a szolgáltatás által küldött egy időben.

Ez a cikk egy mintaalkalmazás segítségével hogyan használható a beszédfelismerő ügyfélkódtárának Android az Android-eszközök hang-szöveg transzformációs Java alkalmazások fejlesztéséhez.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

A minta a rendszer által fejlesztett [Android Studio](http://developer.android.com/sdk/index.html) Java-környezetben a Windows számára.

### <a name="get-the-client-library-and-sample-application"></a>Az ügyféloldali kódtár és mintaalkalmazás alkalmazás lekérése

A beszédfelismerő ügyfélkódtárának és az Android-minták elérhetők a [Speech ügyfél-SDK for Android](https://github.com/microsoft/cognitive-speech-stt-android). A minták/SpeechRecoExample könyvtárban buildable mintát talál. A két kódtárakat kell használnia a saját alkalmazások SpeechSDK/libs a armeabi alatt és a x86 mappát. A libandroid_platform.so fájl mérete 22 MB, de a 4 MB-ra üzembe helyezéskor csökken.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a beszédfelismerő API és a egy ingyenes próba-előfizetését kulcs lekérése

A beszédfelismerő API a Cognitive Services-(korábban Project Oxford) részét képezi. Ingyenes próba-előfizetését helyenk beszerezheti a [Cognitive Services-előfizetés](https://azure.microsoft.com/try/cognitive-services/) lapot. Miután kiválasztotta a beszédfelismerő API, válassza ki a **API-kulcs lekérése** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsát adja vissza. Mindkét kulcsot kvóta, így használhatja az egyiket sem vannak társítva.

Ha a használni kívánt *leképezés a felismerés*, is regisztrálni kell a [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Előfizetési kulcs lekérése. Ügyfélkódtárak beszéd használata előtt rendelkeznie kell egy [előfizetési kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
>* Az előfizetési kulcs használja. A megadott Android mintaalkalmazással frissítse a fájl samples/SpeechRecoExample/res/values/strings.xml az előfizetési kulcsok. További információkért lásd: [hozza létre és futtassa a mintát](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>A beszédfelismerő ügyfélkódtárának használata

Az ügyféloldali kódtár, az alkalmazás használatához hajtsa végre a [utasításokat](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Annak az ügyfél kódtár – referencia Android docs mappájában a [Speech ügyfél-SDK for Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Hozhat létre és -minták futtatása

Megtudhatja, hogyan hozhat létre és futtassa a mintát, ez látható [tájékoztató oldalon](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>A minták ismertetése

### <a name="create-recognition-clients"></a>Elismerés ügyfelek létrehozása

A kód a következő minta bemutatja, hogyan hozhat létre a felismerés ügyfélosztályok felhasználói esetek alapján:

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

Az ügyféloldali kódtár biztosít előre megvalósított felismerés ügyfélosztályok Beszédfelismerés a tipikus forgatókönyvek:

* `DataRecognitionClient`: A beszédfelismerés PCM adatokkal (például a fájl- vagy audióanyagot forrás). Az adatok pufferek van osztva, és minden egyes puffer Speech szolgáltatásnak továbbítja. Bármilyen módosítás nélkül történik a pufferek, így a felhasználó a saját csend észlelési alkalmazhatja, ha szükséges. Az adatok WAV-fájlokból áll rendelkezésre, ha a Speech Service a fájlt közvetlenül küldhet adatokat. Ha olyan nyers adat, például hang, a Bluetooth keresztül érkező először küld formátum fejléc beszédszolgáltatás követi az adatokat.
* `MicrophoneRecognitionClient`: A beszédfelismerés együtt származik a mikrofon hang. Ellenőrizze, hogy a mikrofon be van kapcsolva, és a mikrofon származó adatokat a speech recognition szolgáltatásnak továbbítja. A beépített "csend detector használatával" mikrofon adatokra alkalmazza, a felismerés szolgáltatás továbbítás előtt.
* `DataRecognitionClientWithIntent` és `MicrophoneRecognitionClientWithIntent`: felismerés szöveg mellett strukturált vissza ezeket az ügyfeleket, a célt a beszélő, amely az alkalmazások által használható további műveletek alapjául információt. "Leképezés" használatához először betanítja a modellt használatával kell [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Nyelv felismerése

Ha használ `SpeechRecognitionServiceFactory` szeretne létrehozni az ügyfelet, ki kell választania egy nyelvet. Beszédfelismerési szolgáltatás által támogatott nyelvek teljes listáját lásd: [támogatott nyelvek](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Emellett meg kell adnia `SpeechRecognitionMode` létrehozásakor, az ügyfél és `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Mennyi ideig az utterance (kifejezés), legfeljebb 15 másodperc alatt. Mivel a szolgáltatás adatokat küld, az ügyfél megkapta a több részleges és több végső eredményt, több legjobb n választási lehetőség a.
* `LongDictation`: Az utterance (kifejezés), akár két perc hosszú. Mivel a szolgáltatás adatokat küld, a az ügyfél több részleges és több végső eredményt is, ahol a szolgáltatás azonosítja a mondaton belüli szünetet alapján kap.

### <a name="attach-event-handlers"></a>Eseménykezelők csatolása

Az ügyfél különböző eseménykezelők csatolhat létrehozott:

* **Részleges eredményeket események**: Ez az esemény meghívása megtörténik, minden alkalommal, amikor beszédszolgáltatás előrejelzi, előfordulhat, hogy el véleményét, beszéd befejezése előtt is (Ha `MicrophoneRecognitionClient`), vagy fejezze be az adatok küldésének (használatakor `DataRecognitionClient`).
* **Hibaesemények**: meghívva, ha a szolgáltatás hibát észlel.
* **Leképezési események**: "WithIntent" ügyfeleken nevű (kizárólag a `ShortPhrase` mód) után a végső felismerés eredményét egy strukturált JSON-leképezés van értelmezni.
* **Események eredményeként**:
  * A `ShortPhrase` mód, ez az esemény nevezik, és n legjobb eredményeket ad vissza, és beszéljen befejezése után.
  * A `LongDictation` üzemmód, az eseménykezelő neve többször, ahol a szolgáltatás azonosítja a mondaton belüli szünetet alapján.
  * **A legjobb n lehetőségek mindegyike**, egy megbízhatósági érték és néhány másik formája a felismert szöveget adja vissza. További információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Androidhoz készült ügyféloldali kódtár – referencia](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [A C#-ban, a Microsoft Speech API használatának első lépései a .NET-es Windows](GetStartedCSharpDesktop.md)
* [IOS-eszközökön a Microsoft beszédfelismerési API, Objective-C nyelven használatának első lépései](Get-Started-ObjectiveC-iOS.md)
* [A Microsoft Speech API a JavaScript használatának első lépései](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft Speech API REST-en keresztül](GetStartedREST.md)
