---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: f674edd15b86f49d60450a53f5df5852b32f95a4
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906434"
---
Ez az útmutató bemutatja, hogyan telepítheti a Linux rendszerhez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Rendszerkövetelmények

Linux Tekintse meg a [támogatott Linux-disztribúciók és a megcélzott architektúrák](~/articles/cognitive-services/speech-service/speech-sdk.md)listáját.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* GCC
* [Go Binary (1,13 vagy újabb)](https://golang.org/dl/)

* A támogatott linuxos platformokhoz telepíteni kell bizonyos kódtárakat ( `libssl` a Secure Sockets Layer támogatásához és a hangalapú `libasound2` támogatáshoz). A könyvtárak megfelelő verzióinak telepítéséhez szükséges parancsokért tekintse meg az alábbi disztribúciót.

   * Ubuntu/Debian rendszeren:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Ha a libssl 1.0.0 nem érhető el, telepítse a libssl 1.0. x (ahol az x értéke nagyobb, mint 0) vagy a libssl 1.1.

   * RHEL/CentOS-on:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - A RHEL/CentOS 7 rendszeren kövesse a [RHEL/CentOS 7 for SPEECH SDK konfigurálására](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)vonatkozó utasításokat.
> - A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Go-környezet konfigurálása

A következő lépések végrehajtásával állítsa be a go-környezetet a Speech SDK megkereséséhez. Mindkét lépésben cserélje le a processzort a `<architecture>` CPU-architektúrára. Ez a következő lesz:,, `x86` `x64` `arm32` vagy `arm64` .

1. Mivel a kötések támaszkodnak `cgo` , meg kell adnia a környezeti változókat, hogy a go megtalálja az SDK-t:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Az SDK-t is tartalmazó alkalmazások futtatásához meg kell adnia az operációs rendszer helyét, ahol a következő könyvtárak találhatók:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list-go.md)]
