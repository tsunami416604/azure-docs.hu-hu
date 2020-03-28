---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 7d29f781950784cdb8951b3a6e45d5b5d0f9819f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79539539"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java futásidejű<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Vessünk egy pillantást arra, [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) hogyan jön létre az a egy kulcs és régió használatával.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Felismerő inicializálása

Miután létrehozott egy [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)t, a következő lépés [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)a . inicializálása. Amikor inicializál egy, [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)akkor át `config`kell adnia a . Ez biztosítja a hitelesítő adatokat, amelyek a beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) a következőket kell tennie:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) létre `audioConfig` kell hoznia [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először adja `import` hozzá a következő állításokat.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Ezután az alábbi hivatkozással `AudioConfig` hivatkozhat az objektumra:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable)Ha azonban hívás `fromDefaultMicrophoneInput`helyett létrehoz egy , meg `fromWavFileOutput` fogja `filename` hívni és átadni a paramétert.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Beszéd felismerése

A Java-hoz való beszédsdka [recognizer osztálya](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) néhány olyan módszert tartalmaz, amelyeket beszédfelismerésre használhat.

* Egylövéses felismerés (aszinkron) – Nem blokkoló (aszinkron) módban végzi a felismerést. Ez felismeri az egyetlen utterance (kifejezés). Az egyetlen utterance (kifejezés) végét a csend figyelése határozza meg a végén, vagy amíg legfeljebb 15 másodpercnyi hang feldolgozása meg nem történik.
* Folyamatos felismerés (aszinkron) – Aszinkron módon folyamatos felismerési műveletet kezdeményez. Ha mikrofon használata helyett hangfájlt szeretne megadni, akkor is meg kell adnia egy. Az aszinkron folyamatos felismerés leállításához hívja [meg a stopContinuousRecognitionAsync metódust.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról.](../../../how-to-choose-recognition-mode.md)

### <a name="single-shot-recognition"></a>Egylövetű felismerés

Íme egy példa az aszinkron egylövéses felismerésre a következők használatával: [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Az eredmény kezeléséhez kódot kell írnia. Ez a minta a [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable)következőket értékeli:

* Kinyomtatja a felismerés eredményét:`ResultReason.RecognizedSpeech`
* Ha nincs egyezés a felismeréssel, értesítse a felhasználót:`ResultReason.NoMatch`
* Ha hiba történik, nyomtassa ki a hibaüzenetet:`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Folyamatos felismerés

Folyamatos felismerés egy kicsit több szó, mint egy-shot felismerés. A felismerési eredmények `recognizing`elérése `recognized`érdekében elő kell fizetnie a , és `canceled` eseményekre. A felismerés leállításához [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)fel kell hívnia a programot. Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlban.

Kezdjük a bemenet meghatározásával és a [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)következő inicializálásával:

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Ezután hozzunk létre egy változót a beszédfelismerés állapotának kezeléséhez. Először is, az `Semaphore` osztály hatókörénál jelentjük be.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Feliratkozunk a rendszerből küldött [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)eseményekre.

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Jelzés a közbenső felismerési eredményeket tartalmazó eseményekre.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Jelzés a végső felismerési eredményeket tartalmazó eseményekre (sikeres felismerési kísérlet jelzése).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): A felismerési munkamenet (művelet) végét jelző események jelzése.
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Jelzés a visszavont felismerési eredményeket tartalmazó eseményekhez (olyan felismerési kísérlet jelzése, amelyet ennek eredményeként vagy közvetlen törlési kérelem, vagy átvitel- vagy protokollhiba miatt töröltek).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Mindent felállítva, hívhatjuk [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használataesetén engedélyezheti a diktálás feldolgozását a megfelelő "Diktálás engedélyezése" funkcióval. Ezzel a móddal a beszédfelismerési konfigurációs példány értelmezi a mondatstruktúrák, például az írásjelek szóleírását. Például a "A városban él" kimondott szöveget a "A városban él?" szövegként értelmezi.

A diktálási mód engedélyezéséhez használja a [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) módszerét a on. [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfelismerés gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet franciára. A kódban keresse [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)meg a , majd adja hozzá ezt a sort közvetlenül alatta.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable)egy olyan paraméter, amely egy karakterláncot argumentumként vesz fel. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>A felismerés pontosságának javítása

A beszédfelismerési SDK segítségével többféleképpen is javíthatja a felismerés pontosságát. Vessünk egy pillantást a kifejezéslistákra. A Kifejezéslisták kifejezéssel azonosítják a hangadatokban szereplő ismert kifejezéseket, például egy személy nevét vagy egy adott helyet. A kifejezéslistához egyetlen szó vagy teljes kifejezés adható. Az elismerés során a kifejezéslista egy bejegyzését használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha nem talál pontos egyezést a kifejezéssel, az elismerés nem segít.

> [!IMPORTANT]
> A Kifejezéslista szolgáltatás csak angol nyelven érhető el.

Kifejezéslista használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) objektumot, [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)majd adjon hozzá konkrét szavakat és kifejezéseket a használatával.

A következő [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) felismerésre vagy a beszédfelismerési szolgáltatáshoz való újracsatlakozást követően végrehajtott módosítások.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezéslistát: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek a felismerés pontosságának javítására

A kifejezéslisták csak egy lehetőség a felismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../how-to-custom-speech.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)