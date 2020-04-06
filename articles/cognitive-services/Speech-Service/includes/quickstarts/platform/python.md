---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 327000173b4c5c378f60c76eb1a24bd155901671
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666918"
---
Ez az útmutató bemutatja, hogyan telepítheti a [speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) python. Ha csak azt szeretné, hogy a csomag `pip install azure-cognitiveservices-speech`neve a kezdéshez a saját, fuss .

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- A Python Speech SDK-csomag a következő operációs rendszerekhez érhető el:
  - Windows: x64 és x86
  - Mac: macOS X 10.12-es vagy újabb verzió
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 az x64-en

## <a name="prerequisites"></a>Előfeltételek

- A támogatott Linux platformokon`libssl` bizonyos könyvtárak at kell `libasound2` telepíteni (a biztonságos aljzatok réteg támogatásához és a hangtámogatáshoz). Az alábbi terjesztésben tájékatosan tájékasztok a könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért.

  - Az Ubuntun futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - A Debian 9-en futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Az RHEL/CentOS 8-on futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> Az RHEL/CentOS 8 rendszeren kövesse az [OpenSSL linuxos beállítására](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)vonatkozó utasításokat.

- Windows rendszerben a [Microsoft Visual Studio 2019-hez terjeszthető Microsoft Visual C++ újraterjeszthető](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) platformra van szükség. Ne feledje, hogy ennek első telepítése szükségessé teheti a Windows újraindítását, mielőtt folytatná ezt az útmutatót.
- És végül, szüksége lesz [Python 3.5-3.8](https://www.python.org/downloads/). A telepítés ellenőrzéséhez nyisson meg egy `python --version` parancssort, írja be a parancsot, és ellenőrizze az eredményt. Ha megfelelően van telepítve, a "Python 3.5.1" vagy hasonló választ kap.

## <a name="install-the-speech-sdk-from-pypi"></a>A beszédfelismerési SDK telepítése pypi-ből

Ha saját környezetet használ, vagy eszközöket hoz létre, futtassa a következő parancsot a Speech SDK [PyPI-ből](https://pypi.org/)történő telepítéséhez. A Visual Studio-kód felhasználói számára ugorjon a következő alszakaszra az irányított telepítéshez.

```sh
pip install azure-cognitiveservices-speech
```

Ha macOS rendszert futtat, előfordulhat, hogy a fenti `pip` parancs működéséhez a következő parancsot kell futtatnia:

```sh
python3 -m pip install --upgrade pip
```

Miután sikeresen `pip` használta `azure-cognitiveservices-speech`a telepítést, használhatja a beszédbeszéd SDK importálja a névteret a Python-projektek.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>A beszédfelismerési SDK telepítése a Visual Studio-kód használatával

1. Töltse le és telepítse a [Python](https://www.python.org/downloads/) legújabb, 3.5-3.8-as verzióját.
   - A Windows-felhasználók győződjön meg róla, hogy válassza ki a "Add Python a PATH" a telepítési folyamat során.
1. Töltse le és telepítse a [Visual Studio-kódot](https://code.visualstudio.com/Download).
1. Nyissa meg a Visual Studio-kódot, és telepítse a Python-bővítményt. Válassza a menü > **Fájlbeállítások** >  **File****kiterjesztései parancsát.** Keresse meg a **Python** t, és kattintson **a Telepítés gombra.**

   ![A Python-bővítmény telepítése](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. A Visual Studio-kódból is telepítse a Speech SDK Python csomagot az integrált parancssorból:
   1. Nyisson meg egy terminált (a legördülő menükből, az Új **terminál** > **terminálról)**
   1. A megnyíló terminálon írja be a parancsot`python -m pip install azure-cognitiveservices-speech`

Ha még nem ért idát meg a Visual Studio-kód, olvassa el a [Visual Studio kóddokumentációját.](https://code.visualstudio.com/docs) A Visual Studio-kódról és a Pythonról a [Visual Studio Code Python oktatóanyaga](https://code.visualstudio.com/docs/python/python-tutorial)című témakörben talál további információt.

## <a name="support-and-updates"></a>Támogatás és frissítések

A Speech SDK Python-csomag frissítéseit a PyPI-n keresztül terjesztik, és a [Kibocsátási megjegyzésekben](~/articles/cognitive-services/speech-service/releasenotes.md)jelentik be.
Ha új verzió érhető el, a parancssegítségével `pip install --upgrade azure-cognitiveservices-speech`frissíthet rá.
Ellenőrizze, hogy jelenleg melyik verzió `azure.cognitiveservices.speech.__version__` van telepítve a változó vizsgálatával.

Ha problémája van, vagy hiányzik egy funkció, olvassa el a [Támogatás és súgó beállításai című témakört.](~/articles/cognitive-services/speech-service/support.md)

## <a name="next-steps"></a>További lépések

[!INCLUDE [windows](../quickstart-list.md)]
