---
title: Ismerkedés a Microsoft beszéd felismerés API-val Android rendszeren Java nyelven |} Microsoft Docs
description: A Microsoft-hang transzformációs API használatával, amelyek a szóbeli hang szöveg Android alkalmazások fejlesztéséhez.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: a10f7be1c36fb431016a9867f606e26be858069e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347450"
---
# <a name="get-started-with-speech-recognition-in-java-on-android"></a>Az Android Java a beszédfelismerés használatába

A beszédfelismerés felismerés API-val Android-alkalmazások, amelyek segítségével a felhőalapú beszéd szolgáltatás szóbeli hang szöveggé alakítani fejleszthet. Az API támogatja valós idejű streamelési, így az alkalmazás egyszerre is és részleges felismerési eredmények aszinkron módon fogadásához azt a szolgáltatás hang küld egy időben.

Ez a cikk egy mintaalkalmazást használ a beszédfelismerés ügyféloldali kódtára a Android beszéd-szöveg alkalmazások fejlesztéséhez Java nyelven az Android-eszközök használatát mutatják be.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

A minta fejlesztése által [Android Studio](http://developer.android.com/sdk/index.html) Windows Java nyelven.

### <a name="get-the-client-library-and-sample-application"></a>Az ügyfél kódtár és mintaalkalmazás alkalmazás beszerzése

A beszédfelismerés ügyféloldali kódtár és Android-példák találhatók a [beszéd ügyfél Android SDK](https://github.com/microsoft/cognitive-speech-stt-android). A buildable minta a minták/SpeechRecoExample könyvtárban található. A két szalagtárat szeretné használni a saját SpeechSDK/függvénytárak a armeabi az alkalmazások és a x86 található mappát. A libandroid_platform.so fájl mérete 22 MB, de a 4 MB központi telepítéskor csökken.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Fizessen elő a hang transzformációs API-t, és egy ingyenes próba-előfizetés kulcs beszerzése

A Diktálásfelismerési API (korábban projekt Oxford) kognitív szolgáltatások részét képezi. Ingyenes próba-előfizetés kulcsokat beszerezheti a [kognitív szolgáltatások előfizetés](https://azure.microsoft.com/try/cognitive-services/) lap. Miután kiválasztotta a hang transzformációs API-t, jelölje ki a **API-kulcs beolvasása** a kulcs beszerzése. Egy elsődleges és másodlagos kulcsot adja vissza. Mindkét kulcsot, vagy kulccsal a azonos kvóta vannak társítva.

Ha a használni kívánt *biztonsági mentés felismerés*, is be kell jelentkeznie a a [nyelvi ismertetése intelligens szolgáltatás (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Egy előfizetés kulcs beszerzése. Beszéd klienskódtárak használata előtt rendelkeznie kell egy [előfizetés kulcs](https://azure.microsoft.com/try/cognitive-services/).
>
>* Az előfizetés-kulcsot használ. A megadott Android mintaalkalmazást frissítse a fájl samples/SpeechRecoExample/res/values/strings.xml az előfizetés kulcsokkal. További információkért lásd: [létrehozása és futtatása a minták](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>A beszédfelismerés ügyféloldali kódtár használata

Hajtsa végre az ügyféloldali kódtár az alkalmazásban, a [utasításokat](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Az ügyfél kódtár – dokumentáció Android az található, a dokumentumok mappában a [beszéd ügyfél Android SDK](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Hozza létre és minták futtatása

Megtudhatja, hogyan lehet létrehozni, és minták futtatása, megjelenik ez [tájékoztató oldalon](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Minták alapján

### <a name="create-recognition-clients"></a>Hozzon létre felismerés ügyfelek

A kód a következő példa bemutatja, hogyan felismerés ügyfél osztályokkal felhasználói forgatókönyvek alapján:

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

Az ügyféloldali kódtár biztosít előre megvalósított felismerés ügyfél osztályok Beszédfelismerés a jellemző forgatókönyvek:

* `DataRecognitionClient`: A beszédfelismerés PCM adatait (például egy fájl vagy a hallható forrás). Az adatok van felosztva pufferek, majd minden puffer beszéd szolgáltatásnak továbbítja. Módosítás nélkül azért van szükség, a buffers, így a felhasználó a saját csend észlelési alkalmazhatja, ha szükséges. Ha az adatokat a WAV-fájlokból áll rendelkezésre, a beszédfelismerés szolgáltatás fájl jobbról küldhet adatokat. Ha nyers adatok, például hang Bluetooth, keresztül érkező, először formátum fejléc küldése az adatokat pedig Beszéd szolgáltatáshoz.
* `MicrophoneRecognitionClient`: A Beszédfelismerés a hang a mikrofon származik. Győződjön meg arról, hogy a mikrofon be van kapcsolva, a mikrofon adatait a beszédfelismerés felismerés szolgáltatásnak küldi el. A beépített "csend, beállítás" alkalmazzák a mikrofon adatokat a felismerési szolgáltatás továbbítás előtt.
* `DataRecognitionClientWithIntent` és `MicrophoneRecognitionClientWithIntent`: ezek az ügyfelek visszaállításához mellett felismerés szöveg, strukturált a hangalapú, amely az alkalmazások által használható további műveletek alapjául a célt információt. "Cél" használatához először használatával a modell betanításához szüksége [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Beszédfelismerési nyelv

Amikor `SpeechRecognitionServiceFactory` az ügyfél létrehozása, ki kell választania egy nyelvet. A beszédfelismerés szolgáltatás által támogatott nyelvek teljes listájáért lásd: [támogatott nyelv](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Is meg kell adnia `SpeechRecognitionMode` létrehozásakor az ügyfél és `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: Legfeljebb 15 egy utterance hosszú másodperc. A szolgáltatás adatokat küld, mivel az ügyfél megkapja a több részleges eredményt és egy végeredményt több n-legjobb választást.
* `LongDictation`: Egy utterance akár két perc hosszú. A szolgáltatás adatokat küld, mivel az ügyfél megkapja a több részleges eredményt, és több végső eredmény, ha a szolgáltatás azonosítja a mondatok szünetel.

### <a name="attach-event-handlers"></a>Az eseménykezelők csatolása

Az ügyfél különböző eseménykezelők csatolhat létrehozott:

* **Részleges eredmények események**: Ez az esemény minden alkalommal, amikor a beszédfelismerés szolgáltatás előrejelzi mi, előfordulhat, hogy kell arról, még mielőtt beszéd menüelemnek (használatakor `MicrophoneRecognitionClient`) vagy a Befejezés adatküldés (használatakor `DataRecognitionClient`).
* **Hibaesemények**: hívható meg, ha a szolgáltatás hibát észlel.
* **Leképezési események**: "WithIntent" ügyfelek hívása (csak a `ShortPhrase` mód) után utolsó felismerés eredménye strukturált JSON-leképezés van értelmezni.
* **Események eredménye**:
  * A `ShortPhrase` mód, ez az esemény neve, és n-legjobb eredményt ad vissza, és beszéljen befejezése után.
  * A `LongDictation` üzemmód, az eseménykezelő neve többször, ahol a szolgáltatás azonosítja a mondatok szünet alapján.
  * **Az egyes az n-legjobb lehetőségeket**, abban, hogy az érték, és néhány más űrlapok a felismert szöveg adott vissza. További információkért lásd: [kimeneti formátum](../Concepts.md#output-format).

## <a name="related-topics"></a>Kapcsolódó témakörök

* [Androidhoz készült ügyféloldali kódtár – referencia](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [A .NET Windows Ismerkedés a C# Microsoft Diktálásfelismerési API-val](GetStartedCSharpDesktop.md)
* [Ismerkedés a Microsoft-hang transzformációs API Objective-C az IOS rendszerű eszközökön](Get-Started-ObjectiveC-iOS.md)
* [A Microsoft-hang transzformációs API a JavaScript az első lépései](GetStartedJSWebsockets.md)
* [Ismerkedés a Microsoft-hang transzformációs API REST-en keresztül](GetStartedREST.md)
