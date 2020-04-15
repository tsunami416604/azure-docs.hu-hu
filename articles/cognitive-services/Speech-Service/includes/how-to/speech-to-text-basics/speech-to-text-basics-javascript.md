---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/14/2020
ms.author: dapine
ms.openlocfilehash: 668cf9e831191a5d649f7dd82af03eb637bb0d3a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314226"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit megtehetne, telepítenie kell a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"> </span>SDK-t. </a> A platformtól függően kövesse az alábbi utasításokat:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webböngésző<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Ezenkívül a célkörnyezettől függően használja az alábbiak egyikét:

# <a name="import"></a>[Importálása](#tab/import)

```javascript
import * as sdk from "microsoft-cognitiveservices-speech-sdk";
```

További információ: `import` <a href="https://javascript.info/import-export" target="_blank">export and <span class="docon docon-navigate-external x-hidden-focus"> </span>import </a>.

# <a name="require"></a>[Igényel](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

További információ: `require` <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[szkriptet.](#tab/script)

Töltse le és bontsa ki a <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* fájlt, és helyezze el a HTML-fájl számára elérhető mappában.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Ha webböngészőt céloz meg, és `<script>` a címkét használja; az `sdk` előtag nem szükséges. Az `sdk` előtag egy alias, amelyet `require` a mi vagy modulunk elnevezésére használunk. `import`

---

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Vessünk egy pillantást arra, [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) hogyan jön létre az a egy kulcs és régió használatával. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Felismerő inicializálása

Miután létrehozott egy [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)t, a következő lépés [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)a . inicializálása. Amikor inicializál egy, [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)akkor át `speechConfig`kell adnia a . Ez biztosítja a hitelesítő adatokat, amelyek a beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) a következőket kell tennie:

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) létre `audioConfig` kell hoznia [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Hivatkozzon `AudioConfig` az objektumra az alábbiak szerint:

```javascript
const audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
const speechConfig = sdk.SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . Ez azonban csak akkor végezhető el, ha **a Node.js programot célozza** meg, és amikor [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)hívást `fromDefaultMicrophoneInput`helyett létrehoz egy , meg fogja hívni `fromWavFileOutput` és átadni a `filename` paramétert.

```javascript
const audioConfig = sdk.AudioConfig.fromWavFileInput("YourAudioFile.wav");
const speechConfig = sdk.SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Beszéd felismerése

A [C#](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) beszédsdk a beszédfelismerési SDK-osztálya néhány olyan módszert tartalmaz, amelyeket a beszédfelismeréshez használhat.

* Egylövéses felismerés (aszinkron) – Nem blokkoló (aszinkron) módban végzi a felismerést. Ez felismeri az egyetlen utterance (kifejezés). Az egyetlen utterance (kifejezés) végét a csend figyelése határozza meg a végén, vagy amíg legfeljebb 15 másodpercnyi hang feldolgozása meg nem történik.
* Folyamatos felismerés (aszinkron) – Aszinkron módon folyamatos felismerési műveletet kezdeményez. A felhasználó regisztrálja az eseményeket, és kezeli a különböző alkalmazásállapot. Az aszinkron folyamatos felismerés leállításához [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)hívja meg a hívást.

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról.](../../../how-to-choose-recognition-mode.md)

### <a name="single-shot-recognition"></a>Egylövetű felismerés

Íme egy példa az aszinkron egylövéses felismerésre a következők használatával: [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync)

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Az eredmény kezeléséhez kódot kell írnia. Ez a minta a [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason)következőket értékeli:

* Kinyomtatja a felismerés eredményét:`ResultReason.RecognizedSpeech`
* Ha nincs egyezés a felismeréssel, értesítse a felhasználót:`ResultReason.NoMatch`
* Ha hiba történik, nyomtassa ki a hibaüzenetet:`ResultReason.Canceled`

```javascript
switch (result.reason) {
    case sdk.ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.Text}`);
        console.log("    Intent not recognized.");
        break;
    case sdk.ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case sdk.ResultReason.Canceled:
        const cancellation = sdk.CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.Reason}`);

        if (cancellation.Reason == sdk.CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.ErrorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
}
```

### <a name="continuous-recognition"></a>Folyamatos felismerés

Folyamatos felismerés egy kicsit több szó, mint egy-shot felismerés. A felismerési eredmények `Recognizing`elérése `Recognized`érdekében elő kell fizetnie a , és `Canceled` eseményekre. A felismerés leállításához [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)fel kell hívnia a programot. Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlban.

Kezdjük a bemenet meghatározásával és a [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)következő inicializálásával:

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Feliratkozunk a rendszerből küldött [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)eseményekre.

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing): Jelzés a közbenső felismerési eredményeket tartalmazó eseményekre.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized): Jelzés a végső felismerési eredményeket tartalmazó eseményekre (sikeres felismerési kísérlet jelzése).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped): A felismerési munkamenet (művelet) végét jelző események jelzése.
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled): Jelzés a visszavont felismerési eredményeket tartalmazó eseményekhez (olyan felismerési kísérlet jelzése, amelyet ennek eredményeként vagy közvetlen törlési kérelem, vagy átvitel- vagy protokollhiba miatt töröltek).

```javascript
recognizer.Recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.Result.Text}`);
};

recognizer.recognized = (s, e) => {
    if (e.Result.Reason == sdk.ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.Result.Text}`);
    }
    else if (e.Result.Reason == sdk.ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.Reason}`);

    if (e.Reason == sdk.CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.ErrorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.ErrorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.SessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Mindent felállítva, hívhatjuk [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync).

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használataesetén engedélyezheti a diktálás feldolgozását a megfelelő "Diktálás engedélyezése" funkcióval. Ezzel a móddal a beszédfelismerési konfigurációs példány értelmezi a mondatstruktúrák, például az írásjelek szóleírását. Például a "A városban él" kimondott szöveget a "A városban él?" szövegként értelmezi.

A diktálási mód engedélyezéséhez használja a [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) módszerét a on. [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfelismerés gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet olaszra. A kódban keresse [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)meg a , majd adja hozzá ezt a sort közvetlenül alatta.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

A [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) tulajdonság nyelv-területi formátumú karakterláncot vár. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájának **Területi** oszlopában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>A felismerés pontosságának javítása

A beszédfelismerési SDK segítségével többféleképpen is javíthatja a felismerés pontosságát. Vessünk egy pillantást a kifejezéslistákra. A Kifejezéslisták kifejezéssel azonosítják a hangadatokban szereplő ismert kifejezéseket, például egy személy nevét vagy egy adott helyet. A kifejezéslistához egyetlen szó vagy teljes kifejezés adható. Az elismerés során a kifejezéslista egy bejegyzését használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha nem talál pontos egyezést a kifejezéssel, az elismerés nem segít.

> [!IMPORTANT]
> A Kifejezéslista szolgáltatás csak angol nyelven érhető el.

Kifejezéslista használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) objektumot, [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-)majd adjon hozzá konkrét szavakat és kifejezéseket a használatával.

A következő [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) felismerésre vagy a beszédfelismerési szolgáltatáshoz való újracsatlakozást követően végrehajtott módosítások.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezéslistát:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek a felismerés pontosságának javítására

A kifejezéslisták csak egy lehetőség a felismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../how-to-custom-speech.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)