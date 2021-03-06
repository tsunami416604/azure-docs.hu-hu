---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: 4e5e23c578d3c8ab72ae4b1483dc14c2161b9451
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96912339"
---
A beszédfelismerési szolgáltatás egyik fő funkciója az emberi beszéd (más néven beszéd – szöveg) felismerése és átírása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben a kiváló minőségű beszéd-szöveg átalakítás elvégzéséhez.

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a JavaScript gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> javascripthez készült Speech SDK </a>-t. A platformtól függően kövesse az alábbi utasításokat:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webböngésző <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Emellett a cél környezettől függően a következők egyikét használja:

# <a name="script"></a>[parancsfájl](#tab/script)

Töltse le és csomagolja ki a <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -fájlhoz készült Speech SDK-t, és helyezze el a HTML-fájl számára elérhető mappába.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Ha webböngészőt céloz meg, és felhasználja a `<script>` címkét, az `sdk` előtagok nem szükségesek az osztályok hivatkozásakor. Az `sdk` előtag a modul elnevezésére szolgáló alias `require` .

# <a name="require"></a>[igényel](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

További információ `require` : <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Mi a szükséges <span class="docon docon-navigate-external x-hidden-focus"></span> ? </a>.

---

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot. Hozzon létre egy [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) -t a kulcs és a régió használatával. A [kulcsok és régiók keresése](../../../overview.md#find-keys-and-region) lapon találja meg a kulcs-régió párokat.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Néhány más módon is elvégezhető a következők inicializálása [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) :

* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

## <a name="recognize-from-microphone-browser-only"></a>Felismerés mikrofonból (csak böngésző)

Ha az eszköz mikrofonját használva szeretné felismerni a beszédfelismerést, hozzon létre egy `AudioConfig` használatával `fromDefaultMicrophoneInput()` . Ezután inicializálja a t [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) , és adja át a `speechConfig` és a `audioConfig` .

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

Ha egy *adott* hangbeviteli eszközt szeretne használni, meg kell adnia az eszköz azonosítóját a következőben: `AudioConfig` . Ismerje meg [, hogyan kérheti le az eszköz azonosítóját](../../../how-to-select-audio-input-devices.md) a hangbemeneti eszközhöz.

## <a name="recognize-from-file"></a>Felismerés fájlból 

# <a name="browser"></a>[Böngésző](#tab/browser)

Egy böngészőalapú JavaScript-környezetben lévő hangfájlból származó beszéd felismeréséhez használja a `fromWavFileInput()` függvényt a létrehozásához [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) . A függvény `fromWavFileInput()` egy JavaScript- [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) objektumot vár paraméterként.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

Ha Node.js hangfájlból szeretné felismerni a beszédet, a leküldéses adatfolyamot használó alternatív kialakítási mintát kell használni, mivel a JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) -objektum nem használható Node.js futtatókörnyezetben. A következő kód:

* Leküldéses adatfolyamot hoz létre a `createPushStream()`
* A `.wav` fájl megnyitása olvasási adatfolyam létrehozásával, majd a leküldéses adatfolyamba való írásával
* Hangkonfiguráció létrehozása a leküldéses adatfolyam használatával

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

A leküldéses adatfolyamok bemenetként való használata azt feltételezi, hogy a hangadatok egy nyers PCM, például a fejlécek kihagyása.
Az API bizonyos esetekben továbbra is működni fog, ha a fejléc nem lett kihagyva, de a legjobb eredmény érdekében érdemes lehet a fejlécek olvasására kiolvasni a logikát, hogy az a `fs` *hangadatok kezdetekor* induljon el.

---

## <a name="error-handling"></a>Hibakezelés

