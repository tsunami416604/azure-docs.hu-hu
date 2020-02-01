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
ms.openlocfilehash: 4f75adba27c8173f918fa1afbd44f307d50eb995
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902023"
---
# <a name="ship-an-application"></a>Alkalmazáskészítés

Tekintse meg a [SPEECH SDK-licencet](https://aka.ms/csspeech/license201809), valamint a [harmadik féltől származó szoftvereket](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) , amikor az Azure Cognitive Services Speech SDK-t terjeszti. Tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy)is.

A platformtól függően eltérő függőségek állnak fenn az alkalmazás végrehajtásához.

## <a name="windows"></a>Windows

A Cognitive Services Speech SDK tesztelése a Windows 10 és a Windows Server 2016 rendszeren történik.

A Cognitive Services Speech SDK-hoz a [Microsoft C++ Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) -es verziójának terjeszthető változata szükséges a rendszeren. A `Microsoft Visual C++ Redistributable for Visual Studio 2019` legújabb verziójához a telepítőket letöltheti:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Ha az alkalmazás felügyelt kódot használ, akkor a célszámítógépen `.NET Framework 4.6.1` vagy annál újabb szükséges.

A mikrofonos bevitelhez telepíteni kell a Multimédia alaprendszer kódtárakat. Ezek a kódtárak a Windows 10 és a Windows Server 2016 rendszer részét képezik. Ezen könyvtárak nélkül is használhatja a Speech SDK-t, ha a mikrofon nem hangbemeneti eszközként van használatban.

A szükséges Speech SDK-fájlokat az alkalmazással megegyező könyvtárba lehet telepíteni. Így az alkalmazás közvetlenül hozzáférhet a könyvtárakhoz. Ügyeljen arra, hogy a megfelelő verziót (Win32/x64) válassza ki, amely megfelel az alkalmazásnak.

| Name (Név) | Függvény |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | A natív és felügyelt központi telepítéshez szükséges Core SDK |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Felügyelt központi telepítéshez szükséges                      |

> [!NOTE]
> A (korábbi kiadásokban szállított) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájl kiadási 1.3.0 kezdve többé nem szükséges. A funkció most már integrálva van az alap SDK-val.

> [!NOTE]
> A Windows Forms-alkalmazás (.NET-keretrendszer C# ) projekt esetében ellenőrizze, hogy a könyvtárak szerepelnek-e a projekt központi telepítési beállításai között. Ezt a `Properties -> Publish Section`alatt tekintheti meg. Kattintson a `Application Files` gombra, és keresse meg a megfelelő könyvtárakat a legördülő listából. Győződjön meg arról, hogy az érték `Included`. A Visual Studio tartalmazni fogja a fájlt a projekt közzétételekor vagy telepítésekor.

## <a name="linux"></a>Linux

A Speech SDK jelenleg az Ubuntu 16,04, az Ubuntu 18,04 és a Debian 9 disztribúciókat támogatja.
Natív alkalmazás esetén a Speech SDK-függvénytárat kell átadnia `libMicrosoft.CognitiveServices.Speech.core.so`.
Ügyeljen arra, hogy az alkalmazásnak megfelelő verziót (x86, x64) válassza ki. A Linux-verziótól függően előfordulhat, hogy a következő függőségeket is meg kell adnia:

- A GNU C könyvtár megosztott kódtárai (beleértve a POSIX-szálak programozási könyvtárát, `libpthreads`)
- Az OpenSSL-könyvtár (`libssl.so.1.0.0` vagy `libssl.so.1.0.2`)
- Az ALSA-alkalmazások megosztott könyvtára (`libasound.so.2`)

Ubuntu rendszeren a GNU C-kódtárakat már alapértelmezés szerint telepíteni kell. Az utolsó három telepíthető a következő parancsokkal:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

A Debian 9 rendszeren telepítse ezeket a csomagokat:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Következő lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Lásd: beszédfelismerés felismeréseC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
