---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bac2ed447c9055f095e604725591c487378f5091
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399680"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse a Beszéd <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">SDK-cikk <span class="docon docon-navigate-external x-hidden-focus"></span> Beszédfelismerés SDK</a> szakaszának utasításait.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja meg a következő `using` állításokat a *Program.cs* fájl tetején.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Érzékeny adatok és környezeti változók

A cikkben szereplő példa forráskód a bizalmas adatok, például a beszédfelismerési erőforrás-előfizetési kulcs és a régió tárolására szolgáló környezeti változóktól függ. Az `Program` osztály `static readonly string` két értéket tartalmaz, amelyek a gazdagépek környezeti `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`változóiból vannak rendelve, nevezetesen és a . Mindkét mező az osztály hatókörén belül helyezkedik el, így az osztály metódusszerveiben is elérhetővé válik. A környezeti változókkal kapcsolatos további információkért lásd a [környezeti változókat és az alkalmazáskonfigurációt.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
}
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

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfordítás egyik gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet olaszra. A kódban kommunikáljon [`SpeechTranslationConfig`][config] a példán, `SpeechRecognitionLanguage` és hozzárendelve a tulajdonsághoz.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] tulajdonság nyelv-területi formátumú karakterláncot vár. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájának **Területi** oszlopában bármilyen értéket megadhat.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfordítás másik gyakori feladata a célfordítási nyelvek megadása, legalább egy szükséges, de többszörösei támogatottak. A következő kódrészletben mind a francia, mind a német fordítási nyelv célokat.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Minden hívásnál [`AddTargetLanguage`][addlang]új célfordítási nyelv van megadva. Más szóval, ha a beszéd felismerése a forrásnyelvről történik, minden célfordítás elérhető az eredményül kapott fordítási művelet részeként.

## <a name="initialize-a-translation-recognizer"></a>Fordításfelismerő inicializálása

Miután létrehozott egy [`SpeechTranslationConfig`][config]t, a következő lépés [`TranslationRecognizer`][recognizer]a . inicializálása. Amikor inicializál egy, [`TranslationRecognizer`][recognizer]akkor át `translationConfig`kell adnia a . A konfigurációs objektum biztosítja a hitelesítő adatokat, amelyeka beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`TranslationRecognizer`][recognizer] a következőket kell tennie:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`][audioconfig] létre `audioConfig` kell hoznia [`TranslationRecognizer`][recognizer]egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először az alábbi `AudioConfig` hivatkozással fog hivatkozni az objektumra:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . [`AudioConfig`][audioconfig]Ha azonban hívás `FromDefaultMicrophoneInput`helyett létrehoz egy , meg `FromWavFileInput` fogja `filename` hívni és átadni a paramétert.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a beszédfelismerési SDK mikrofonra vagy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszédfordítás előtt történik. Miután az összes objektum inicializálódott, hívja meg a recognize-once függvényt, és kapja meg az eredményt.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

A beszédfelismerés ről további információt [a beszédfelismerés alapjaiban talál.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Fordítások szintetizálása

A sikeres beszédfelismerés és -fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`Translations`][translations] szótárkulcs a célfordítás nyelve, az érték pedig a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszédfelolvasás) szintetizálható.

### <a name="event-based-synthesis"></a>Eseményalapú szintézis

Az `TranslationRecognizer` objektum eseményt `Synthesizing` ad ki. Az esemény többször is aktiválódik, és mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéből. Ha több nyelvre fordít, olvassa el a [kézi szintézis](#manual-synthesis)t. Adja meg a szintézis [`VoiceName`][voicename] hang hozzárendelésével egy, `Synthesizing` és egy eseménykezelő az eseményhez, kap a hang. A következő példa a lefordított hangot *.wav* fájlként menti.

> [!IMPORTANT]
> Az eseményalapú szintézis csak egyetlen fordítással működik, **nem** ad hozzá több célfordítási nyelvet. Ezenkívül a [`VoiceName`][voicename] nyelvnek meg kell egyeznie például a célfordítás nyelvével; `"de"` lehet térkép `"de-DE-Hedda"`.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Kézi szintézis

A [`Translations`][translations] szótár segítségével szintetizálhatja a fordításszövegből származó hangot. Végighaladminden fordításon, és szintetizálja a fordítást. Példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. A következő példa öt nyelvre fordítódik, és minden fordítást egy hangfájllá szintetizálnak a megfelelő neurális nyelven.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

A beszédszintézisről további információt [a beszédszintézis alapjaiban](../../../text-to-speech-basics.md)talál.

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
