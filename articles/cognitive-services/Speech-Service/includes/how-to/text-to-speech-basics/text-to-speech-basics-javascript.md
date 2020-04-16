---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399558"
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
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

További információ: `import` <a href="https://javascript.info/import-export" target="_blank">export and <span class="docon docon-navigate-external x-hidden-focus"> </span>import </a>.

# <a name="require"></a>[Igényel](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

További információ: `require` <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[szkriptet.](#tab/script)

Töltse le és bontsa ki a <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* fájlt, és helyezze el a HTML-fájl számára elérhető mappában.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Ha webböngészőt céloz meg, és `<script>` a címkét használja; az `sdk` előtag nem szükséges. Az `sdk` előtag a `require` modul elnevezésére használt alias.

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

Ebben a példában [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) egy előfizetési kulcs és régió használatával hoz létre. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot. A cikk többi részéhez is létrehozhat néhány alapvető sablonkódot, amelyet a különböző testreszabásokhoz módosíthat.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd fájllá való egészizálása

Ezután hozzon [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) létre egy objektumot, amely szövegfelolvasásos átalakításokat és kimeneteket hajt végre hangszórókon, fájlokon vagy más kimeneti adatfolyamokon. Az [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) elfogadja az előző [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) lépésben létrehozott objektum params params- ét, és egy [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) olyan objektumot, amely meghatározza a kimeneti eredmények kezelésének módját.

A kezdéshez `AudioConfig` hozzon létre egy `.wav` olyan fájlt `fromAudioFileOutput()` automatikusan írni egy fájlba a statikus függvény használatával.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Ezután példányosítsa `SpeechSynthesizer` `speechConfig` meg az `audioConfig` objektum ot és az objektumot params-ként. Ezután a beszédszintézis végrehajtása és a fájlba `speakTextAsync()` írás olyan egyszerű, mint egy szövegsorozattal futni. Az eredmény visszahívás egy nagyszerű `synthesizer.close()`hely, hogy hívja , sőt - ez a hívás szükséges ahhoz, hogy szintézis megfelelően működik.

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
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

Futtassa a programot, `.wav` és a program a megadott helyre ír egy szintetizált fájlt. Ez egy jó példa a legalapvetőbb használat, de a következő, ha megnézi a kimenet testreszabása és a kimeneti válasz kezelése, mint egy memórián belüli stream egyéni forgatókönyvek.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszóró kimenetére

Bizonyos esetekben előfordulhat, hogy közvetlenül a szintetizált beszédeket közvetlenül a hangszóróhoz szeretné kiadni. Ehhez példányosítsa meg `AudioConfig` a `fromDefaultSpeakerOutput()` statikus függvény t. Ez az aktuális aktív kimeneti eszközre vált.

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

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beszerezése memórián belüli adatfolyamként

A beszédalkalmazások fejlesztésének számos forgatókönyve esetén valószínűleg az eredményül kapott hangadatokra memóriafolyamként van szükség, nem pedig közvetlenül egy fájlba. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Absztrakt az eredményül kapott bájttömböt az egyéni alsóbb rétegbeli szolgáltatások kereshető adatfolyamaként.
* Integrálja az eredményt más API-kkal vagy szolgáltatásokkal.
* Módosítsa a hangadatokat, írjon egyéni `.wav` fejléceket stb.

Ez a változás egyszerű az előző példához képest. Először távolítsa `AudioConfig` el a blokkot, mivel ettől a ponttól kezdve manuálisan fogja kezelni a kimeneti viselkedést a fokozott vezérlés érdekében. Ezután `undefined` adja `AudioConfig` át `SpeechSynthesizer` a konstruktora. 

> [!NOTE]
> A `undefined` `AudioConfig`, ahelyett, hogy kihagyna, mint a fenti hangszóró kimeneti példában, alapértelmezés szerint nem játssza le a hangot az aktuális aktív kimeneti eszközön.

Ezúttal az eredményt egy [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) változóba menti. A `SpeechSynthesisResult.audioData` tulajdonság `ArrayBuffer` a kimeneti adatokat adja vissza. Ezt `ArrayBuffer` manuálisan is használhatja.

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

Itt bármilyen egyéni viselkedést megvalósíthat `ArrayBuffer` az eredményül kapott objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, többek között:

* Hangfájl típusa
* Minta-arány
* Bitmélység

A hangformátum módosításához használja `speechSynthesisOutputFormat` az `SpeechConfig` objektum tulajdonságát. Ez a tulajdonság `enum` egy [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)olyan típusra számít, amelyet a kimeneti formátum kiválasztásához használ. Az elérhető [hangformátumok listáját](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) a referenciadokumentumokban található.

A követelményektől függően különböző fájltípusokra különböző lehetőségek vonatkoznak. Ne feledje, hogy definíció `Raw24Khz16BitMonoPcm` szerint a nyers formátumok, mint például nem tartalmaznak hangfejléceket. Csak akkor használjon nyers formátumokat, ha tudja, hogy az alsóbb rétegbeli megvalósítás dekódolhat egy nyers bitfolyamot, vagy ha manuálisan tervez fejléceket a bitmélység, a mintavételi sebesség, a csatornák száma stb.

Ebben a példában az `Riff24Khz16BitMonoPcm` `speechSynthesisOutputFormat` `SpeechConfig` objektumon lévő beállítással magas minőségű RIFF formátumot adhat meg. Az előző szakaszban található példához `ArrayBuffer` hasonlóan lekell szereznie a hangadatokat, és kölcsönhatásba kell lépnie velük.

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

Ha ismét futtatja `.wav` a programot, a program a megadott elérési útra ír.

## <a name="use-ssml-to-customize-speech-characteristics"></a>Beszédfelismerési jellemzők testreszabása ssml-rel

A beszédszintetizáló nyelv (SSML) lehetővé teszi a szövegírás-olvasási hangmagasság, kiejtés, beszédarány, hangerő és egyebek nagyobb részének finomhangolását egy XML-sémából érkező kérelmek elküldésével. Ez a szakasz néhány gyakorlati használati példát mutat be, de részletesebb útmutatót az [SSML útmutatójában talál.](../../../speech-synthesis-markup.md)

Az SSML testreszabási használatának megkezdéséhez egy egyszerű módosítást kell elindítania, amely átkapcsolja a hangot.
Először hozzon létre egy új XML-fájlt az SSML konfigurációhoz a gyökérprojekt könyvtárában ebben a példában. `ssml.xml` A gyökérelem `<speak>`mindig , és a `<voice>` szöveg tördelése egy `name` elemben lehetővé teszi a hang megváltoztatását a param használatával. Ez a példa megváltoztatja a hangját egy férfi angol (UK) hangra. Vegye figyelembe, hogy ez a hang egy **szabványos** hang, amely különböző árképzéssel és rendelkezésre állással rendelkezik, mint **a neurális** hangok. Tekintse meg a támogatott **szabványos** hangok [teljes listáját.](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices)

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Ezután módosítania kell a beszédszintézisi kérelmet, hogy hivatkozzon az XML-fájlra. A kérelem többnyire ugyanaz, de `speakTextAsync()` a függvény `speakSsmlAsync()`használata helyett a . Ez a függvény XML-karakterláncot vár, ezért először hozzon létre egy függvényt az XML-fájl betöltéséhez és karakterláncként való visszaküldéséhez.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

További információ `readFileSync`a : <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js<span class="docon docon-navigate-external x-hidden-focus"></span>fájlrendszer</a>. Innen az eredményobjektum pontosan megegyezik az előző példákkal.

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

A kimenet működik, de van néhány egyszerű további változtatást lehet tenni, hogy segítsen hangzik természetesebb. Az általános beszédsebesség egy kicsit túl gyors, `<prosody>` ezért hozzáadunk egy címkét, és csökkentjük a sebességet az alapértelmezett sebesség **90% -ára.** Továbbá, a szünet után a vessző a mondatban egy kicsit túl rövid és természetellenes hangzású. A probléma megoldásához `<break>` adjon hozzá egy címkét a beszéd késleltetéséhez, és állítsa a param időt **200 ms-ra.** Futtassa újra a szintézist, és nézze meg, hogyan befolyásolták ezek a testreszabások a kimenetet.

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

A neurális hangok beszédszintézisi algoritmusok, amelyeket mély neurális hálózatok működtetnek. Neurális hang használata esetén a szintetizált beszéd szinte megkülönböztethetetlen az emberi felvételektől. Az emberszerű természetes prosody és világos artikuláció a szavak, neurális hangok jelentősen csökkenti a hallgatás fáradtság, amikor a felhasználók kölcsönhatásba AI rendszerek.

Ha neurális hangra `name` szeretne váltani, módosítsa az egyik [neurális hangbeállítást](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Ezután vegyen fel egy `mstts`XML-névteret a `<mstts:express-as>` alkalmazáshoz, és tördelje a szöveget a címkében. A `style` param segítségével testreszabhatja a beszédstílust. Ez a `cheerful`példa a , `customerservice` `chat` de próbálja meg beadni, vagy látni a különbséget a beszédstílus.

> [!IMPORTANT]
> Neurális hangok **csak** az *USA keleti régiójában,* *Délkelet-Ázsiában*és *Nyugat-európai* régiókban létrehozott beszédfelismerési erőforrások esetében támogatottak.

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
