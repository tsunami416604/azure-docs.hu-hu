---
title: Az RHEL/CentOS 7 beállítása – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: További információ az RHEL/CentOS 7 beállításáról a beszédfelismerési SDK-k használható.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639162"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Az RHEL/CentOS 7 konfigurálása beszédfelismerési SDK-hoz

A Red Hat Enterprise Linux (RHEL) 8 x64 és CentOS 8 x64 hivatalosan is támogatja a Speech SDK 1.10.0-s és újabb verzióját. A beszédfelismerési SDK rhel/CentOS 7 x64 rendszeren is használható, de ehhez frissíteni kell a C++ fordítót (C++ fejlesztéshez) és a megosztott C++ futásidejű könyvtárat a rendszeren.

A C++ fordító verziójának ellenőrzéséhez futtassa a következőket:

```bash
g++ --version
```

Ha a fordító telepítve van, a kimenetnek így kell kinéznie:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Ez az üzenet jelzi, hogy a GCC 4-es főverziója telepítve van. Ez a verzió nem támogatja teljes mértékben a C++ 11 szabványt, amelyet a Speech SDK használ. Ha c++ programot próbál lefordítani ezzel a GCC-verzióval és a Speech SDK fejlécekkel, az fordítási hibákat eredményez.

Fontos a megosztott C++ futásidejű függvénytár (libstdc++) verziójának ellenőrzése is. A beszédbeszéd SDK-jának nagy része natív C++ kódként van megvalósítva, ami azt jelenti, hogy a libstdc++ függvénye az alkalmazások fejlesztéséhez használt nyelvtől.

A libstdc++ helyének megkereséséhez futtassa a következőt:

```bash
ldconfig -p | grep libstdc++
```

A vanília RHEL/CentOS 7 (x64) kimenete:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Ezen üzenet alapján érdemes ellenőrizni a verziódefiníciókat ezzel a paranccsal:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

A kimenetnek a következőnek kell lennie:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

A beszédsdkóhoz **CXXABI_1.3.9** **GLIBCXX_3.** Ezeket az információkat `ldd libMicrosoft.CognitiveServices.Speech.core.so` a Linux-csomag beszédsdk-kódtáraion futtatva találhatja meg.

> [!NOTE]
> Ajánlott, hogy a rendszerre telepített GCC-verzió legalább **5.4.0**legyen, megfelelő futásidejű kódtárakkal.

## <a name="example"></a>Példa

Ez a mintaparancs bemutatja, hogyan konfigurálható az RHEL/CentOS 7 x64 fejlesztési célokra (C++, C#, Java, Python) a Speech SDK 1.10.0 vagy újabb beszédfelismerési sdk-val:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Speech SDK ismertetése](speech-sdk.md)
