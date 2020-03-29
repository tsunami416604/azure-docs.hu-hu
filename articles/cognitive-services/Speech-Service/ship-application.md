---
title: Alkalmazások fejlesztése a Speech SDK - Speech Service szolgáltatással
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan telepíthet egy olyan alkalmazást, amely a beszédfelismerési SDK-t használja a támogatott platformokon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330804"
---
# <a name="ship-an-application"></a>Alkalmazáskészítés

Vegye figyelembe a [beszédbeszéd SDK-licenc,](https://aka.ms/csspeech/license201809)valamint a [külső gyártótól származó szoftver értesítéseket,](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) amikor az Azure Cognitive Services Speech SDK terjesztése. Tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy)is.

A platformtól függően különböző függőségek léteznek az alkalmazás végrehajtásához.

## <a name="windows"></a>Windows

A Cognitive Services beszédfelismerési SDK-ját Windows 10 és Windows Server 2016 rendszeren tesztelték.

A Cognitive Services beszédstak számára a [Microsoft Visual C++ újraterjeszthető a rendszeren.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) Az itt verzió legújabb verziójához `Microsoft Visual C++ Redistributable for Visual Studio 2019` töltheti le a telepítők:

- [Win32 között](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Ha az alkalmazás felügyelt `.NET Framework 4.6.1` kódot használ, a vagy később van szükség a célgépen.

Mikrofonbevitel esetén telepíteni kell a Media Foundation tárakat. Ezek a tárak a Windows 10 és a Windows Server 2016 részét képezik. A beszédfelismerési SDK-t e könyvtárak nélkül is használhatja, feltéve, hogy a mikrofont nem használják hangbemeneti eszközként.

A szükséges beszédsdka-fájlok az alkalmazással azonos könyvtárban telepíthetők. Így az alkalmazás közvetlenül hozzáférhet a könyvtárakhoz. Győződjön meg arról, hogy az alkalmazásnak megfelelő verziót (Win32/x64) válassza ki.

| Név | Függvény |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, a natív és felügyelt telepítéshez szükséges |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Felügyelt telepítéshez szükséges                      |

> [!NOTE]
> Az 1.3.0-s kiadástól kezdve a fájlra `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (a korábbi kiadásokban szállítva) már nincs szükség. The functionality is now integrated in the core SDK.

> [!NOTE]
> A Windows Forms app (.NET Framework) C# projekt esetében győződjön meg arról, hogy a könyvtárak szerepelnek a projekt telepítési beállításaiközött. Akkor ellenőrizze ezt `Properties -> Publish Section`a . Kattintson `Application Files` a gombra, és keresse meg a megfelelő könyvtárakat a lefelé görgetési listából. Ellenőrizze, hogy az `Included`érték értéke . A Visual Studio a projekt közzétételekor/telepítésekor tartalmazza a fájlt.

## <a name="linux"></a>Linux

A Speech SDK jelenleg támogatja az Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8 disztribúciókat.
Natív alkalmazás esetén a beszédfelismerési SDK-könyvtárat kell szállítania. `libMicrosoft.CognitiveServices.Speech.core.so`
Győződjön meg arról, hogy az alkalmazásnak megfelelő verziót (x86, x64) választja ki. A Linux-verziótól függően előfordulhat, hogy a következő függőségeket is tartalmaznia kell:

- A GNU C könyvtár megosztott könyvtárai (beleértve a `libpthreads`POSIX szálak programozási könyvtárát, )
- Az OpenSSL`libssl.so.1.0.0` könyvtár `libssl.so.1.0.2`( vagy )
- Az ALSA alkalmazások megosztott`libasound.so.2`könyvtára ( )

Az Ubuntu-n a GNU C könyvtárakat alapértelmezés szerint már telepíteni kell. Az utolsó három a következő parancsokkal telepíthető:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

A Debian 9-en telepítsd a következő csomagokat:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

A RHEL/CentOS 8-on:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Az RHEL/CentOS 8 rendszeren kövesse az [OpenSSL linuxos beállítására](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)vonatkozó utasításokat.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A beszéd felismerése C-ben #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
