---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: c5d954cc2bdda0b1fcb67801fa948e1f56fb0364
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986239"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához `using` adja meg a következő importálási és utasítások a parancsfájl tetején.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Ebben a példában [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) egy előfizetési kulcs és régió használatával hoz létre. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot. A cikk többi részéhez is létrehozhat néhány alapvető sablonkódot, amelyet a különböző testreszabásokhoz módosíthat.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}
    
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd fájllá való egészizálása

Ezután hozzon [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) létre egy objektumot, amely szövegfelolvasásos átalakításokat és kimeneteket hajt végre hangszórókon, fájlokon vagy más kimeneti adatfolyamokon. Az [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) elfogadja az előző [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) lépésben létrehozott objektum params params- ét, és egy [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) olyan objektumot, amely meghatározza a kimeneti eredmények kezelésének módját.

A kezdéshez `AudioConfig` hozzon létre egy `.wav` automatikusan írja `FromWavFileOutput()` a kimenetet egy fájlba a függvény használatával.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Ezután példányosítson meg egy `SpeechSynthesizer`, az `config` objektumot és az `audioConfig` objektumot params-ként. Ezután a beszédszintézis végrehajtása és a fájlba `SpeakTextAsync()` írás olyan egyszerű, mint egy szövegsorozattal futni.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Futtassa a programot, `.wav` és a program a megadott helyre ír egy szintetizált fájlt. Ez egy jó példa a legalapvetőbb használat, de a következő, ha megnézi a kimenet testreszabása és a kimeneti válasz kezelése, mint egy memórián belüli stream egyéni forgatókönyvek.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszóró kimenetére

Bizonyos esetekben előfordulhat, hogy közvetlenül a szintetizált beszédeket közvetlenül a hangszóróhoz szeretné kiadni. Ehhez egyszerűen hagyja ki `AudioConfig` a param `SpeechSynthesizer` létrehozásakor a fenti példában. Ez az aktuális aktív kimeneti eszközre vált.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beszerezése memórián belüli adatfolyamként

A beszédalkalmazások fejlesztésének számos forgatókönyve esetén valószínűleg az eredményül kapott hangadatokra memóriafolyamként van szükség, nem pedig közvetlenül egy fájlba. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Absztrakt az eredményül kapott bájttömböt az egyéni alsóbb rétegbeli szolgáltatások kereshető adatfolyamaként.
* Integrálja az eredményt más API-kkal vagy szolgáltatásokkal.
* Módosítsa a hangadatokat, írjon egyéni `.wav` fejléceket stb.

Ez a változás egyszerű az előző példához képest. Először távolítsa `AudioConfig`el a , ahogy kezeli a kimeneti viselkedés manuálisan ettől a ponttól kezdve a fokozott ellenőrzés. Ezután `NULL` adja `AudioConfig` át `SpeechSynthesizer` a konstruktora. 

> [!NOTE]
> A `NULL` `AudioConfig`, ahelyett, hogy kihagyna, mint a fenti hangszóró kimeneti példában, alapértelmezés szerint nem játssza le a hangot az aktuális aktív kimeneti eszközön.

Ezúttal az eredményt egy [`SpeechSynthesisResult`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesisresult) változóba menti. A `GetAudioData` getter `byte []` a kimeneti adatok at ad vissza. Ezt `byte []` manuálisan is használhatja, vagy [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) az osztály segítségével kezelheti a memórián belüli adatfolyamot. Ebben a példában `AudioDataStream.FromResult()` a statikus függvény használatával kap egy adatfolyamot az eredményből.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

Itt bármilyen egyéni viselkedést megvalósíthat `stream` az eredményül kapott objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, többek között:

* Hangfájl típusa
* Minta-arány
* Bitmélység

A hangformátum módosításához használja `SetSpeechSynthesisOutputFormat()` az `SpeechConfig` objektumon lévő függvényt. Ez a függvény `enum` egy [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)olyan típust vár, amelyet a kimeneti formátum kiválasztásához használ. Az elérhető [hangformátumok listáját](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) a referenciadokumentumokban található.

A követelményektől függően különböző fájltípusokra különböző lehetőségek vonatkoznak. Ne feledje, hogy definíció `Raw24Khz16BitMonoPcm` szerint a nyers formátumok, mint például nem tartalmaznak hangfejléceket. Csak akkor használjon nyers formátumokat, ha tudja, hogy az alsóbb rétegbeli megvalósítás dekódolhat egy nyers bitfolyamot, vagy ha manuálisan tervez fejléceket a bitmélység, a mintavételi sebesség, a csatornák száma stb.

Ebben a példában az `Riff24Khz16BitMonoPcm` `SpeechSynthesisOutputFormat` `SpeechConfig` objektumon lévő beállítással magas minőségű RIFF formátumot adhat meg. Az előző szakaszban található példához [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) hasonlóan az eredmény memórián belüli adatfolyamának leírásával, majd egy fájlba írhatja azt.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
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

Ezután módosítania kell a beszédszintézisi kérelmet, hogy hivatkozzon az XML-fájlra. A kérelem többnyire ugyanaz, de `SpeakTextAsync()` a függvény `SpeakSsmlAsync()`használata helyett a . Ez a függvény XML-karakterláncot vár, ezért először karakterláncként tölti be az SSML-konfigurációt. Innen az eredményobjektum pontosan megegyezik az előző példákkal.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
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
