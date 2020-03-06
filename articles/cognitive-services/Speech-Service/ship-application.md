---
title: Alkalmazások fejlesztése a Speech SDK-Speech szolgáltatással
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezhet üzembe olyan alkalmazást, amely a Speech SDK-t használja a támogatott platformokon.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330804"
---
# <a name="ship-an-application"></a>Alkalmazáskészítés

Tekintse meg a [SPEECH SDK-licencet](https://aka.ms/csspeech/license201809), valamint a [harmadik féltől származó szoftvereket](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) , amikor az Azure Cognitive Services Speech SDK-t terjeszti. Tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy)is.

A platformtól függően eltérő függőség létezik, az alkalmazás végrehajtása.

## <a name="windows"></a>Windows

A Cognitive Services beszédfelismerő SDK a Windows 10 és Windows Server 2016 rendszerben lett tesztelve.

A Cognitive Services Speech SDK-hoz a [Microsoft C++ Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) -es verziójának terjeszthető változata szükséges a rendszeren. A `Microsoft Visual C++ Redistributable for Visual Studio 2019` legújabb verziójához a telepítőket letöltheti:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Ha az alkalmazás felügyelt kódot használ, akkor a célszámítógépen `.NET Framework 4.6.1` vagy annál újabb szükséges.

Mikrofon bemeneti a Multimédia alaprendszer függvénytárak telepítve kell lennie. Ezek a könyvtárak a Windows 10 és Windows Server 2016 részét képezik. Akkor lehet a Speech SDK használata nélkül ezek a kódtárak mindaddig, amíg a hangbemeneti eszköz egy mikrofonnal nem használja.

A szükséges beszéd SDK-fájlokat is telepíthető az alkalmazás könyvtárába. Ezzel a módszerrel az alkalmazás közvetlenül hozzáférhet a kódtárakat. Győződjön meg arról, hogy válassza ki a megfelelő verzióját (Win32/x64), amely megfelel az alkalmazás.

| Name (Név) | Függvény |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK-t, natív és felügyelt üzembe helyezéséhez szükséges |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | felügyelt üzembe helyezéséhez szükséges                      |

> [!NOTE]
> A (korábbi kiadásokban szállított) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájl kiadási 1.3.0 kezdve többé nem szükséges. A funkció most már integrálva van az alap SDK-val.

> [!NOTE]
> A Windows Forms-alkalmazás (.NET-keretrendszer C# ) projekt esetében ellenőrizze, hogy a könyvtárak szerepelnek-e a projekt központi telepítési beállításai között. Ezt a `Properties -> Publish Section`alatt tekintheti meg. Kattintson a `Application Files` gombra, és keresse meg a megfelelő könyvtárakat a legördülő listából. Győződjön meg arról, hogy az érték `Included`. A Visual Studio tartalmazni fogja a fájlt a projekt közzétételekor vagy telepítésekor.

## <a name="linux"></a>Linux

A Speech SDK jelenleg a következőket támogatja: Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8 disztribúció.
Natív alkalmazás esetén a Speech SDK-függvénytárat kell átadnia `libMicrosoft.CognitiveServices.Speech.core.so`.
Jelölje ki, amely megfelel az alkalmazás verziója (x86, x64). A Linux verziójától függően is szüksége lehet a következő függőségeket tartalmaznak:

- A GNU C könyvtár megosztott kódtárai (beleértve a POSIX-szálak programozási könyvtárát, `libpthreads`)
- Az OpenSSL-könyvtár (`libssl.so.1.0.0` vagy `libssl.so.1.0.2`)
- Az ALSA-alkalmazások megosztott könyvtára (`libasound.so.2`)

Ubuntu rendszeren a GNU C-kódtárakat már alapértelmezés szerint telepíteni kell. Az utolsó három is telepíthetők az alábbi parancsokkal:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

A Debian 9 rendszeren telepítse ezeket a csomagokat:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

RHEL/CentOS 8 rendszeren:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

## <a name="next-steps"></a>Következő lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Lásd: beszédfelismerés felismeréseC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
