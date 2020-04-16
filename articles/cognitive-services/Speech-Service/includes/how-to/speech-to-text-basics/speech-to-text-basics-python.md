---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 1c58d285e77ace20531912ec150a9d44acde61b8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399775"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a következőket feltételezi:

* Rendelkezik egy Azure-fiókkal és beszédszolgáltatás-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel – [próbálja ki ingyen a Beszéd szolgáltatást.](../../../get-started.md)

## <a name="install-and-import-the-speech-sdk"></a>A beszédfelismerési SDK telepítése és importálása

Mielőtt bármit is tehetne, telepítenie kell a beszédfelismerési SDK-t.

```Python
pip install azure-cognitiveservices-speech
```

Ha macOS rendszert futtat, és telepítési problémákba ütközik, előfordulhat, hogy először futtatnia kell ezt a parancsot.

```Python
python3 -m pip install --upgrade pip
```

A beszédbeszéd SDK telepítése után importálja azt a Python-projektbe ezzel a kijelentéssel.

```Python
import azure.cognitiveservices.speech as speechsdk
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

Vessünk egy pillantást arra, [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) hogyan jön létre az a egy kulcs és régió használatával. A [régióazonosító](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) megkereséséhez tekintse meg a régiótámogatási lapot.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Felismerő inicializálása

Miután létrehozott egy [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)t, a következő lépés [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)a . inicializálása. Amikor inicializál egy, [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)akkor át `speech_config`kell adnia a . Ez biztosítja a hitelesítő adatokat, amelyek a beszédszolgáltatás érvényesítéséhez szükséges.

Ha az eszköz alapértelmezett mikrofonjával ismeri fel a beszédet, [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) a következőket kell tennie:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Ha meg szeretné adni a hangbemeneti eszközt, akkor [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) létre `audio_config` kell hoznia [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)egy és meg kell adnia a paramétert a inicializálásakor.

> [!TIP]
> [További információ arról, hogyan szerezheti be a hangbemeneti eszköz eszközazonosítóját.](../../../how-to-select-audio-input-devices.md)

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor is meg `audio_config`kell adnia egy . Ha azonban létrehoz [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python)egy , ahelyett, `device_name`hogy a, `filename` akkor használja a paramétert.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Beszéd felismerése

A Speech SDK python-hoz tartozó [Recognizer osztály](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) néhány olyan módszert tár fel, amelyeket a beszédfelismeréshez használhat.

* Egylövéses felismerés (szinkronizálás) – A felismerést blokkoló (szinkron) módban hajtja végre. Egyetlen utterance (kifejezés) felismerése után adja vissza. Az egyetlen utterance (kifejezés) végét a csend figyelése határozza meg a végén, vagy amíg legfeljebb 15 másodpercnyi hang feldolgozása meg nem történik. A tevékenység eredményként adja vissza a felismerés szövegét.
* Egylövéses felismerés (aszinkron) – Nem blokkoló (aszinkron) módban végzi a felismerést. Ez felismeri az egyetlen utterance (kifejezés). Az egyetlen utterance (kifejezés) végét a csend figyelése határozza meg a végén, vagy amíg legfeljebb 15 másodpercnyi hang feldolgozása meg nem történik.
* Folyamatos felismerés (szinkronizálás) – Szinkron módon folyamatos felismerést kezdeményez. Az ügyfélnek `EventSignal` csatlakoznia kell a felismerési eredmények fogadásához. A felismerés leállításához hívjuk a [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Folyamatos felismerés (aszinkron) – Aszinkron módon folyamatos felismerési műveletet kezdeményez. A felhasználónak csatlakoznia kell az EventSignal-hoz, hogy megkapja a felismeréseredményeit. Az aszinkron folyamatos felismerés leállításához hívja meg [a stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--).

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról.](../../../how-to-choose-recognition-mode.md)

### <a name="single-shot-recognition"></a>Egylövetű felismerés

Íme egy példa a szinkron egylövéses felismerésre a következők használatával: [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once)

```Python
result = speech_recognizer.recognize_once()
```

Íme egy példa a szinkron egylövéses felismerésre a következők használatával: [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)

```Python
result = speech_recognizer.recognize_once_async()
```

Függetlenül attól, hogy a szinkron vagy aszinkron módszert használta, az eredményen keresztül valamilyen kódot kell írnia. Ez a minta a [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)következőket értékeli:

* Kinyomtatja a felismerés eredményét:`speechsdk.ResultReason.RecognizedSpeech`
* Ha nincs egyezés a felismeréssel, értesítse a felhasználót:`speechsdk.ResultReason.NoMatch `
* Ha hiba történik, nyomtassa ki a hibaüzenetet:`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Folyamatos felismerés

