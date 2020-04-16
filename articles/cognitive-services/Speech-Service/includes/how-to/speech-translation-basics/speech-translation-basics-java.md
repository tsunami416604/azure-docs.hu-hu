---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 73e6e117428808aae39e361a3b119e9b2af1ac27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399652"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik egy Azure-fiók és a Speech szolgáltatás-előfizetés. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyen a Beszédszolgáltatást.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t. A platformtól függően kövesse a Beszéd <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">SDK-cikk <span class="docon docon-navigate-external x-hidden-focus"></span> Beszédfelismerés SDK</a> szakaszának utasításait.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához a következő `import` utasítások szerepeljenek a **. Java* kód fájlt.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Érzékeny adatok és környezeti változók

A cikkben szereplő példa forráskód a bizalmas adatok, például a beszédfelismerési erőforrás-előfizetési kulcs és a régió tárolására szolgáló környezeti változóktól függ. A Java kódfájl `static final String` két értéket tartalmaz, amelyek a gazdagépek `SPEECH__SUBSCRIPTION__KEY` környezeti `SPEECH__SERVICE__REGION`változóiból vannak hozzárendelve, nevezetesen és . Mindkét mező az osztály hatókörén belül helyezkedik el, így az osztály metódusszerveiben is elérhetővé válik. A környezeti változókkal kapcsolatos további információkért lásd a [környezeti változókat és az alkalmazáskonfigurációt.](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfordítás egyik gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet olaszra. A kódban, kölcsönhatásba [`SpeechTranslationConfig`][config] lépnek `setSpeechRecognitionLanguage` a példány, hívja meg a metódust.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

A [`setSpeechRecognitionLanguage`][recognitionlang] függvény nyelv-területi formátumkarakterláncot vár. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájának **Területi** oszlopában bármilyen értéket megadhat.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfordítás másik gyakori feladata a célfordítási nyelvek megadása, legalább egy szükséges, de többszörösei támogatottak. A következő kódrészletben mind a francia, mind a német fordítási nyelv célokat.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Minden hívásnál [`addTargetLanguage`][addlang]új célfordítási nyelv van megadva. Más szóval, ha a beszéd felismerése a forrásnyelvről történik, minden célfordítás elérhető az eredményül kapott fordítási művelet részeként.

## <a name="initialize-a-translation-recognizer"></a>Fordításfelismerő inicializálása

Miután létrehozott egy [`SpeechTranslationConfig`][config]t, a következő lépés [`TranslationRecognizer`][recognizer]a . inicializálása. Amikor inicializál egy, [`TranslationRecognizer`][recognizer]akkor át `translationConfig`kell adnia a . A konfigurációs objektum biztosítja a hitelesítő adatokat, amelyeka beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`TranslationRecognizer`][recognizer] a következőket kell tennie:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`][audioconfig] létre `audioConfig` kell hoznia [`TranslationRecognizer`][recognizer]egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

Először az alábbi `AudioConfig` hivatkozással fog hivatkozni az objektumra:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audioConfig`kell adnia egy . [`AudioConfig`][audioconfig]Ha azonban hívás `fromDefaultMicrophoneInput`helyett létrehoz egy , meg `fromWavFileInput` fogja `filename` hívni és átadni a paramétert.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a beszédfelismerési SDK mikrofonra vagy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszédfordítás előtt történik. Miután az összes objektum inicializálódott, hívja meg a recognize-once függvényt, és kapja meg az eredményt.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

A beszédfelismerés ről további információt [a beszédfelismerés alapjaiban talál.](../../../speech-to-text-basics.md)

## <a name="synthesize-translations"></a>Fordítások szintetizálása

A sikeres beszédfelismerés és -fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`getTranslations`][translations] függvény egy szótárt ad vissza a kulccsal, mint a cél fordítási nyelv, és az érték a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszédfelolvasás) szintetizálható.

### <a name="event-based-synthesis"></a>Eseményalapú szintézis

Az `TranslationRecognizer` objektum eseményt `synthesizing` ad ki. Az esemény többször is aktiválódik, és mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéből. Ha több nyelvre fordít, olvassa el a [kézi szintézis](#manual-synthesis)t. Adja meg a szintézis [`setVoiceName`][voicename] hang hozzárendelésével egy, `synthesizing` és egy eseménykezelő az eseményhez, kap a hang. A következő példa a lefordított hangot *.wav* fájlként menti.

> [!IMPORTANT]
> Az eseményalapú szintézis csak egyetlen fordítással működik, **nem** ad hozzá több célfordítási nyelvet. Ezenkívül a [`setVoiceName`][voicename] nyelvnek meg kell egyeznie például a célfordítás nyelvével; `"de"` lehet térkép `"de-DE-Hedda"`.

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Kézi szintézis

A [`getTranslations`][translations] függvény egy szótárat ad vissza, amely a fordítási szövegből származó hang szintetizálására használható. Végighaladminden fordításon, és szintetizálja a fordítást. Példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. A következő példa öt nyelvre fordítódik, és minden fordítást egy hangfájllá szintetizálnak a megfelelő neurális nyelven.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

A beszédszintézisről további információt [a beszédszintézis alapjaiban](../../../text-to-speech-basics.md)talál.

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
