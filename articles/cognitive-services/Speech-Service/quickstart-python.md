---
title: 'Gyors útmutató: Beszédfelismerés felismerése, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval létrehozhat egy, a Pythonhoz készült Speech SDK-t használó beszéd-szöveges konzolos alkalmazást. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 3f473e8ad15d3df053cca100580574b1b115f492
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803255"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Gyors útmutató: Beszéd felismerése a Pythonhoz készült Speech SDK-val

A gyors útmutatók is elérhetők a [Speech-szintézishez](quickstart-text-to-speech-python.md)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk bemutatja, hogyan használható a Speech Services a Pythonhoz készült Speech SDK-val. Bemutatja, hogyan ismerhető fel a mikrofon bemenetének beszéde.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetési kulcs a Speech Serviceshez. [Szerezze be az egyiket ingyenesen](get-started.md).
* [Python 3,5 vagy újabb verzió](https://www.python.org/downloads/).
* A Python Speech SDK csomag elérhető a következő operációs rendszerekhez:
    * Windows: x64 és x86.
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

Vagy letöltheti ezt a rövid útmutatót [Jupyter](https://jupyter.org) jegyzetfüzetként a [Speech SDK Sample adattárból](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) , és futtathatja jegyzetfüzetként.

### <a name="sample-code"></a>Mintakód

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

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
   Ha a rendszer kéri, beszéljen néhány szót. Az átmásolt szöveg nem sokkal később jelenik meg.

   ![Minta futtatása](media/sdk/qs-python-vscode-python-run.png)

Ha az utasítások követésével kapcsolatos problémái vannak, tekintse meg a kiterjedt [Visual Studio Code Python oktatóanyagot](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megismerése a GitHubon](https://aka.ms/csspeech/samples)
