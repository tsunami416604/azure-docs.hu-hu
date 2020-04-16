---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 17d8c0157fcd478d01452167d240fb67daeeda5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399618"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse a Beszéd <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">SDK-cikk <span class="docon docon-navigate-external x-hidden-focus"></span> Beszédfelismerés SDK</a> szakaszának utasításait.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja meg a következő `import` állításokat a python kódfájl tetején.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Érzékeny adatok és környezeti változók

A cikkben szereplő példa forráskód a bizalmas adatok, például a beszédfelismerési erőforrás-előfizetési kulcs és a régió tárolására szolgáló környezeti változóktól függ. A python kódfájl két értéket tartalmaz, amelyek a gazdagépek `SPEECH__SUBSCRIPTION__KEY` környezeti `SPEECH__SERVICE__REGION`változóiból vannak hozzárendelve, nevezetesen és . Mindkét változó a globális hatókörhöz tartozik, így a kódfájl függvénydefiníciójában is elérhetők. A környezeti változókkal kapcsolatos további információkért lásd a [környezeti változókat és az alkalmazáskonfigurációt.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Beszédfordítási konfiguráció létrehozása

A beszédfelismerési szolgáltatás hívásához létre kell hoznia [`SpeechTranslationConfig`][config]egy . Ez az osztály az előfizetéssel kapcsolatos információkat is tartalmaz, például a kulcsot és a kapcsolódó régiót, végpontot, állomást vagy engedélyezési jogkivonatot.

> [!TIP]
> Függetlenül attól, hogy beszédfelismerést, beszédszintetizálást, fordítást vagy szándékfelismerést végez, mindig létrehoz egy konfigurációt.

Többféleképpen is inicializálhatja a következőket: [`SpeechTranslationConfig`][config]

* Előfizetéssel: adja át a kulcsot és a társított régióban.
* Végpont: adja át a beszédszolgáltatás végpont. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Állomással: adja át az állomáscímet. A kulcs vagy engedélyezési jogkivonat nem kötelező.
* Egy engedélyezési jogkivonattal: adja át egy engedélyezési jogkivonatot és a társított régiót.

Vessünk egy pillantást arra, [`SpeechTranslationConfig`][config] hogyan jön létre az a egy kulcs és régió használatával. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfordítás egyik gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet olaszra. A kódban kommunikáljon [`SpeechTranslationConfig`][config] a példán, `speech_recognition_language` és hozzárendelve a tulajdonsághoz.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

A [`speech_recognition_language`][recognitionlang] tulajdonság nyelv-területi formátumú karakterláncot vár. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájának **Területi** oszlopában bármilyen értéket megadhat.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfordítás másik gyakori feladata a célfordítási nyelvek megadása, legalább egy szükséges, de többszörösei támogatottak. A következő kódrészletben mind a francia, mind a német fordítási nyelv célokat.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Minden hívásnál [`add_target_language`][addlang]új célfordítási nyelv van megadva. Más szóval, ha a beszéd felismerése a forrásnyelvről történik, minden célfordítás elérhető az eredményül kapott fordítási művelet részeként.

## <a name="initialize-a-translation-recognizer"></a>Fordításfelismerő inicializálása

Miután létrehozott egy [`SpeechTranslationConfig`][config]t, a következő lépés [`TranslationRecognizer`][recognizer]a . inicializálása. Amikor inicializál egy, [`TranslationRecognizer`][recognizer]akkor át `translation_config`kell adnia a . A konfigurációs objektum biztosítja a hitelesítő adatokat, amelyeka beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`TranslationRecognizer`][recognizer] a következőket kell tennie:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`][audioconfig] létre `audio_config` kell hoznia [`TranslationRecognizer`][recognizer]egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először az alábbi `AudioConfig` hivatkozással fog hivatkozni az objektumra:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . Ha azonban a [`AudioConfig`][audioconfig]használatával való hívás `use_default_microphone=True`helyett létrehoz egy `filename="path-to-file.wav"` , `filename` akkor a használatával fog hívást, és meg kell adnia a paramétert.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a beszédfelismerési SDK mikrofonra vagy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszédfordítás előtt történik. Miután az összes objektum inicializálódott, hívja meg a recognize-once függvényt, és kapja meg az eredményt.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

A beszédfelismerés ről további információt [a beszédfelismerés alapjaiban talál.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Fordítások szintetizálása

A sikeres beszédfelismerés és -fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`translations`][translations] szótárkulcs a célfordítás nyelve, az érték pedig a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszédfelolvasás) szintetizálható.

### <a name="event-based-synthesis"></a>Eseményalapú szintézis

Az `TranslationRecognizer` objektum eseményt `Synthesizing` ad ki. Az esemény többször is aktiválódik, és mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéből. Ha több nyelvre fordít, olvassa el a [kézi szintézis](#manual-synthesis)t. Adja meg a szintézis [`voice_name`][voicename] hang hozzárendelésével egy, `Synthesizing` és egy eseménykezelő az eseményhez, kap a hang. A következő példa a lefordított hangot *.wav* fájlként menti.

> [!IMPORTANT]
> Az eseményalapú szintézis csak egyetlen fordítással működik, **nem** ad hozzá több célfordítási nyelvet. Ezenkívül a [`voice_name`][voicename] nyelvnek meg kell egyeznie például a célfordítás nyelvével; `"de"` lehet térkép `"de-DE-Hedda"`.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Kézi szintézis

A [`translations`][translations] szótár segítségével szintetizálhatja a fordításszövegből származó hangot. Végighaladminden fordításon, és szintetizálja a fordítást. Példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`speech_synthesis_voice_name`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. A következő példa öt nyelvre fordítódik, és minden fordítást egy hangfájllá szintetizálnak a megfelelő neurális nyelven.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

A beszédszintézisről további információt [a beszédszintézis alapjaiban](../../../text-to-speech-basics.md)talál.

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
