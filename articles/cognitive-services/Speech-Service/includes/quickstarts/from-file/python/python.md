---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 9365b4bbf6e6fb37156b6e9f24b923591945ea52
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376601"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../overview.md#try-the-speech-service-for-free)
> * [LUIS-alkalmazás létrehozása és végponti kulcs beszerzése](../../../../quickstarts/create-luis.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítései a PyPI-on keresztül oszlanak meg, és a [kibocsátási megjegyzésekben](~/articles/cognitive-services/Speech-Service/releasenotes.md)jelent meg.
Ha új verzió érhető el, a paranccsal frissítheti azt `pip install --upgrade azure-cognitiveservices-speech` .
A változó ellenőrzésével ellenőrizze, hogy jelenleg melyik verziót telepítette `azure.cognitiveservices.speech.__version__` .

Ha problémája van, vagy hiányzik egy szolgáltatás, tekintse meg a [támogatási és súgó beállításait](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>A Speech SDK-t használó Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

Ebből a rövid útmutatóból átmásolhatja a [mintakód](#sample-code) egy forrásfájlban, `quickstart.py` és futtathatja azt az ide vagy a konzolon:

```Bash
python quickstart.py
```

Vagy letöltheti ezt a rövid útmutatót [Jupyter](https://jupyter.org) jegyzetfüzetként a [Speech SDK Sample adattárból](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) , és futtathatja jegyzetfüzetként.

### <a name="sample-code"></a>Mintakód

> [!NOTE]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
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

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>A Speech SDK és a Visual Studio Code telepítése és használata

1. Töltse le és telepítse a [Python](https://www.python.org/downloads/), 3,5 és 3,8 64-bites verzióját a számítógépre.
1. Töltse le és telepítse a [Visual Studio Code-ot](https://code.visualstudio.com/Download).
1. Nyissa meg a Visual Studio Code-ot, és telepítse a Python-bővítményt. Válassza **File**  >  **Preferences**  >  a menüből a file Preferences**bővítmények** lehetőséget. Keressen rá a **Python**kifejezésre.

   ![A Python-bővítmény telepítése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Hozzon létre egy mappát a projekt tárolásához a alkalmazásban. Ilyen például a Windows Intéző használata.
1. A Visual Studio Code-ban válassza a **fájl** ikont. Ezután nyissa meg a létrehozott mappát.

   ![Mappa megnyitása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Hozzon létre egy új Python-forrásfájlt `speechsdk.py` az új fájl ikon kiválasztásával.

   ![Fájl létrehozása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Másolja, illessze be és mentse a [Python-kódot](#sample-code) az újonnan létrehozott fájlba.
1. Szúrja be a Speech Service-előfizetés adatait.
1. Ha be van jelölve, a Python-tolmács az ablak alján az állapotsor bal oldalán jelenik meg.
   Ellenkező esetben hozza létre az elérhető Python-tolmácsok listáját. Nyissa meg a Command paletta <kbd>CTRL + SHIFT + P billentyűkombinációt</kbd> , és írja be a **Python: Select tolmács elemet**. Válasszon ki egy megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio Code-ból is telepítheti. Tegye meg, hogy ha még nincs telepítve a kiválasztott Python-tolmácshoz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. Hozza létre újra a parancs-palettát <kbd>CTRL + SHIFT + P billentyűkombinációval</kbd> , és adja meg a **terminált: új integrált terminál létrehozása**.
   A megnyíló terminálon írja be a parancsot `python -m pip install azure-cognitiveservices-speech` vagy a megfelelő parancsot a rendszer számára.
1. A mintakód futtatásához kattintson a jobb gombbal a szerkesztőben lévő valahova. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   A hangfájl első 15 másodpercét a rendszer felismeri és naplózza a konzol ablakában.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Ha az utasítások követésével kapcsolatos problémái vannak, tekintse meg a kiterjedt [Visual Studio Code Python oktatóanyagot](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]