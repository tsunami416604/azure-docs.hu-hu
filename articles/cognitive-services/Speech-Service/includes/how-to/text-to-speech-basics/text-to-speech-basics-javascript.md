---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 931fcd113f883f126c2f94e6f3d06684b9a0e772
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407632"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> javascripthez készült Speech SDK </a>-t. A platformtól függően kövesse az alábbi utasításokat:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webböngésző<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Emellett a cél környezettől függően a következők egyikét használja:

# <a name="script"></a>[parancsfájl](#tab/script)

Töltse le és csomagolja ki a <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span> </a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -fájlhoz készült Speech SDK-t, és helyezze el a HTML-fájl számára elérhető mappába.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Ha webböngészőt céloz meg, és a `<script>` címkét használja, az `sdk` előtag nem szükséges. Az `sdk` előtag a modul elnevezésére szolgáló alias `require` .

# <a name="import"></a>[importálása](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

További információ `import` : <a href="https://javascript.info/import-export" target="_blank">Exportálás és <span class="docon docon-navigate-external x-hidden-focus"></span> Importálás </a>.

# <a name="require"></a>[igényel](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

További információ `require` : <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Mi a szükséges <span class="docon docon-navigate-external x-hidden-focus"></span> ? </a>.

---


## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

Az alábbiakat többféleképpen lehet inicializálni [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Ebben a példában egy [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) előfizetési kulcsot és egy régiót hoz létre. A régió azonosítójának megkereséséhez tekintse meg a [régiók támogatása](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) lapot. Emellett a cikk további részében is létrehozhat egy alapszintű, a különböző testreszabási beállításokkal módosítható egyszerű kiírási kódot.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd készítése fájlba

Ezután létrehoz egy [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) objektumot, amely szöveg-beszéd átalakításokat és kimeneteket hajt végre a hangszórók, fájlok vagy más kimeneti adatfolyamok számára. Az [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) elfogadva paraméterként az [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) előző lépésben létrehozott objektumot, valamint egy objektumot, [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) amely meghatározza a kimeneti eredmények kezelését.

Az indításhoz hozzon létre egy `AudioConfig` fájlt, amely automatikusan a kimenetet egy fájlba írja a `.wav` `fromAudioFileOutput()` statikus függvény használatával.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Ezután hozza létre az `SpeechSynthesizer` `speechConfig` objektumot és az `audioConfig` objektumot paraméterként. Ezután a beszédfelismerés végrehajtása és a fájlba való írás olyan egyszerű, mintha `speakTextAsync()` egy szöveges karakterláncot futtasson. Az eredmény visszahívása nagyszerű hely a híváshoz `synthesizer.close()` , valójában ez a hívás szükséges ahhoz, hogy a szintézis megfelelően működjön.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Futtassa a programot, és a rendszer a `.wav` megadott helyre írja a szintetizált fájlt. Ez jó példa a legalapvetőbb használatra, de a következő lépés a kimenet testreszabása és a kimeneti válasz kezelése memóriában tárolt adatfolyamként az egyéni forgatókönyvek használata esetén.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszórók kimenetére

Bizonyos esetekben érdemes közvetlenül a szintetizált beszédet közvetlenül a beszélőhöz adni. Ehhez hozza létre a `AudioConfig` `fromDefaultSpeakerOutput()` statikus függvény használatával. Ez a kimenet az aktuális aktív kimeneti eszközre mutat.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beolvasása memóriabeli adatfolyamként

A beszédfelismerés számos forgatókönyve esetében valószínű, hogy az eredményül kapott hangadatokat memórián belüli adatfolyamként kell megadnia, nem pedig közvetlenül egy fájlba írni. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Az eredményül kapott bájtos tömböt az egyéni alsóbb rétegbeli szolgáltatásokhoz tartozó, keresési lehetőségként szolgáló adatfolyamként elvonta
* Az eredmény integrálása más API-k vagy szolgáltatások használatával.
* A hangadatok módosítása, egyéni `.wav` fejlécek írása stb.

Ezt a változást egyszerűen elvégezheti az előző példából. Először távolítsa el a `AudioConfig` blokkot, mivel a kimeneti viselkedést ettől kezdve manuálisan fogja kezelni a jobb szabályozás érdekében. Ezután adja át a-t a `undefined` `AudioConfig` `SpeechSynthesizer` konstruktorban. 

> [!NOTE]
> `undefined`A ( `AudioConfig` z) helyett, ahelyett, hogy kihagyja, mint a fenti hangsugárzó-kimeneti példában, a nem játssza le alapértelmezés szerint a hangot a jelenlegi aktív kimeneti eszközön.

Ezúttal egy változóba menti az eredményt [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) . A `SpeechSynthesisResult.audioData` tulajdonság a `ArrayBuffer` kimeneti adatokat adja vissza. Ezt manuálisan is használhatja `ArrayBuffer` .

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Innen bármilyen egyéni viselkedést alkalmazhat az eredményül kapott `ArrayBuffer` objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, beleértve a következőket:

* Hangfájl típusa
* Mintavételezési arány
* Kis mélység

A hangformátum módosításához használja az `speechSynthesisOutputFormat` objektum tulajdonságát `SpeechConfig` . Ez a tulajdonság egy `enum` típust vár [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) , amelyet a kimeneti formátum kiválasztásához használ. A rendelkezésre álló [hangformátumok listáját](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) az útmutató dokumentációjában tekintheti meg.

A követelményektől függően különböző fájltípusok választhatók. Vegye figyelembe, hogy definíció szerint a nyers formátumok, például a nem `Raw24Khz16BitMonoPcm` tartalmaznak hangfejléceket. A nyers formátumok csak akkor használhatók, ha tudja, hogy az alsóbb rétegbeli implementáció dekódolást végez a nyers Bitstream, vagy ha manuálisan kívánja felépíteni a fejléceket a kis mélység, a mintavételezési arány, a csatornák száma stb. alapján.

Ebben a példában egy magas hűségű RIFF formátumot kell megadnia az `Riff24Khz16BitMonoPcm` objektumra vonatkozó beállítással `speechSynthesisOutputFormat` `SpeechConfig` . Az előző szakaszban szereplő példához hasonlóan a hangadatok beolvasása `ArrayBuffer` és a velük folytatott kommunikáció.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

A program ismételt futtatásakor a rendszer egy `.wav` fájlt ír a megadott elérési útra.

## <a name="use-ssml-to-customize-speech-characteristics"></a>A SSML használata a beszédfelismerési jellemzők testreszabásához

A Speech szintézis Markup Language (SSML) lehetővé teszi, hogy a kérések XML-sémából való elküldésével részletesen beállítsa a szöveg-beszéd kimenetet, a kiejtést, a beszéd sebességét, a kötetet és a többit. Ez a szakasz néhány hasznos példát mutat be, de részletesebb útmutatásért tekintse meg a [SSML ismertető cikket](../../../speech-synthesis-markup.md).

A SSML testreszabáshoz való használatának megkezdéséhez egy egyszerű módosítást hajt végre, amely átváltja a hangot.
Először hozzon létre egy új XML-fájlt a SSML config-hoz a legfelső szintű Project-címtárban, ebben a példában `ssml.xml` . A gyökérelem mindig `<speak>` , és egy elem szövegének körbefuttatása `<voice>` lehetővé teszi a hang módosítását a `name` param használatával. Ez a példa egy férfi angol (Egyesült Királysági) hangra vált. Vegye figyelembe, hogy ez a hang egy **szabványos** hang, amely különböző díjszabással és rendelkezésre állással rendelkezik, mint a **neurális** hangok. Tekintse meg a támogatott **standard** hangok [teljes listáját](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) .

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Ezután módosítania kell a beszédfelismerési kérést az XML-fájlra való hivatkozáshoz. A kérelem többnyire azonos, de a függvény használata helyett használja a parancsot `speakTextAsync()` `speakSsmlAsync()` . Ez a függvény egy XML-karakterláncot vár, ezért először létre kell hoznia egy függvényt egy XML-fájl betöltéséhez, és karakterláncként kell visszaadnia.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

További információ a szolgáltatásról `readFileSync` : <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js <span class="docon docon-navigate-external x-hidden-focus"></span> fájlrendszer</a>. Innen az eredmény objektum pontosan megegyezik az előző példákkal.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

A kimenet működik, de van néhány egyszerű további módosítás is, amely segít a természetesebb hangzásban. A teljes beszélő sebesség egy kicsit túl gyors, ezért felvesszük a `<prosody>` címkét, és az alapértelmezett sebesség **90%-ában** csökkentik a sebességet. Emellett a mondatban található vessző utáni szünet egy kicsit túl rövid és természetellenes hang. A probléma megoldásához vegyen fel egy `<break>` címkét a beszéd késleltetéséhez, és állítsa be az időparamétert a **200ms**értékre. Futtassa újra a szintézist, hogy megtekintse, hogy a testreszabások hogyan érintik a kimenetet.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neurális hangok

A neurális hangok a mély neurális hálózatok által működtetett hangszintézisi algoritmusok. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

Ha egy neurális hangra szeretne váltani, módosítsa a `name` beállítást az egyik [neurális hanglehetőségre](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Ezután adjon hozzá egy XML-névteret a `mstts` elemhez, és szúrja be a szöveget a `<mstts:express-as>` címkébe. A `style` paraméter használatával testreszabhatja a beszéd stílusát. Ez a példa a következőt használja `cheerful` , de megpróbáljuk beállítani a vagy a értékre, `customerservice` `chat` hogy megtekintse a beszéd stílusának különbségét.

> [!IMPORTANT]
> A neurális hangokat **csak** az *USA keleti*régiójában, *Dél-Kelet-Ázsia*és *Nyugat-Európában* létrehozott beszédfelismerési erőforrások támogatják.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
