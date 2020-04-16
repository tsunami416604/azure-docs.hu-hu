---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399953"
---
:::row:::
    :::column span="3":::
        A Speech SDK csak az **Ubuntu 16.04/18.04**, **a Debian 9**, a Red Hat Enterprise Linux **(RHEL) 7/8**és a **CentOS 7/8** rendszereket támogatja a következő célarchitektúrákon, ha Linux-szal használják:
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
> Linux ARM64 célzásakor és C# használatakor a .NET Core 3.x (dotnet-sdk-3.x csomag) szükséges. Ha arm32-t vagy ARM64-et céloz meg, a Python nem támogatott.

> [!NOTE]
> Az Ubuntu 16.04, ubuntu 18.04 és Debian 9 x86-os architektúrái csak a C++ fejlesztést támogatják a Speech SDK-val.

### <a name="system-requirements"></a>Rendszerkövetelmények

Natív alkalmazás esetén a beszédfelismerési `libMicrosoft.CognitiveServices.Speech.core.so`SDK a . Győződjön meg arról, hogy a célarchitektúra (x86, x64) megegyezik az alkalmazás. A Linux-verziótól függően további függőségek re lehet szükség.

- A GNU C könyvtár megosztott könyvtárai (beleértve a `libpthreads`POSIX szálak programozási könyvtárát, )
- Az OpenSSL`libssl.so.1.0.0` könyvtár `libssl.so.1.0.2`( vagy )
- Az ALSA alkalmazások megosztott`libasound.so.2`könyvtára ( )

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
> Kövesse az [RHEL/CentOS 7 beszédfelismerési SDK-ra való beállítására](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)vonatkozó utasításokat.

> [!TIP]
> Az RHEL/CentOS 8 rendszeren kövesse az [OpenSSL linuxos beállítására](../how-to-configure-openssl-linux.md)vonatkozó utasításokat.

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
