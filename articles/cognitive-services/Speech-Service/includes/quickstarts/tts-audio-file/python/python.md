---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 34d9dc2bdf99737e6b4c17abe37a18f795c19df8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274904"
---
## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Kap egy részére szabad.](~/articles/cognitive-services/Speech-Service/get-started.md)
* [Python 3.5-3.8](https://www.python.org/downloads/).
* A Python Speech SDK-csomag a következő operációs rendszerekhez érhető el:
    * Windows: x64 és x86.
    * Mac: macOS X 10.12-es vagy újabb verzió.
    * Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 az x64-en.
* Linux on futtassa ezeket a parancsokat a szükséges csomagok telepítéséhez:

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel--centos-8"></a>[RHEL / CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> Az RHEL/CentOS 7/8 rendszeren kövesse az [OpenSSL for Linux beállítására](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)vonatkozó utasításokat.

---

* Windows rendszerben a [Microsoft Visual Studio 2019-hez terjeszthető Microsoft Visual C++ újraterjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) platformra van szükség.

## <a name="install-the-speech-sdk"></a>A beszédfelismerési SDK telepítése

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Ez a parancs telepíti a Python-csomagot a [PyPI-ből](https://pypi.org/) a beszédfelismerési SDK-hoz:

```Bash
pip install azure-cognitiveservices-speech
```

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

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

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
   Ellenkező esetben hozza létre az elérhető Python-értelmezők listáját. Nyissa meg a parancspalettát (<kbd>Ctrl+Shift+P</kbd>), és írja be a **Python: Select Interpreter parancsot**. Válassza ki a megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio-kódból telepítheti. Tegye ezt meg, ha még nincs telepítve a kiválasztott Python-értelmezőhöz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. A parancspaletta megjelölése<kbd>(Ctrl+Shift+P</kbd>) és írja be a **Terminal: Create New Integrated Terminal parancsot**.
   A megnyíló terminálon `python -m pip install azure-cognitiveservices-speech` adja meg a rendszernek megfelelő parancsot vagy parancsot.
1. A mintakód futtatásához kattintson a jobb gombbal valahol a szerkesztőn belül. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   A program a szöveget beszédbeszédmé alakítja, és a megadott hangadatokba menti.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Ha problémái vannak az alábbi utasításokat követve, olvassa el a visual [studio code python-oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)ot.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
