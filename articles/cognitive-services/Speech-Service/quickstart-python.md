---
title: 'Gyors útmutató: Beszédfelismerést, Python - beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató segítségével hozzon létre egy hang-szöveg transzformációs konzolalkalmazást a Pythonhoz készült a Speech SDK használatával. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 40869457ce933368e17a2054dfca50fc4505fa22
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381575"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Gyors útmutató: Pythonhoz készült beszédfelismerést a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk bemutatja, hogyan használható a beszédfelismerési szolgáltatás révén a beszéd SDK Pythonhoz készült. Azt szemlélteti, hogyan beszédfelismerést a mikrofon bemenete.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételek listáját:

* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).
* [Python 3.5-ös (64 bites)](https://www.python.org/downloads/) vagy újabb.
* A Python beszéd SDK-csomag Windows (x64), (macOS X 10.12 vagy újabb verziójára) Mac és Linux (Ubuntu 16.04 vagy 18.04 a x64) érhető el.
* Ubuntu rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* A Windows, is szükség van a [Microsoft Visual C++ újraterjeszthető csomag a Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) a platformhoz.

## <a name="install-the-speech-sdk"></a>A beszédfelismerés SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A Cognitive Services beszédfelismerő SDK Python-csomag telepíthető [PyPI](https://pypi.org/) ezzel a paranccsal a parancssorban:

```sh
pip install azure-cognitiveservices-speech
```

A Cognitive Services Speech SDK jelenlegi verziója az `1.2.0`.

## <a name="support-and-updates"></a>Támogatás és frissítések

A beszédfelismerés SDK Python-csomag frissítései PyPI keresztül, és a bejelentett a [kibocsátási megjegyzések](./releasenotes.md) lapot.
Ha új verzió érhető el, frissítheti azt a paranccsal `pip install --upgrade azure-cognitiveservices-speech`.
Ellenőrizheti, hogy mely verziója van telepítve vizsgálatával szerezheti be a `azure.cognitiveservices.speech.__version__` változó.

Ha probléma van, vagy hiányzik egy szolgáltatást, rendelkezik egy pillantást a [támogatási oldal](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>A beszédfelismerés SDK-val a Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

Másolja át is a [kód](#quickstart-code) a rövid útmutató egy forrásfájl `quickstart.py` , és futtassa azt az IDE-ben vagy a konzolon

```sh
python quickstart.py
```

vagy letöltheti, ez a rövid útmutató egy [Jupyter](https://jupyter.org) Notebookban, a [Cognitive Services beszéd mintaadattárban](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) , egy jegyzetfüzetet, majd futtassa.

### <a name="sample-code"></a>Mintakód

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Telepítse, és a Speech SDK használata a Visual Studio Code használatával

1. [Töltse le](https://www.python.org/downloads/) és a egy 64 bites verzióját (3.5-ös vagy újabb) a Python telepítése a számítógépre.
1. [Töltse le](https://code.visualstudio.com/Download) és a Visual Studio Code telepítéséhez.
1. Nyissa meg a Visual Studio Code és a Python-bővítmény telepítése kiválasztásával **fájl** > **beállítások** > **bővítmények** a menüből, és a "Python" keresése.
   ![Python-bővítmény telepítése](media/sdk/qs-python-vscode-python-extension.png)
1. Hozzon létre egy mappát a projekt, tárolásához, például a Windows Explorer használatával.
1. A Visual Studio Code-ban kattintson a **fájl** ikonra, és nyissa meg a létrehozott mappába.
   ![Mappa megnyitása](media/sdk/qs-python-vscode-python-open-folder.png)
1. Hozzon létre egy új Python-forrásfájl `speechsdk.py`, az új fájl ikonra kattintva.
   ![Fájl létrehozása](media/sdk/qs-python-vscode-python-newfile.png)
1. Másolása, beillesztése és mentése a [Python-kód](#quickstart-code) az újonnan létrehozott fájlt.
1. Helyezze be a Speech Service előfizetési adatok.
1. Ha egy Python-fordítóra már használatban van, akkor az ablak alján az állapotsor bal oldalán fog megjelenni.
   Ellenkező esetben szakaszhoz elérhető Python interprety listáját nyissa meg a **Parancskatalógus** (`Ctrl+Shift+P`), és írja be **Python: Válassza ki a Interpret**, és válasszon egy megfelelő.
1. A beszédfelismerés SDK Python-csomag még nem telepítette a kiválasztott Python-fordítóra, ha ezt könnyedén megteheti az belül a Visual Studio Code.
   A beszédfelismerés SDK-csomag telepítéséhez nyisson meg egy terminált, és a Parancskatalógus fel újra (`Ctrl+Shift+P`), és írja be **terminálon: Hozzon létre új integrált terminálon**.
   Megnyílik a terminálon írja be a parancsot `python -m pip install azure-cognitiveservices-speech`, vagy a rendszer a megfelelő parancsot.
1. A mintakód futtatásához kattintson a jobb gombbal a szerkesztő belül valahol, és válassza ki **futtassa Python-fájlt a terminálon**.
   Tegyük fel, röviden egyszer kéri, és az átírt szövegnek kell megjelennie röviddel utána.
   ![Minta futtatása](media/sdk/qs-python-vscode-python-run.png)

Ha ezek az utasítások a következő problémák vannak, tekintse meg a szélesebb körű [Visual Studio Code Python-oktatóprogram](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megtekintése a Githubon](https://aka.ms/csspeech/samples)
