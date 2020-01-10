---
title: 'Gyors útmutató: a Python platformhoz készült Speech SDK beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a platformot a Python és a Speech Service SDK használatával.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 218d0dca43d126c1318c273603a4980697c465af
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751968"
---
Ez az útmutató bemutatja, hogyan telepítheti a Pythonhoz készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- A Python Speech SDK csomag elérhető a következő operációs rendszerekhez:
  - Windows: x64 és x86
  - Mac: macOS X 10,12 vagy újabb verzió
  - Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 x64 rendszeren

## <a name="prerequisites"></a>Előfeltételek

- A támogatott linuxos platformokhoz telepíteni kell bizonyos könyvtárakat (`libssl` a Secure Sockets Layer támogatáshoz, és `libasound2` a hangtámogatáshoz). A könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért tekintse meg az alábbi disztribúciót.

  - Ubuntu rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - A Debian 9 rendszeren futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- Windows rendszeren a platformhoz a [Visual Studio C++ 2019-hez készült Microsoft vizualizációs terjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) csomag szükséges. Vegye figyelembe, hogy az első telepítéskor szükség lehet a Windows újraindítására az útmutató folytatása előtt.
- Végül pedig [Python 3,5, 3,6 vagy 3,7](https://www.python.org/downloads/)szükséges. A telepítés ellenőrzéséhez nyisson meg egy parancssort, és írja be a `python --version` parancsot, és vizsgálja meg az eredményt. Ha megfelelően van telepítve, a "Python 3.5.1" vagy hasonló választ kap.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>A Speech SDK telepítése a Visual Studio Code használatával

1. Töltse le és telepítse a [Python](https://www.python.org/downloads/) legújabb támogatott verzióját a platformhoz, 3,5-es vagy újabb verzióhoz.
   - A Windows-felhasználók a telepítési folyamat során a "Python hozzáadása az elérési úthoz" lehetőséget kell választani.
1. Töltse le és telepítse a [Visual Studio Code](https://code.visualstudio.com/Download)-ot.
1. Nyissa meg a Visual Studio Code-ot, és telepítse a Python-bővítményt. A menüben válassza a **fájl** > **Beállítások** > **bővítmények** lehetőséget. Keressen rá a **Python** kifejezésre, majd kattintson a **telepítés**gombra.

   ![A Python-bővítmény telepítése](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. A Visual Studio Code-ban is telepítse a Speech SDK Python-csomagot az integrált parancssorból:
   1. Nyisson meg egy terminált (a legördülő menükből, a **terminal** > **új terminálból**)
   1. A megnyíló terminálon írja be a parancsot `python -m pip install azure-cognitiveservices-speech`

Ekkor készen áll a Pythonban a Speech SDK-ra való kódolásra, és a [következő lépésekre](#next-steps) léphet tovább. Ha még nem ismeri a Visual Studio Code-ot, tekintse meg a részletes [Visual Studio Code-dokumentációt](https://code.visualstudio.com/docs). További információ a Visual Studio Code-ról és a Pythonról: [Visual Studio Code Python oktatóanyag](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>A Speech SDK telepítése a parancssor használatával

Ha nem használja a Visual Studio Code-ot, a következő parancs telepíti a Python-csomagot a [PyPI](https://pypi.org/) -ből a Speech SDK-hoz. A Visual Studio Code-felhasználók esetében ugorjon a következő alszakaszra.

```sh
pip install azure-cognitiveservices-speech
```

Ha macOS rendszeren fut, előfordulhat, hogy a következő parancsot kell futtatnia a `pip` parancs működéséhez:

```sh
python3 -m pip install --upgrade pip
```

Miután sikeresen felhasználta `pip` a `azure-cognitiveservices-speech`telepítésére, a Speech SDK használatával importálja a névteret a Python-projektbe. Példa:

```py
import azure.cognitiveservices.speech as speechsdk
```

Ez a [következő lépésekben](#next-steps) felsorolt példákban részletesebben látható.

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítései a PyPI-on keresztül oszlanak meg, és a [kibocsátási megjegyzésekben](~/articles/cognitive-services/speech-service/releasenotes.md)jelent meg.
Ha új verzió érhető el, a `pip install --upgrade azure-cognitiveservices-speech`paranccsal frissítheti azt.
A `azure.cognitiveservices.speech.__version__` változó vizsgálatával ellenőrizze, hogy jelenleg melyik verziót telepítette.

Ha problémája van, vagy hiányzik egy szolgáltatás, tekintse meg a [támogatási és súgó beállításait](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
