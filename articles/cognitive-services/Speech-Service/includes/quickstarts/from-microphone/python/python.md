---
title: 'Rövid útmutató: beszéd felismerése mikrofonból, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval létrehozhat egy, a Pythonhoz készült Speech SDK-t használó beszéd-szöveges konzolos alkalmazást. Ha elkészült, a számítógép mikrofonjával valós időben konvertálhat át beszédet szöveggé.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 086a77a9d7096870b8d381d301bfc0f31ab390f9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468657"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítései a PyPI-on keresztül oszlanak meg, és a [kibocsátási megjegyzésekben](~/articles/cognitive-services/Speech-Service/releasenotes.md)jelent meg.
Ha új verzió érhető el, a `pip install --upgrade azure-cognitiveservices-speech`paranccsal frissítheti azt.
A `azure.cognitiveservices.speech.__version__` változó vizsgálatával ellenőrizze, hogy jelenleg melyik verziót telepítette.

Ha problémája van, vagy hiányzik egy szolgáltatás, tekintse meg a [támogatási és súgó beállításait](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>A Speech SDK-t használó Python-alkalmazás létrehozása

### <a name="run-the-sample"></a>Minta futtatása

Ebből a rövid útmutatóból másolhatja a [mintakód](#sample-code) `quickstart.py` és futtathatja azt az ide vagy a konzolon:

```sh
python quickstart.py
```

Vagy letöltheti ezt a rövid útmutatót [Jupyter](https://jupyter.org) jegyzetfüzetként a [Speech SDK Sample adattárból](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) , és futtathatja jegyzetfüzetként.

### <a name="sample-code"></a>Mintakód

> [!NOTE]
> A Speech SDK alapértelmezés szerint az en-us nyelv használatával ismeri fel a nyelvet, a forrás nyelvének kiválasztásával kapcsolatos információkért lásd: nyelv [megadása a beszédhez szöveghez](../../../../how-to-specify-source-language.md) .

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

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
1. Szúrja be a Speech Service-előfizetés adatait.
1. Ha be van jelölve, a Python-tolmács az ablak alján az állapotsor bal oldalán jelenik meg.
   Ellenkező esetben hozza létre az elérhető Python-tolmácsok listáját. Nyissa meg a parancssort (CTRL + SHIFT + P), és írja be a **Python: Select tolmács elemet**. Válasszon ki egy megfelelőt.
1. A Speech SDK Python-csomagot a Visual Studio Code-ból is telepítheti. Tegye meg, hogy ha még nincs telepítve a kiválasztott Python-tolmácshoz.
   A Speech SDK csomag telepítéséhez nyisson meg egy terminált. Hozza létre újra a parancs-palettát (CTRL + SHIFT + P), és írja be a következőt **: új integrált terminál létrehozása**.
   A megnyíló terminálon írja be a parancs `python -m pip install azure-cognitiveservices-speech` vagy a megfelelő parancsot a rendszer számára.
1. A mintakód futtatásához kattintson a jobb gombbal a szerkesztőben lévő valahova. Válassza **a Python-fájl futtatása a terminálon**lehetőséget.
   Ha a rendszer kéri, beszéljen néhány szót. Az átmásolt szöveg nem sokkal később jelenik meg.

   ![Minta futtatása](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Ha az utasítások követésével kapcsolatos problémái vannak, tekintse meg a kiterjedt [Visual Studio Code Python oktatóanyagot](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
