---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 2e6be94a7fc83c6da8bf7a46fde6ea0f5ef7c764
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400433"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Hozzon létre egy LUIS-alkalmazást, és szerezzen be egy végpontkulcsot](../../../../quickstarts/create-luis.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítéseit a PyPI-n keresztül terjesztik, és a [Kibocsátási megjegyzésekben](~/articles/cognitive-services/Speech-Service/releasenotes.md)jelentik be.
Ha új verzió érhető el, a parancssegítségével `pip install --upgrade azure-cognitiveservices-speech`frissíthet rá.
Ellenőrizze, hogy jelenleg melyik verzió `azure.cognitiveservices.speech.__version__` van telepítve a változó vizsgálatával.

Ha problémája van, vagy hiányzik egy funkció, olvassa el a [Támogatás és súgó beállításai című témakört.](~/articles/cognitive-services/Speech-Service/support.md)

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>A beszédfelismerési SDK-t használó Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

A [mintakódot](#sample-code) ebből a rövid útmutatóból egy forrásfájlba `quickstart.py` másolhatja, és futtathatja az IDE-ben vagy a konzolon:

```Bash
python quickstart.py
```

Vagy letöltheti ezt a rövid útmutatót [Jupyter-jegyzetfüzetként](https://jupyter.org) a [Speech SDK mintatárból,](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) és futtathatja notebookként.

### <a name="sample-code"></a>Mintakód

> [!NOTE]
> A beszédsdk alapértelmezés szerint felismeri a nyelv en-us használatát, olvassa el a forrásnyelv megadása a [beszéd szöveghez című témakört](../../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.

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

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>A Beszéd SDK telepítése és használata a Visual Studio-kóddal

1. Töltse le és telepítse a [Python](https://www.python.org/downloads/)64 bites , 3.5-3.8-as verzióját a számítógépre.
1. Töltse le és telepítse a [Visual Studio-kódot](https://code.visualstudio.com/Download).
1. Nyissa meg a Visual Studio-kódot, és telepítse a Python-bővítményt. Válassza a menü > **Fájlbeállítások** >  **File****kiterjesztései parancsát.** **Python**keresése .

   ![A Python-bővítmény telepítése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Hozzon létre egy mappát a projekt tárolásához. Ilyen például a Windows Intéző használata.
1. A Visual Studio-kódban kattintson a **Fájl** ikonra. Ezután nyissa meg a létrehozott mappát.

   ![Mappa megnyitása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Hozzon létre egy `speechsdk.py`új Python-forrásfájlt, az új fájl ikonjának kiválasztásával.

   ![Fájl létrehozása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Másolja, illessze be és mentse a [Python-kódot](#sample-code) az újonnan létrehozott fájlba.
1. Helyezze be a beszédszolgáltatás-előfizetés adatait.
1. Ha be van jelölve, egy Python-értelmező jelenik meg az állapotsor bal oldalán az ablak alján.
   Ellenkező esetben hozza létre az elérhető Python-értelmezők listáját. Nyissa meg a parancspaletta <kbd>Ctrl +Shift +P</kbd> és írja **be Python: Select Interpreter**. Válassza ki a megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio-kódból telepítheti. Tegye ezt meg, ha még nincs telepítve a kiválasztott Python-értelmezőhöz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. A parancspaletta felsorolása <kbd>Ctrl+Shift+P</kbd> és enter **Terminal: Create New Integrated Terminal**.
   A megnyíló terminálon `python -m pip install azure-cognitiveservices-speech` adja meg a rendszernek megfelelő parancsot vagy parancsot.
1. A mintakód futtatásához kattintson a jobb gombbal valahol a szerkesztőn belül. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   A hangfájlból származó beszédbevitel első 15 másodperceit a rendszer felismeri és beírja a konzol ablakába.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Ha problémái vannak az alábbi utasításokat követve, olvassa el a visual [studio code python-oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)ot.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]