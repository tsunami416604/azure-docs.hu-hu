---
title: 'Gyors útmutató: Beszédfelismerést, Python - beszédszolgáltatások'
titleSuffix: Azure Cognitive Services
description: Ez az útmutató segítségével hozzon létre egy hang-szöveg transzformációs konzolalkalmazást, amely a Speech SDK Pythonhoz készült. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: chlandsi
ms.openlocfilehash: d3a6b8389d44d3ad92f9305124884d97fa293429
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447108"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Gyors útmutató: Pythonhoz készült beszédfelismerést a Speech SDK-val

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Ez a cikk bemutatja, hogyan használhatja a beszédfelismerési szolgáltatás révén a beszéd SDK Pythonhoz készült. Azt szemlélteti, hogyan beszédfelismerést a mikrofon bemenete.

## <a name="prerequisites"></a>Előfeltételek

* A beszédfelismerési szolgáltatás egy Azure-előfizetés kulcs. [Igényeljen ingyenesen egy](get-started.md).
* [Python 3.5-ös vagy újabb](https://www.python.org/downloads/).
* Az említett operációs rendszerektől a Python beszéd SDK-csomag érhető el: 
    * Windows: x64 és x86.
    * Mac: macOS X 10.12 vagy újabb verziója.
    * Linux: Ubuntu 16.04 vagy a x64 18.04.
* Ubuntu rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libasound2 wget
  ```

* A Windows, is szükség van a [Microsoft Visual C++ újraterjeszthető csomag a Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) a platformhoz.

## <a name="install-the-speech-sdk"></a>A beszédfelismerés SDK telepítése

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Ez a parancs telepíti a Python-csomag [PyPI](https://pypi.org/) beszéd SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Támogatás és frissítések 

A beszédfelismerés SDK Python-csomag frissítései PyPI keresztül, és a bejelentett a [kibocsátási megjegyzések](./releasenotes.md).
Ha új verzió érhető el, frissítheti azt a paranccsal `pip install --upgrade azure-cognitiveservices-speech`.
Ellenőrizze, hogy mely verziója van telepítve vizsgálatával szerezheti be a `azure.cognitiveservices.speech.__version__` változó. 

Ha a probléma, vagy éppen hiányzik az egyik funkciója, [támogatás és súgó](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>A beszédfelismerés SDK-t használó Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

Másolhatja a [mintakód](#sample-code) a rövid útmutató egy forrásfájl `quickstart.py` , és futtassa azt az IDE-ben vagy a konzolon:

```sh
python quickstart.py
```

Vagy letöltheti, ez a rövid útmutató egy [Jupyter](https://jupyter.org) Notebookban, a [beszéd SDK mintaadattár](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) , egy jegyzetfüzetet, majd futtassa. 

### <a name="sample-code"></a>Mintakód

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Telepítse, és a Speech SDK használata a Visual Studio Code használatával

1. Töltse le és telepítse a 64 bites verzióját [Python](https://www.python.org/downloads/), 3.5-ös vagy újabb, a számítógépen.
1. Töltse le és telepítse [Visual Studio Code](https://code.visualstudio.com/Download).
1. Nyissa meg a Visual Studio Code-ot, és telepítse a Python-bővítményt. Válassza ki **fájl** > **beállítások** > **bővítmények** a menüből. Keresse meg **Python**.

   ![A Python-bővítmény telepítése](media/sdk/qs-python-vscode-python-extension.png)

1. Hozzon létre egy mappát a projekt tárolásához. Ilyen például, a Windows Intéző használatával.
1. A Visual Studio Code-ban válassza ki a **fájl** ikonra. Ezután nyissa meg a létrehozott mappába.

   ![Mappa megnyitása](media/sdk/qs-python-vscode-python-open-folder.png)
   
1. Hozzon létre egy új Python forrásfájl `speechsdk.py`, a fájl új ikon kiválasztásával.

   ![Fájl létrehozása](media/sdk/qs-python-vscode-python-newfile.png)

1. Másolása, beillesztése és mentése a [Python-kód](#sample-code) az újonnan létrehozott fájlt.
1. Helyezze be a Speech service előfizetési adatok.
1. Ha a kiválasztott, egy Python-fordítóra az ablak alján az állapotsor bal oldalán jeleníti meg.
   Ellenkező esetben viszi, megjelenik az elérhető Python interprety listáját. Nyissa meg a parancskatalógust (Ctrl + Shift + P), és adja meg **Python: Válassza ki a Interpret**. Válasszon egy megfelelő.
1. A Visual Studio Code belül a Speech SDK Python-csomag is telepítheti. Megtenni, ha nincs telepítve, még a Python-fordítóra kiválasztva.
   A beszédfelismerés SDK-csomag telepítéséhez nyisson meg egy terminált. Nyissa meg a parancskatalógust újra (Ctrl + Shift + P), és adja meg **terminálon: Hozzon létre új integrált terminálon**.
   Megnyílik a terminálon írja be a parancsot `python -m pip install azure-cognitiveservices-speech` vagy a rendszer a megfelelő parancsot.
1. A mintakód futtatásához kattintson a jobb gombbal valahol a szerkesztő belül. Válassza ki **futtassa Python-fájlt terminálon**.
   Néhány szó beszél, amikor a rendszer kéri. Az átírt szöveg röviddel utána jeleníti meg.

   ![A minta futtatása](media/sdk/qs-python-vscode-python-run.png)

Ha ezek az utasítások a következő problémákat, tekintse meg a szélesebb körű [Visual Studio Code Python-oktatóprogram](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Python-minták megtekintése a Githubon](https://aka.ms/csspeech/samples)
