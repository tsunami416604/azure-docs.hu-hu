---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 8679d6d4c8ff0a6abdf045187c284ca65f43ee7e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986260"
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

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja meg a parancsfájl tetején a következő `using` állításokat.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Ebben a példában [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) egy előfizetési kulcs és régió használatával hoz létre. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot. A cikk többi részéhez is létrehozhat néhány alapvető sablonkódot, amelyet a különböző testreszabásokhoz módosíthat.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd fájllá való egészizálása

Ezután hozzon [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) létre egy objektumot, amely szövegfelolvasásos átalakításokat és kimeneteket hajt végre hangszórókon, fájlokon vagy más kimeneti adatfolyamokon. Az [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) elfogadja az előző [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) lépésben létrehozott objektum params params- ét, és egy [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) olyan objektumot, amely meghatározza a kimeneti eredmények kezelésének módját.

A kezdéshez `AudioConfig` hozzon létre egy `.wav` automatikusan írja `FromWavFileOutput()` a kimenetet egy fájlba `using` a függvény használatával, és adja meg egy utasítással. Ebben `using` a környezetben egy utasítás automatikusan elidegeníti a nem felügyelt erőforrásokat, és az objektum az ártalmatlanítás után kikerül a hatókörből.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Ezután egy másik `SpeechSynthesizer` `using` kijelentéssel kell megadnom. Adja `config` át az `audioConfig` objektumot és az objektumot params-ként. Ezután a beszédszintézis végrehajtása és a fájlba `SpeakTextAsync()` írás olyan egyszerű, mint egy szövegsorozattal futni.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Futtassa a programot, `.wav` és a program a megadott helyre ír egy szintetizált fájlt. Ez egy jó példa a legalapvetőbb használat, de a következő, ha megnézi a kimenet testreszabása és a kimeneti válasz kezelése, mint egy memórián belüli stream egyéni forgatókönyvek.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszóró kimenetére

Bizonyos esetekben előfordulhat, hogy közvetlenül a szintetizált beszédeket közvetlenül a hangszóróhoz szeretné kiadni. Ehhez egyszerűen hagyja ki `AudioConfig` a param `SpeechSynthesizer` létrehozásakor a fenti példában. Ez az aktuális aktív kimeneti eszközre vált.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beszerezése memórián belüli adatfolyamként

A beszédalkalmazások fejlesztésének számos forgatókönyve esetén valószínűleg az eredményül kapott hangadatokra memóriafolyamként van szükség, nem pedig közvetlenül egy fájlba. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Absztrakt az eredményül kapott bájttömböt az egyéni alsóbb rétegbeli szolgáltatások kereshető adatfolyamaként.
* Integrálja az eredményt más API-kkal vagy szolgáltatásokkal.
* Módosítsa a hangadatokat, írjon egyéni `.wav` fejléceket stb.

Ez a változás egyszerű az előző példához képest. Először távolítsa `AudioConfig` el a blokkot, mivel ettől a ponttól kezdve manuálisan fogja kezelni a kimeneti viselkedést a fokozott vezérlés érdekében. Ezután `null` adja `AudioConfig` át `SpeechSynthesizer` a konstruktora. 

> [!NOTE]
> A `null` `AudioConfig`, ahelyett, hogy kihagyna, mint a fenti hangszóró kimeneti példában, alapértelmezés szerint nem játssza le a hangot az aktuális aktív kimeneti eszközön.

Ezúttal az eredményt egy [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet) változóba menti. A `AudioData` tulajdonság `byte []` a kimeneti adatokat tartalmazza. Ezt `byte []` manuálisan is használhatja, vagy [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) az osztály segítségével kezelheti a memórián belüli adatfolyamot. Ebben a példában `AudioDataStream.FromResult()` a statikus függvény használatával kap egy adatfolyamot az eredményből.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Itt bármilyen egyéni viselkedést megvalósíthat `stream` az eredményül kapott objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, többek között:

* Hangfájl típusa
* Minta-arány
* Bitmélység

A hangformátum módosításához használja `SetSpeechSynthesisOutputFormat()` az `SpeechConfig` objektumon lévő függvényt. Ez a függvény `enum` egy [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)olyan típust vár, amelyet a kimeneti formátum kiválasztásához használ. Az elérhető [hangformátumok listáját](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) a referenciadokumentumokban található.

A követelményektől függően különböző fájltípusokra különböző lehetőségek vonatkoznak. Ne feledje, hogy definíció `Raw24Khz16BitMonoPcm` szerint a nyers formátumok, mint például nem tartalmaznak hangfejléceket. Csak akkor használjon nyers formátumokat, ha tudja, hogy az alsóbb rétegbeli megvalósítás dekódolhat egy nyers bitfolyamot, vagy ha manuálisan tervez fejléceket a bitmélység, a mintavételi sebesség, a csatornák száma stb.

Ebben a példában az `Riff24Khz16BitMonoPcm` `SpeechSynthesisOutputFormat` `SpeechConfig` objektumon lévő beállítással magas minőségű RIFF formátumot adhat meg. Az előző szakaszban található példához [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) hasonlóan az eredmény memórián belüli adatfolyamának leírásával, majd egy fájlba írhatja azt.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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

Ezután módosítania kell a beszédszintézisi kérelmet, hogy hivatkozzon az XML-fájlra. A kérelem többnyire ugyanaz, de `SpeakTextAsync()` a függvény `SpeakSsmlAsync()`használata helyett a . Ez a függvény XML-karakterláncot vár, ezért először az SSML-konfigurációt tölti be karakterláncként a használatával. `File.ReadAllText()` Innen az eredményobjektum pontosan megegyezik az előző példákkal.

> [!NOTE]
> Ha a Visual Studio alkalmazást használja, a buildkonfiguráció valószínűleg nem fogja megtalálni az XML-fájlt. A probléma megoldásához kattintson a jobb gombbal az XML-fájlra, és válassza a **Tulajdonságok parancsot.** Módosítsa **a Build műveletet** *tartalomra*, és módosítsa **a Másolás a kimeneti könyvtárba** *mindig másolásra.*

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
