---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: d0298bcd675b1b94999dab3a1ad1c40a6feb7438
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93136257"
---
:::row:::
    :::column span="3":::
        A Speech SDK csak az **Ubuntu 16.04/18.04/20.04** , a **Debian 9/10** , a **Red Hat Enterprise Linux (RHEL) 7/8** és a **CentOS 7/8** használatát támogatja a következő megcélzott architektúrák esetén, amikor Linux rendszeren használja:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) C++ fejlesztéshez
- x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) Java-hoz
- x64, ARM32 (Debian/Ubuntu) és ARM64 (Debian/Ubuntu) a .NET Core-hoz
- x64 a Pythonhoz

> [!IMPORTANT]
> A C# Linux ARM64 esetében a .NET Core 3. x (DotNet-SDK-3. x csomag) szükséges.

### <a name="system-requirements"></a>Rendszerkövetelmények

Natív alkalmazások esetén a Speech SDK a következőre támaszkodik: `libMicrosoft.CognitiveServices.Speech.core.so` . Győződjön meg arról, hogy a célként megadott architektúra (x86, x64) megfelel az alkalmazásnak. A Linux-verziótól függően további függőségekre lehet szükség.

- A GNU C könyvtár megosztott kódtárai (beleértve a POSIX-szálak programozási könyvtárát `libpthreads` )
- Az OpenSSL-könyvtár ( `libssl.so.1.0.0` vagy `libssl.so.1.0.2` )
- Az ALSA-alkalmazások megosztott könyvtára ( `libasound.so.2` )

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Ha a libssl 1.0. x nem érhető el, telepítse inkább a libssl 1.1-es verzióját.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Ha a libssl 1.0. x nem érhető el, telepítse inkább a libssl 1.1-es verzióját.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 és CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - A RHEL/CentOS 7 rendszeren kövesse a [RHEL/CentOS 7 for SPEECH SDK konfigurálására](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)vonatkozó utasításokat.
> - A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
