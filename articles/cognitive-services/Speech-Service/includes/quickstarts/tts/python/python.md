---
title: 'Rövid útmutató: Beszéd szintetizálása, Python – beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés idúsítása pythonban a BeszédsK használatával
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 239bb1e43b6a89ed1e8a18d544cf646b5f2de9b9
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671674"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [Azure-beszédfelismerési erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítéseit a PyPI-n keresztül terjesztik, és a [Kibocsátási megjegyzésekben](~/articles/cognitive-services/Speech-Service/releasenotes.md)jelentik be.
Ha új verzió érhető el, a parancssegítségével `pip install --upgrade azure-cognitiveservices-speech`frissíthet rá.
Ellenőrizze, hogy jelenleg melyik verzió `azure.cognitiveservices.speech.__version__` van telepítve a változó vizsgálatával.

Ha problémája van, vagy hiányzik egy funkció, olvassa el a [Támogatás és súgó beállításai című témakört.](~/articles/cognitive-services/Speech-Service/support.md)

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>A beszédfelismerési SDK-t használó Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

A [mintakódot](#sample-code) ebből a rövid útmutatóból egy forrásfájlba `quickstart.py` másolhatja, és futtathatja az IDE-ben vagy a konzolon:

```sh
python quickstart.py
```

Vagy letöltheti ezt a rövid útmutatót [Jupyter-jegyzetfüzetként](https://jupyter.org) a [Speech SDK mintatárból,](https://aka.ms/csspeech/samples) és futtathatja notebookként.

### <a name="sample-code"></a>Mintakód

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

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
   Ellenkező esetben hozza létre az elérhető Python-értelmezők listáját. Nyissa meg a parancspalettát (Ctrl+Shift+P), és írja be a **Python: Select Interpreter parancsot**. Válassza ki a megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio-kódból telepítheti. Tegye ezt meg, ha még nincs telepítve a kiválasztott Python-értelmezőhöz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. A parancspaletta ismételt létrehozása (Ctrl+Shift+P) és a **Terminal: Create New Integrated Terminal**( Új integrált terminál létrehozása ) beírása.
   A megnyíló terminálon `python -m pip install azure-cognitiveservices-speech` adja meg a rendszernek megfelelő parancsot vagy parancsot.
1. A mintakód futtatásához kattintson a jobb gombbal valahol a szerkesztőn belül. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   A rendszer a rendszer a rendszer kérésekor írjon be egy szöveget. A szintetizált hang lejátszása röviddel később történik.

   ![Minta futtatása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Ha problémái vannak az alábbi utasításokat követve, olvassa el a visual [studio code python-oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial)ot.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Lásd még

- [Egyéni hang létrehozása](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Egyéni hangminták rögzítése](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
