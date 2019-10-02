---
title: 'Gyors útmutató: Beszédszintetizátor beszéd, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szintetizálhatja a beszédfelismerést a Pythonban a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803963"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>Gyors útmutató: Beszédfelismerés a Pythonhoz készült Speech SDK-val

A [beszédfelismerés](quickstart-python.md)is elérhetővé teszi a gyors útmutatókat.

Ez a cikk bemutatja, hogyan használható a Speech Services a Pythonhoz készült Speech SDK-val. Bemutatja, hogyan lehet szövegből szintetizálni beszédet, és az alapértelmezett hangkimenettel játssza le.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be az egyiket ingyenesen](get-started.md).
* [Python 3,5 vagy újabb verzió](https://www.python.org/downloads/).
* A Python Speech SDK csomag elérhető a következő operációs rendszerekhez:
    * Windows 10: x64 és x86.
    * Mac: macOS X 10,12 vagy újabb verzió.
    * Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 x64 rendszeren.
* Linux rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

  * On Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Debian 9 rendszeren:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Windows rendszeren a platformhoz a [Visual Studio C++ 2019-hez készült Microsoft vizualizációs terjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) csomag szükséges.

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Vegye figyelembe, hogy ez az oktatóanyag nem fog működni a 1.7.0-nál korábbi SDK-verzióval.

Ez a parancs telepíti a Python-csomagot a [PyPI](https://pypi.org/) -ből a Speech SDK-hoz:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítései a PyPI-on keresztül oszlanak meg, és a [kibocsátási megjegyzésekben](./releasenotes.md)jelent meg.
Ha új verzió érhető el, a `pip install --upgrade azure-cognitiveservices-speech` paranccsal frissítheti azt.
A `azure.cognitiveservices.speech.__version__` változó vizsgálatával ellenőrizze, hogy jelenleg melyik verziót telepítette.

Ha problémája van, vagy hiányzik egy szolgáltatás, tekintse meg a [támogatási és súgó beállításait](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>A Speech SDK-t használó Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

Ebből a rövid útmutatóból másolhatja a [mintakód](#sample-code) `quickstart.py` forrásfájlban, és futtathatja az ide vagy a konzolon:

```sh
python quickstart.py
```

Vagy letöltheti ezt a rövid útmutatót [Jupyter](https://jupyter.org) jegyzetfüzetként a [Speech SDK Sample adattárból](https://aka.ms/csspeech/samples) , és futtathatja jegyzetfüzetként.

### <a name="sample-code"></a>Mintakód

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>A Speech SDK és a Visual Studio Code telepítése és használata

1. Töltse le és telepítse a [Python](https://www.python.org/downloads/), 3,5 vagy újabb 64 bites verzióját a számítógépre.
1. Töltse le és telepítse a [Visual Studio Code](https://code.visualstudio.com/Download)-ot.
1. Nyissa meg a Visual Studio Code-ot, és telepítse a Python-bővítményt. Válassza a **fájl** > **Beállítások** > **bővítmények** lehetőséget a menüből. Keressen rá a **Python**kifejezésre.

   ![A Python-bővítmény telepítése](media/sdk/qs-python-vscode-python-extension.png)

1. Hozzon létre egy mappát a projekt tárolásához a alkalmazásban. Ilyen például a Windows Intéző használata.
1. A Visual Studio Code-ban válassza a **fájl** ikont. Ezután nyissa meg a létrehozott mappát.

   ![Mappa megnyitása](media/sdk/qs-python-vscode-python-open-folder.png)

1. Hozzon létre egy új, `speechsdk.py` Python-forrásfájlt az új fájl ikon kiválasztásával.

   ![Fájl létrehozása](media/sdk/qs-python-vscode-python-newfile.png)

1. Másolja, illessze be és mentse a [Python-kódot](#sample-code) az újonnan létrehozott fájlba.
1. Szúrja be a Speech Services-előfizetés adatait.
1. Ha be van jelölve, a Python-tolmács az ablak alján az állapotsor bal oldalán jelenik meg.
   Ellenkező esetben hozza létre az elérhető Python-tolmácsok listáját. Nyissa meg a parancssort (CTRL + SHIFT + P), és írja be a **Python: Válassza a tolmács**lehetőséget. Válasszon ki egy megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio Code-ból is telepítheti. Tegye meg, hogy ha még nincs telepítve a kiválasztott Python-tolmácshoz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. Hozza létre újra a parancs-palettát (CTRL + SHIFT + P), és írja be a **Terminal: Hozzon létre egy új, integrált Terminal @ no__t-0.
   A megnyíló terminálon írja be a `python -m pip install azure-cognitiveservices-speech` parancsot vagy a megfelelő parancsot a rendszer számára.
1. A mintakód futtatásához kattintson a jobb gombbal a szerkesztőben lévő valahova. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   Ha a rendszer kéri, írjon be egy szöveget. A szintetizált hang rövidesen le lesz játszva.

   ![Minta futtatása](media/sdk/qs-python-vscode-python-run-tts.png)

Ha az utasítások követésével kapcsolatos problémái vannak, tekintse meg a kiterjedt [Visual Studio Code Python oktatóanyagot](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Lásd még

- [Hangbetűkészletek testreszabása](how-to-customize-voice-font.md)
- [Hangminták rögzítése](record-custom-voice-samples.md)