Az előző példákban egyszerűen lekérheti a felismert szöveget `result.text` , de a hibák és más válaszok kezeléséhez meg kell írnia egy kódot az eredmény kezeléséhez. A következő kód kiértékeli a [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?preserve-view=true&view=azure-node-latest#reason) tulajdonságot, és:

* Kinyomtatja az eredmények felismerésének eredményét: `ResultReason.RecognizedSpeech`
* Ha nincs felismerési egyezés, tájékoztassa a felhasználót: `ResultReason.NoMatch`
* Ha hiba történt, nyomtassa ki a hibaüzenetet: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Folyamatos felismerés

Az előző példákban a single-shot felismerést használjuk, amely egyetlen Kimondás. Az egyetlen kiírás végének meghatározása úgy történik, hogy a csendet figyeli a végén, vagy legfeljebb 15 másodperces hangot dolgoz fel.

Ezzel szemben a folyamatos felismerést akkor kell használni, ha meg szeretné **határozni** , hogy mikor kell leállítani a felismerést. `Recognizing` `Recognized` A felismerési eredmények beszerzéséhez elő kell fizetnie a, a és az `Canceled` eseményekre. Az felismerés leállításához hívja meg a következőt: [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#stopcontinuousrecognitionasync) . Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlon.

Először határozza meg a bemenetet, és inicializálja a [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) következőket:

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Ezután fizessen elő a által elküldett eseményekre [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognizing): A köztes felismerési eredményeket tartalmazó események jelzése.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#recognized): A végső felismerési eredményeket tartalmazó események jelzése (sikeres felismerési kísérletet jelezve).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#sessionstopped): Az elismerési munkamenet végét jelző események jelzése (művelet).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#canceled): A megszakított felismerési eredményeket tartalmazó események jelzése (olyan felismerési kísérletet jelez, amelyet a rendszer az eredmény vagy a közvetlen törlési kérelem miatt megszakított, vagy ha egy átviteli vagy protokollhiba történt).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Az összes beállítása [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?preserve-view=true&view=azure-node-latest#startcontinuousrecognitionasync) után hívja meg az felismerést.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használatakor engedélyezheti a diktálás feldolgozását a megfelelő "diktálás engedélyezése" funkció használatával. Ez a mód azt eredményezi, hogy a beszédfelismerési konfigurációs példány értelmezi a mondatok (például a központozás) szövegének leírását. A "Do You Live in Town kérdőjel" kifejezés például "a városban él?" szöveget fogja értelmezni.

A diktálási mód engedélyezéséhez használja a [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#enabledictation--) metódust a alkalmazásban [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszédfelismerés általános feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban keresse meg [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) , majd adja hozzá ezt a sort közvetlenül alatta.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

A [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest#speechrecognitionlanguage) tulajdonság nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="improve-recognition-accuracy"></a>Az elismerés pontosságának javítása

A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez. A kifejezések listájának megadásával javíthatja a beszédfelismerés pontosságát.

Ha például a "Move to" parancs és a "Ward" lehetséges célpontja látható, akkor hozzáadhat egy "Áthelyezés a kórterembe" bejegyzést. Egy kifejezés hozzáadásával nő a valószínűsége annak, hogy ha a hang felismerése felismerte, hogy az "Áthelyezés a kórterembe" lehetőség fel lesz ismerve a "költözés felé" helyett.

Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer a mondatok listájában szereplő szavak és kifejezések felismerését fokozza, még akkor is, ha a bejegyzések a teljes szöveg közepén jelennek meg. 

> [!IMPORTANT]
> A kifejezések listázása funkció a következő nyelveken érhető el: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, PT-BR, zh-CN

A kifejezések listájának használatához először hozzon létre egy [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) objektumot, majd adjon hozzá konkrét szavakat és kifejezéseket a következővel: [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest#addphrase-string-) .

A [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?preserve-view=true&view=azure-node-latest) következő felismerés vagy a beszédfelismerési szolgáltatás újrakapcsolódása után a módosítások érvénybe lépnek.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezések listáját:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek az elismerés pontosságának növeléséhez

A kifejezések listája csak egyetlen lehetőség az elismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../custom-speech-overview.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)