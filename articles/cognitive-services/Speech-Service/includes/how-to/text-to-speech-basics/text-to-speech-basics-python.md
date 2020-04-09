---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986253"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t.

```Python
pip install azure-cognitiveservices-speech
```

Ha macOS rendszert futtat, és telepítési problémákba ütközik, előfordulhat, hogy először futtatnia kell ezt a parancsot.

```Python
python3 -m pip install --upgrade pip
```

A beszédbeszéd SDK telepítése után a parancsfájl tetején a következő importálási utasítások szerepeljenek.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Beszédkonfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Ebben a példában [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) egy előfizetési kulcs és régió használatával hoz létre. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd fájllá való egészizálása

Ezután hozzon [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) létre egy objektumot, amely szövegfelolvasásos átalakításokat és kimeneteket hajt végre hangszórókon, fájlokon vagy más kimeneti adatfolyamokon. Az [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) elfogadja az előző [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) lépésben létrehozott objektum params params- ét, és egy [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) olyan objektumot, amely meghatározza a kimeneti eredmények kezelésének módját.

A kezdéshez `AudioOutputConfig` hozzon létre egy `.wav` automatikusan írja `filename` a kimenetet egy fájlba a konstruktor param használatával.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Ezután példányosítsa meg az `SpeechSynthesizer` a-t úgy, hogy az `speech_config` objektumot és az `audio_config` objektumot params-ként adja át. Ezután a beszédszintézis végrehajtása és a fájlba `speak_text_async()` írás olyan egyszerű, mint egy szövegsorozattal futni.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Futtassa a programot, `.wav` és a program a megadott helyre ír egy szintetizált fájlt. Ez egy jó példa a legalapvetőbb használat, de a következő, ha megnézi a kimenet testreszabása és a kimeneti válasz kezelése, mint egy memórián belüli stream egyéni forgatókönyvek.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszóró kimenetére

Bizonyos esetekben előfordulhat, hogy közvetlenül a szintetizált beszédeket közvetlenül a hangszóróhoz szeretné kiadni. Ehhez használja az előző szakaszban található példát, de módosítsa a `AudioOutputConfig` param eltávolításával, és állítsa be a `filename` . `use_default_speaker=True` Ez az aktuális aktív kimeneti eszközre vált.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beszerezése memórián belüli adatfolyamként

A beszédalkalmazások fejlesztésének számos forgatókönyve esetén valószínűleg az eredményül kapott hangadatokra memóriafolyamként van szükség, nem pedig közvetlenül egy fájlba. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Absztrakt az eredményül kapott bájttömböt az egyéni alsóbb rétegbeli szolgáltatások kereshető adatfolyamaként.
* Integrálja az eredményt más API-kkal vagy szolgáltatásokkal.
* Módosítsa a hangadatokat, írjon egyéni `.wav` fejléceket stb.

Ez a változás egyszerű az előző példához képest. Először távolítsa `AudioConfig`el a , ahogy kezeli a kimeneti viselkedés manuálisan ettől a ponttól kezdve a fokozott ellenőrzés. Ezután `None` adja `AudioConfig` át `SpeechSynthesizer` a konstruktora. 

> [!NOTE]
> A `None` `AudioConfig`, ahelyett, hogy kihagyna, mint a fenti hangszóró kimeneti példában, alapértelmezés szerint nem játssza le a hangot az aktuális aktív kimeneti eszközön.

Ezúttal az eredményt egy [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python) változóba menti. A `audio_data` tulajdonság `bytes` a kimeneti adatok egy objektumát tartalmazza. Ezt az objektumot manuálisan is használhatja, vagy az [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) osztály segítségével kezelheti a memórián belüli adatfolyamot. Ebben a példában `AudioDataStream` a konstruktor segítségével kap egy patak az eredményből.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Itt bármilyen egyéni viselkedést megvalósíthat `stream` az eredményül kapott objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, többek között:

* Hangfájl típusa
* Minta-arány
* Bitmélység

A hangformátum módosításához használja `set_speech_synthesis_output_format()` az `SpeechConfig` objektumon lévő függvényt. Ez a függvény `enum` egy [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)olyan típust vár, amelyet a kimeneti formátum kiválasztásához használ. Az elérhető [hangformátumok listáját](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python) a referenciadokumentumokban található.

A követelményektől függően különböző fájltípusokra különböző lehetőségek vonatkoznak. Ne feledje, hogy definíció `Raw24Khz16BitMonoPcm` szerint a nyers formátumok, mint például nem tartalmaznak hangfejléceket. Csak akkor használjon nyers formátumokat, ha tudja, hogy az alsóbb rétegbeli megvalósítás dekódolhat egy nyers bitfolyamot, vagy ha manuálisan tervez fejléceket a bitmélység, a mintavételi sebesség, a csatornák száma stb.

Ebben a példában az `Riff24Khz16BitMonoPcm` `SpeechSynthesisOutputFormat` `SpeechConfig` objektumon lévő beállítással magas minőségű RIFF formátumot adhat meg. Az előző szakaszban található példához [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) hasonlóan az eredmény memórián belüli adatfolyamának leírásával, majd egy fájlba írhatja azt.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Ha ismét futtatja a `.wav` programot, a program testreszabott fájlt ír a megadott elérési útra.

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

Ezután módosítania kell a beszédszintézisi kérelmet, hogy hivatkozzon az XML-fájlra. A kérelem többnyire ugyanaz, de `speak_text_async()` a függvény `speak_ssml_async()`használata helyett a . Ez a függvény XML-karakterláncot vár, ezért először karakterláncként olvashatja el az SSML-konfigurációt. Innen az eredményobjektum pontosan megegyezik az előző példákkal.

> [!NOTE]
> Ha `ssml_string` a `ï»¿` karakterlánc elején található, le kell vetkőztetnie az anyagjegyzék-formátumot, különben a szolgáltatás hibát ad vissza. Ezt úgy hajthatja `encoding` végre, hogy `open("ssml.xml", "r", encoding="utf-8-sig")`a paramétert a következőképpen állítja be: .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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
