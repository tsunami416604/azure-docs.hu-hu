---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399581"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse a Beszéd <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">SDK-cikk <span class="docon docon-navigate-external x-hidden-focus"></span> Beszédfelismerés SDK</a> szakaszának utasításait.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja meg a következőket `#include` és `using` a C++ kódfájl tetején található állításokat.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Érzékeny adatok és környezeti változók

A cikkben szereplő példa forráskód a bizalmas adatok, például a beszédfelismerési erőforrás-előfizetési kulcs és a régió tárolására szolgáló környezeti változóktól függ. A C++ kódfájl két karakterlánc-értéket tartalmaz, amelyek a gazdagépek `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`környezeti változóiból vannak rendelve, nevezetesen és a . Mindkét mező az osztály hatókörén belül helyezkedik el, így az osztály metódusszerveiben is elérhetővé válik. A környezeti változókkal kapcsolatos további információkért lásd a [környezeti változókat és az alkalmazáskonfigurációt.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfordítás egyik gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet olaszra. A kódban, kölcsönhatásba [`SpeechTranslationConfig`][config] lépnek `SetSpeechRecognitionLanguage` a példány, hívja meg a metódust.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] tulajdonság nyelv-területi formátumú karakterláncot vár. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájának **Területi** oszlopában bármilyen értéket megadhat.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfordítás másik gyakori feladata a célfordítási nyelvek megadása, legalább egy szükséges, de többszörösei támogatottak. A következő kódrészletben mind a francia, mind a német fordítási nyelv célokat.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Minden hívásnál [`AddTargetLanguage`][addlang]új célfordítási nyelv van megadva. Más szóval, ha a beszéd felismerése a forrásnyelvről történik, minden célfordítás elérhető az eredményül kapott fordítási művelet részeként.

## <a name="initialize-a-translation-recognizer"></a>Fordításfelismerő inicializálása

Miután létrehozott egy [`SpeechTranslationConfig`][config]t, a következő lépés [`TranslationRecognizer`][recognizer]a . inicializálása. Amikor inicializál egy, [`TranslationRecognizer`][recognizer]akkor át `translationConfig`kell adnia a . A konfigurációs objektum biztosítja a hitelesítő adatokat, amelyeka beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`TranslationRecognizer`][recognizer] a következőket kell tennie:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`][audioconfig] létre `audioConfig` kell hoznia [`TranslationRecognizer`][recognizer]egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először az alábbi `AudioConfig` hivatkozással fog hivatkozni az objektumra:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . [`AudioConfig`][audioconfig]Ha azonban hívás `FromDefaultMicrophoneInput`helyett létrehoz egy , meg `FromWavFileInput` fogja `filename` hívni és átadni a paramétert.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a beszédfelismerési SDK mikrofonra vagy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszédfordítás előtt történik. Miután az összes objektum inicializálódott, hívja meg a recognize-once függvényt, és kapja meg az eredményt.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

A beszédfelismerés ről további információt [a beszédfelismerés alapjaiban talál.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Fordítások szintetizálása

A sikeres beszédfelismerés és -fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`Translations`][translations] szótárkulcs a célfordítás nyelve, az érték pedig a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszédfelolvasás) szintetizálható.

### <a name="event-based-synthesis"></a>Eseményalapú szintézis

Az `TranslationRecognizer` objektum eseményt `Synthesizing` ad ki. Az esemény többször is aktiválódik, és mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéből. Ha több nyelvre fordít, olvassa el a [kézi szintézis](#manual-synthesis)t. Adja meg a szintézis [`SetVoiceName`][voicename] hang hozzárendelésével egy, `Synthesizing` és egy eseménykezelő az eseményhez, kap a hang. A következő példa a lefordított hangot *.wav* fájlként menti.

> [!IMPORTANT]
> Az eseményalapú szintézis csak egyetlen fordítással működik, **nem** ad hozzá több célfordítási nyelvet. Ezenkívül a [`SetVoiceName`][voicename] nyelvnek meg kell egyeznie például a célfordítás nyelvével; `"de"` lehet térkép `"de-DE-Hedda"`.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Kézi szintézis

A [`Translations`][translations] szótár segítségével szintetizálhatja a fordításszövegből származó hangot. Végighaladminden fordításon, és szintetizálja a fordítást. Példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. A következő példa öt nyelvre fordítódik, és minden fordítást egy hangfájllá szintetizálnak a megfelelő neurális nyelven.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

A beszédszintézisről további információt [a beszédszintézis alapjaiban](../../../text-to-speech-basics.md)talál.

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
