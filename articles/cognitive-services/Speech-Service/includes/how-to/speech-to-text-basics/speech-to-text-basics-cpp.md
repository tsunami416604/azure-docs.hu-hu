---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: dapine
ms.openlocfilehash: 8392e03f272c6db2bb384c8031cb975bca53d530
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79372801"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Vessünk egy pillantást arra, [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) hogyan jön létre az a egy kulcs és régió használatával.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Felismerő inicializálása

Miután létrehozott egy [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)t, a következő lépés [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)a . inicializálása. Amikor inicializál egy, [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)akkor át `speech_config`kell adnia a . Ez biztosítja a hitelesítő adatokat, amelyek a beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) a következőket kell tennie:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) létre `audioConfig` kell hoznia [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először adja `using namespace` hozzá a `#include` következő utasítást a definíciók után.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Ezután az alábbi hivatkozással `AudioConfig` hivatkozhat az objektumra:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)Ha azonban hívás `FromDefaultMicrophoneInput`helyett létrehoz egy , meg `FromWavFileOutput` fogja `filename` hívni és átadni a paramétert.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Beszéd felismerése

A C++ beszédfelismerési SDK-hoz tartozó [Recognizer osztály](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) néhány olyan módszert tartalmaz, amelyeket beszédfelismerésre használhat.

* Egylövéses felismerés (aszinkron) – Nem blokkoló (aszinkron) módban végzi a felismerést. Ez felismeri az egyetlen utterance (kifejezés). Az egyetlen utterance (kifejezés) végét a csend figyelése határozza meg a végén, vagy amíg legfeljebb 15 másodpercnyi hang feldolgozása meg nem történik.
* Folyamatos felismerés (aszinkron) – Aszinkron módon folyamatos felismerési műveletet kezdeményez. A felhasználónak csatlakoznia kell az esemény kezeléséhez, hogy megkapja a felismerési eredményeket. Az aszinkron folyamatos felismerés leállításához [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)hívja meg a hívást.

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról.](../../../how-to-choose-recognition-mode.md)

### <a name="single-shot-recognition"></a>Egylövetű felismerés

Íme egy példa az aszinkron egylövéses felismerésre a következők használatával: [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync)

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Az eredmény kezeléséhez kódot kell írnia. Ez a minta a [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason)következőket értékeli:

* Kinyomtatja a felismerés eredményét:`ResultReason::RecognizedSpeech`
* Ha nincs egyezés a felismeréssel, értesítse a felhasználót:`ResultReason::NoMatch`
* Ha hiba történik, nyomtassa ki a hibaüzenetet:`ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Folyamatos felismerés

Folyamatos felismerés egy kicsit több szó, mint egy-shot felismerés. A felismerési eredmények `Recognizing`elérése `Recognized`érdekében elő kell fizetnie a , és `Canceled` eseményekre. A felismerés leállításához meg kell hívnia a [StopContinuousRecognitionAsync metódust.](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync) Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlban.

Kezdjük a bemenet meghatározásával és a [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)következő inicializálásával:

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Ezután hozzunk létre egy változót a beszédfelismerés állapotának kezeléséhez. Először is, kijelentjük, `promise<void>`hogy az elismerés kezdetén nyugodtan feltételezhetjük, hogy még nincs kész.

```cpp
promise<void> recognitionEnd;
```

Feliratkozunk a rendszerből küldött [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)eseményekre.

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Jelzés a közbenső felismerési eredményeket tartalmazó eseményekre.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Jelzés a végső felismerési eredményeket tartalmazó eseményekre (sikeres felismerési kísérlet jelzése).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): A felismerési munkamenet (művelet) végét jelző események jelzése.
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Jelzés a visszavont felismerési eredményeket tartalmazó eseményekhez (olyan felismerési kísérlet jelzése, amelyet ennek eredményeként vagy közvetlen törlési kérelem, vagy átvitel- vagy protokollhiba miatt töröltek).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Mindent felállítva, hívhatjuk [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync).

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használataesetén engedélyezheti a diktálás feldolgozását a megfelelő "Diktálás engedélyezése" funkcióval. Ezzel a móddal a beszédfelismerési konfigurációs példány értelmezi a mondatstruktúrák, például az írásjelek szóleírását. Például a "A városban él" kimondott szöveget a "A városban él?" szövegként értelmezi.

A diktálási mód engedélyezéséhez használja a [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) módszerét a on. [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfelismerés gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet németre. A kódban keresse [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)meg a , majd adja hozzá ezt a sort közvetlenül alatta.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage)egy olyan paraméter, amely egy karakterláncot argumentumként vesz fel. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>A felismerés pontosságának javítása

A beszédfelismerési SDK segítségével többféleképpen is javíthatja a felismerés pontosságát. Vessünk egy pillantást a kifejezéslistákra. A Kifejezéslisták kifejezéssel azonosítják a hangadatokban szereplő ismert kifejezéseket, például egy személy nevét vagy egy adott helyet. A kifejezéslistához egyetlen szó vagy teljes kifejezés adható. Az elismerés során a kifejezéslista egy bejegyzését használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha nem talál pontos egyezést a kifejezéssel, az elismerés nem segít.

> [!IMPORTANT]
> A Kifejezéslista szolgáltatás csak angol nyelven érhető el.

Kifejezéslista használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) objektumot, [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase)majd adjon hozzá konkrét szavakat és kifejezéseket a használatával.

A következő [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) felismerésre vagy a beszédfelismerési szolgáltatáshoz való újracsatlakozást követően végrehajtott módosítások.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezéslistát: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek a felismerés pontosságának javítására

A kifejezéslisták csak egy lehetőség a felismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../how-to-custom-speech.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)