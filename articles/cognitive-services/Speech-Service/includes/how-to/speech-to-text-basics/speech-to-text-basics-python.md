---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: c8354a67d26210035355f4947cb5f17cee934508
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372822"
---
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk a következőket feltételezi:

* Azure-fiókkal és Speech Service-előfizetéssel rendelkezik. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../get-started.md).

## <a name="install-and-import-the-speech-sdk"></a>A Speech SDK telepítése és importálása

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t.

```Python
pip install azure-cognitiveservices-speech
```

Ha macOS rendszeren fut, és telepítési problémákba ütközik, előfordulhat, hogy először futtatnia kell ezt a parancsot.

```Python
python3 -m pip install --upgrade pip
```

A Speech SDK telepítését követően importálja azt a Python-projektbe ezzel az utasítással.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python). Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

A [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)inicializálása többféleképpen is elvégezhető:

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Vessünk egy pillantást arra, hogyan jön létre egy [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) egy kulcs és egy régió használatával.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Felismerő inicializálása

[`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)létrehozása után a következő lépés egy [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)inicializálása. [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)inicializálásakor át kell adnia a `speech_config`. Ez biztosítja azokat a hitelesítő adatokat, amelyeknek a beszédfelismerési szolgáltatásnak a kérelmét ellenőriznie kell.

Ha az eszköz alapértelmezett mikrofonjának használatával ismeri fel a beszédet, a [`SpeechRecognizer`nek](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) a következőképpen kell kinéznie:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Ha meg szeretné adni a hangbemeneti eszközt, létre kell hoznia egy [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) , és meg kell adnia a `audio_config` paramétert a [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)inicializálásakor.

> [!TIP]
> [Ismerje meg, hogyan kérheti le az eszköz azonosítóját a hangbemeneti eszközhöz](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor továbbra is meg kell adnia egy `audio_config`. Ha azonban a `device_name`megadása helyett [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python)hoz létre, akkor a `filename` paramétert fogja használni.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Beszéd felismerése

A Pythonhoz készült Speech SDK [felismerő osztálya](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) néhány olyan módszert tesz elérhetővé, amelyet a beszédfelismeréshez használhat.

* Single-shot felismerés (szinkronizálás) – az elismerést blokkolja (szinkron) módban hajtja végre. A függvény egyetlen Kimondás után ad vissza értéket. Az egyetlen kiírás végének meghatározása úgy történik, hogy a csendet figyeli a végén, vagy legfeljebb 15 másodperces hangot dolgoz fel. A feladat eredményként visszaadja az elismerés szövegét.
* Single-shot felismerés (aszinkron) – az elismerést nem blokkoló (aszinkron) módban végzi. Ez egyetlen Kimondás felismerését fogja felismerni. Az egyetlen kiírás végének meghatározása úgy történik, hogy a csendet figyeli a végén, vagy legfeljebb 15 másodperces hangot dolgoz fel.
* Folyamatos felismerés (szinkronizálás) – a folyamatos felismerést szinkron módon kezdeményezi. Az ügyfélnek csatlakoznia kell `EventSignal`hoz a felismerési eredmények fogadásához. Az felismerés leállításához hívja meg [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Folyamatos felismerés (aszinkron) – aszinkron módon kezdeményezi a folyamatos felismerési műveletet. A felhasználónak csatlakoznia kell a EventSignal az elismerés eredményeinek fogadásához. Az aszinkron folyamatos felismerés leállításához hívja meg a [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)metódust.

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Egyszeri felvétel felismerése

Íme egy példa arra, hogy hogyan történik a szinkron egylépéses felismerés a [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once)használatával:

```Python
result = speech_recognizer.recognize_once()
```

Íme egy példa arra, hogy hogyan történik a szinkron egylépéses felismerés a [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)használatával:

```Python
result = speech_recognizer.recognize_once_async()
```

Függetlenül attól, hogy a szinkron vagy az aszinkron módszert használta-e, meg kell írnia egy kódot, hogy megismételje az eredményt. Ez a példa a [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)értékeli ki:

* Kinyomtatja a felismerés eredményét: `speechsdk.ResultReason.RecognizedSpeech`
* Ha nincs felismerési egyezés, tájékoztassa a felhasználót: `speechsdk.ResultReason.NoMatch `
* Ha hiba történt, nyomtassa ki a következő hibaüzenetet: `speechsdk.ResultReason.Canceled`

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

A folyamatos felismerés valamivel többet vesz igénybe, mint a single-shot felismerés. Ehhez a `EventSignal`hoz kell csatlakoznia az eredmények felismeréséhez, és az felismerés leállításához meg kell hívnia a [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) vagy a [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)függvényt. Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlon.

Kezdjük azzal, hogy meghatározza a bemenetet, és inicializál egy [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python):

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Ezután hozzon létre egy változót a beszédfelismerés állapotának kezeléséhez. A kezdéshez be kell állítani ezt `False`re, mert az elismerés elején nyugodtan feltételezhető, hogy nem fejeződött be.

```Python
done = False
```

Most létrehozunk egy visszahívást a folyamatos felismerés leállításához, amikor egy `evt` érkezik. Van néhány dolog, amit érdemes szem előtt tartani.

* `evt` fogadásakor a rendszer kinyomtatja a `evt` üzenetet.
* Egy `evt` fogadása után a rendszer meghívja a [stop_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) metódust az elismerés leállítására.
* Az felismerési állapot `True`ra módosul.

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

Ez a mintakód azt mutatja be, hogyan csatlakoztathatók a visszahívások a [`SpeechRecognizer`ból ](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)eljuttatott eseményekhez.

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): a köztes felismerési eredményeket tartalmazó események jelzése.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): a végső felismerési eredményeket tartalmazó események jelzése (sikeres felismerési kísérletet jelezve).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): a felismerési munkamenet kezdetét jelző események jelzése (művelet).
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): az elismerési munkamenet végét jelző események jelzése (művelet).
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): a megszakított felismerési eredményeket tartalmazó események jelzése (olyan felismerési kísérletet jelez, amelyet a rendszer az eredmény vagy a közvetlen törlési kérelem miatt megszakított, vagy ha egy átviteli vagy protokollhiba történt).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Minden beállítással meghívhatjuk [start_continuous_recognition ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használatakor engedélyezheti a diktálás feldolgozását a megfelelő "diktálás engedélyezése" funkció használatával. Ez a mód azt eredményezi, hogy a beszédfelismerési konfigurációs példány értelmezi a mondatok (például a központozás) szövegének leírását. A "Do You Live in Town kérdőjel" kifejezés például "a városban él?" szöveget fogja értelmezni.

A diktálási mód engedélyezéséhez használja a [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) metódusát.

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszédfelismerés általános feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet németre. A kódban keresse meg a SpeechConfig, majd adja hozzá közvetlenül a sort.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language) egy paraméter, amely argumentumként egy karakterláncot vesz fel. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>Az elismerés pontosságának javítása

A Speech SDK-val többféleképpen javítható az elismerés pontossága. Vessünk egy pillantást a kifejezések listájára. A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez. Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer a kifejezések listájában szereplő bejegyzést használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha a kifejezés pontos egyezése nem található, az elismerés nem támogatott.

> [!IMPORTANT]
> A kifejezés lista szolgáltatás csak angol nyelven érhető el.

A kifejezések listájának használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) objektumot, majd adjon hozzá konkrét szavakat és kifejezéseket a [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-).

A [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) változásai a következő felismeréskor vagy a beszédfelismerési szolgáltatáshoz való Újrakapcsolódás után lépnek érvénybe.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Ha törölnie kell a kifejezések listáját: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek az elismerés pontosságának növeléséhez

A kifejezések listája csak egyetlen lehetőség az elismerés pontosságának javítására. További lehetőségek: 

* [A pontosság javítása Custom Speech](../../../how-to-custom-speech.md)
* [A pontosság javítása a bérlői modellekkel](../../../tutorial-tenant-model.md)