---
title: 'Gyors útmutató: a beszédfelismerés szintézise, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan szintetizálhatja a beszédfelismerést a Pythonban a Speech SDK használatával
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 36e48a5d513daa951c1e92017ba9a2322b009703
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503408"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

Vagy letöltheti ezt a rövid útmutatót [Jupyter](https://jupyter.org) jegyzetfüzetként a [Speech SDK Sample adattárból](https://aka.ms/csspeech/samples) , és futtathatja jegyzetfüzetként.

### <a name="sample-code"></a>Mintakód

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>A Speech SDK és a Visual Studio Code telepítése és használata

1. Töltse le és telepítse a [Python](https://www.python.org/downloads/), 3,5 vagy újabb 64 bites verzióját a számítógépre.
1. Töltse le és telepítse a [Visual Studio Code](https://code.visualstudio.com/Download)-ot.
1. Nyissa meg a Visual Studio Code-ot, és telepítse a Python-bővítményt. A menüben válassza a **fájl** > **Beállítások** > **bővítmények** lehetőséget. Keressen rá a **Python**kifejezésre.

   ![A Python-bővítmény telepítése](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Hozzon létre egy mappát a projekt tárolásához a alkalmazásban. Ilyen például a Windows Intéző használata.
1. A Visual Studio Code-ban válassza a **fájl** ikont. Ezután nyissa meg a létrehozott mappát.

   ![Mappa megnyitása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Hozzon létre egy új Python-forrásfájlt, `speechsdk.py`az új fájl ikon kiválasztásával.

   ![Fájl létrehozása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Másolja, illessze be és mentse a [Python-kódot](#sample-code) az újonnan létrehozott fájlba.
1. Szúrja be a Speech Services-előfizetés adatait.
1. Ha be van jelölve, a Python-tolmács az ablak alján az állapotsor bal oldalán jelenik meg.
   Ellenkező esetben hozza létre az elérhető Python-tolmácsok listáját. Nyissa meg a parancssort (CTRL + SHIFT + P), és írja be a **Python: Select tolmács elemet**. Válasszon ki egy megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio Code-ból is telepítheti. Tegye meg, hogy ha még nincs telepítve a kiválasztott Python-tolmácshoz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. Hozza létre újra a parancs-palettát (CTRL + SHIFT + P), és írja be a következőt **: új integrált terminál létrehozása**.
   A megnyíló terminálon írja be a parancs `python -m pip install azure-cognitiveservices-speech` vagy a megfelelő parancsot a rendszer számára.
1. A mintakód futtatásához kattintson a jobb gombbal a szerkesztőben lévő valahova. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   Ha a rendszer kéri, írjon be egy szöveget. A szintetizált hang rövidesen le lesz játszva.

   ![Minta futtatása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Ha az utasítások követésével kapcsolatos problémái vannak, tekintse meg a kiterjedt [Visual Studio Code Python oktatóanyagot](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
