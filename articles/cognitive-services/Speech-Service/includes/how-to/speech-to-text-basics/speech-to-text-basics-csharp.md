---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 982c3c6011936c184c55dd92a76d4aec023baaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399718"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET keretrendszer<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET mag<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Egység<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin között<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Vessünk egy pillantást arra, [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) hogyan jön létre az a egy kulcs és régió használatával. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot.

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Felismerő inicializálása

Miután létrehozott egy [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)t, a következő lépés [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)a . inicializálása. Amikor inicializál egy, [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)akkor át `speechConfig`kell adnia a . Ez biztosítja a hitelesítő adatokat, amelyek a beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) a következőket kell tennie:

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) létre `audioConfig` kell hoznia [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először adja `using` hozzá a következő utasítást.

```csharp
using Microsoft.CognitiveServices.Speech.Audio;
```

Ezután az alábbi hivatkozással `AudioConfig` hivatkozhat az objektumra:

```csharp
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet)Ha azonban hívás `FromDefaultMicrophoneInput`helyett létrehoz egy , meg `FromWavFileOutput` fogja `filename` hívni és átadni a paramétert.


```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Beszéd felismerése

A [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) beszédsdk a beszédfelismerési SDK-osztálya néhány olyan módszert tartalmaz, amelyeket a beszédfelismeréshez használhat.

* Egylövéses felismerés (aszinkron) – Nem blokkoló (aszinkron) módban végzi a felismerést. Ez felismeri az egyetlen utterance (kifejezés). Az egyetlen utterance (kifejezés) végét a csend figyelése határozza meg a végén, vagy amíg legfeljebb 15 másodpercnyi hang feldolgozása meg nem történik.
* Folyamatos felismerés (aszinkron) – Aszinkron módon folyamatos felismerési műveletet kezdeményez. A felhasználó regisztrálja az eseményeket, és kezeli a különböző alkalmazásállapot. Az aszinkron folyamatos felismerés leállításához [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)hívja meg a hívást.

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról.](../../../how-to-choose-recognition-mode.md)

### <a name="single-shot-recognition"></a>Egylövetű felismerés

Íme egy példa az aszinkron egylövéses felismerésre a következők használatával: [`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet)

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

Az eredmény kezeléséhez kódot kell írnia. Ez a minta a [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet)következőket értékeli:

* Kinyomtatja a felismerés eredményét:`ResultReason.RecognizedSpeech`
* Ha nincs egyezés a felismeréssel, értesítse a felhasználót:`ResultReason.NoMatch`
* Ha hiba történik, nyomtassa ki a hibaüzenetet:`ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        Console.WriteLine($"    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

### <a name="continuous-recognition"></a>Folyamatos felismerés

Folyamatos felismerés egy kicsit több szó, mint egy-shot felismerés. A felismerési eredmények `Recognizing`elérése `Recognized`érdekében elő kell fizetnie a , és `Canceled` eseményekre. A felismerés leállításához [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)fel kell hívnia a programot. Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlban.

Kezdjük a bemenet meghatározásával és a [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)következő inicializálásával:

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
Ezután hozzunk létre egy változót a beszédfelismerés állapotának kezeléséhez. Először is, az `TaskCompletionSource<int>` előző nyilatkozatok után jelentjük be.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Feliratkozunk a rendszerből küldött [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)eseményekre.

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet): Jelzés a közbenső felismerési eredményeket tartalmazó eseményekre.
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet): Jelzés a végső felismerési eredményeket tartalmazó eseményekre (sikeres felismerési kísérlet jelzése).
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet): A felismerési munkamenet (művelet) végét jelző események jelzése.
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet): Jelzés a visszavont felismerési eredményeket tartalmazó eseményekhez (olyan felismerési kísérlet jelzése, amelyet ennek eredményeként vagy közvetlen törlési kérelem, vagy átvitel- vagy protokollhiba miatt töröltek).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Mindent felállítva, hívhatjuk [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet).

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használataesetén engedélyezheti a diktálás feldolgozását a megfelelő "Diktálás engedélyezése" funkcióval. Ezzel a móddal a beszédfelismerési konfigurációs példány értelmezi a mondatstruktúrák, például az írásjelek szóleírását. Például a "A városban él" kimondott szöveget a "A városban él?" szövegként értelmezi.

A diktálási mód engedélyezéséhez használja a [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet) módszerét a on. [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfelismerés gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet olaszra. A kódban keresse [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)meg a , majd adja hozzá ezt a sort közvetlenül alatta.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

A [`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet) tulajdonság nyelv-területi formátumú karakterláncot vár. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájának **Területi** oszlopában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>A felismerés pontosságának javítása

A beszédfelismerési SDK segítségével többféleképpen is javíthatja a felismerés pontosságát. Vessünk egy pillantást a kifejezéslistákra. A Kifejezéslisták kifejezéssel azonosítják a hangadatokban szereplő ismert kifejezéseket, például egy személy nevét vagy egy adott helyet. A kifejezéslistához egyetlen szó vagy teljes kifejezés adható. Az elismerés során a kifejezéslista egy bejegyzését használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha nem talál pontos egyezést a kifejezéssel, az elismerés nem segít.

> [!IMPORTANT]
> A Kifejezéslista szolgáltatás csak angol nyelven érhető el.

Kifejezéslista használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) objektumot, [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet)majd adjon hozzá konkrét szavakat és kifejezéseket a használatával.

A következő [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) felismerésre vagy a beszédfelismerési szolgáltatáshoz való újracsatlakozást követően végrehajtott módosítások.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezéslistát: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek a felismerés pontosságának javítására

A kifejezéslisták csak egy lehetőség a felismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../how-to-custom-speech.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)