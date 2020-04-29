---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399953"
---
:::row:::
    :::column span="3":::
        A Speech SDK csak az **Ubuntu 16.04/18.04**, a **Debian 9**, a **Red Hat Enterprise Linux (RHEL) 7/8**és a **CentOS 7/8** használatát támogatja a következő, a Linux rendszeren használt architektúrák esetén:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> A Linux ARM64 megcélzása és a C# használatával – a .NET Core 3. x (DotNet-SDK-3. x csomag) szükséges. Ha a ARM32 vagy a ARM64 célozza meg, a Python nem támogatott.

> [!NOTE]
> Az Ubuntu 16,04, Ubuntu 18,04 és a Debian 9 x86-architektúrái csak a C++ fejlesztést támogatják a Speech SDK-val.

### <a name="system-requirements"></a>Rendszerkövetelmények

Natív alkalmazások esetén a Speech SDK a következőre támaszkodik `libMicrosoft.CognitiveServices.Speech.core.so`:. Győződjön meg arról, hogy a célként megadott architektúra (x86, x64) megfelel az alkalmazásnak. A Linux-verziótól függően további függőségekre lehet szükség.

- A GNU C könyvtár megosztott kódtárai (beleértve a POSIX- `libpthreads`szálak programozási könyvtárát)
- Az OpenSSL-könyvtár`libssl.so.1.0.0` ( `libssl.so.1.0.2`vagy)
- Az ALSA-alkalmazások megosztott könyvtára (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 és CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Kövesse a [RHEL/CentOS 7 for SPEECH SDK konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)utasításait.

> [!TIP]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](../how-to-configure-openssl-linux.md)utasításait.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