Folyamatos felismerés egy kicsit több szó, mint egy-shot felismerés. Ez megköveteli, hogy `EventSignal` csatlakozzon a, hogy a felismerés eredményeit, és a stop felismerés, meg kell hívni [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) vagy [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--). Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlban.

Kezdjük a bemenet meghatározásával és a [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)következő inicializálásával:

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Ezután hozzunk létre egy változót a beszédfelismerés állapotának kezeléséhez. Először is ezt úgy `False`állítjuk be, hogy az elismerés kezdetén nyugodtan feltételezhetjük, hogy még nincs kész.

```Python
done = False
```

Most létrehozunk egy visszahívást, hogy leállítsuk `evt` a folyamatos felismerést, amikor egy fogadás érkezik. Van néhány dolog, amit észben kell tartanod.

* Amikor `evt` egy fogadásérkezik, a rendszer kinyomtatja az `evt` üzenetet.
* A `evt` fogadásután [a stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) a felismerés leállítására van hivatott.
* A felismerési állapot `True`a ikonra változik.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Ez a kódminta bemutatja, hogyan lehet [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)visszahívásokat csatlakoztatni a rendszerből küldött eseményekhez.

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Jelzés a közbenső felismerési eredményeket tartalmazó eseményekre.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Jelzés a végső felismerési eredményeket tartalmazó eseményekre (sikeres felismerési kísérlet jelzése).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): A felismerési munkamenet (művelet) kezdetét jelző események jelzése.
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): A felismerési munkamenet (művelet) végét jelző események jelzése.
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Jelzés a visszavont felismerési eredményeket tartalmazó eseményekhez (olyan felismerési kísérlet jelzése, amelyet ennek eredményeként vagy közvetlen törlési kérelem, vagy átvitel- vagy protokollhiba miatt töröltek).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Minden létre, hívhatjuk [start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használataesetén engedélyezheti a diktálás feldolgozását a megfelelő "Diktálás engedélyezése" funkcióval. Ezzel a móddal a beszédfelismerési konfigurációs példány értelmezi a mondatstruktúrák, például az írásjelek szóleírását. Például a "A városban él" kimondott szöveget a "A városban él?" szövegként értelmezi.

A diktálási mód engedélyezéséhez használja a [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) módszerét a on. [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Forrásnyelv módosítása

A beszédfelismerés gyakori feladata a bemeneti (vagy forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtatná meg a beviteli nyelvet németre. A kódban keresse meg a SpeechConfig-ot, majd adja hozzá ezt a sort közvetlenül alatta.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language)egy olyan paraméter, amely egy karakterláncot argumentumként vesz fel. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>A felismerés pontosságának javítása

A beszédfelismerési SDK segítségével többféleképpen is javíthatja a felismerés pontosságát. Vessünk egy pillantást a kifejezéslistákra. A Kifejezéslisták kifejezéssel azonosítják a hangadatokban szereplő ismert kifejezéseket, például egy személy nevét vagy egy adott helyet. A kifejezéslistához egyetlen szó vagy teljes kifejezés adható. Az elismerés során a kifejezéslista egy bejegyzését használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha nem talál pontos egyezést a kifejezéssel, az elismerés nem segít.

> [!IMPORTANT]
> A Kifejezéslista szolgáltatás csak angol nyelven érhető el.

Kifejezéslista használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) objektumot, [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-)majd adjon hozzá konkrét szavakat és kifejezéseket a használatával.

A következő [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) felismerésre vagy a beszédfelismerési szolgáltatáshoz való újracsatlakozást követően végrehajtott módosítások.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Ha törölnie kell a kifejezéslistát: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek a felismerés pontosságának javítására

A kifejezéslisták csak egy lehetőség a felismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../how-to-custom-speech.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)