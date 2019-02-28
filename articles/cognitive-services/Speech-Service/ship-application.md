---
title: Az SDK - beszédszolgáltatások beszédfelismerési-alkalmazások fejlesztése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre alkalmazásokat a beszédfelismerési SDK használatával.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: f3522f065d22ce276174fbd165c37df3914e32b9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962026"
---
# <a name="ship-an-application"></a>Szállítási alkalmazás

Figyelje meg a [beszéd SDK licenc](https://aka.ms/csspeech/license201809), valamint a [harmadik féltől származó szoftverek értesítések](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) amikor az Azure Cognitive Services beszédfelismerő SDK terjesztése. Ezenkívül tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy).

A platformtól függően eltérő függőség létezik, az alkalmazás végrehajtása.

## <a name="windows"></a>Windows

A Cognitive Services beszédfelismerő SDK a Windows 10 és Windows Server 2016 rendszerben lett tesztelve.

A Cognitive Services beszédfelismerő SDK-t igényel a [Microsoft Visual C++ újraterjeszthető csomag a Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) a rendszeren. Letöltheti a legújabb verziójának telepítője a `Microsoft Visual C++ Redistributable for Visual Studio 2017` itt:

- [A Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Ha az alkalmazás felügyelt kódot, a `.NET Framework 4.6.1` vagy újabb szükséges a célszámítógépen.

Mikrofon bemeneti a Multimédia alaprendszer függvénytárak telepítve kell lennie. Ezek a könyvtárak a Windows 10 és Windows Server 2016 részét képezik. Akkor lehet a Speech SDK használata nélkül ezek a kódtárak mindaddig, amíg a hangbemeneti eszköz egy mikrofonnal nem használja.

A szükséges beszéd SDK-fájlokat is telepíthető az alkalmazás könyvtárába. Ezzel a módszerrel az alkalmazás közvetlenül hozzáférhet a kódtárakat. Győződjön meg arról, hogy válassza ki a megfelelő verzióját (Win32/x64), amely megfelel az alkalmazás.

| Name (Név) | Függvény
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK-t, natív és felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.dll` | felügyelt üzembe helyezéséhez szükséges

>[!NOTE]
> A fájl a kiadás 1.3.0 kezdve `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (tartalmazza a szükséges a korábbi kiadásokban) többé nem szükséges. A funkció most már integrálva van a core SDK-t.

## <a name="linux"></a>Linux

A beszédfelismerés SDK jelenleg támogatja az Ubuntu 16.04 és 18.04 disztribúciókat.
Egy natív alkalmazást, a beszéd SDK-könyvtár szállításra való szüksége `libMicrosoft.CognitiveServices.Speech.core.so`.
Jelölje ki, amely megfelel az alkalmazás verziója (x86, x64). A Linux verziójától függően is szüksége lehet a következő függőségeket tartalmaznak:

* A megosztott szalagtárakkal GNU C-függvénytár (beleértve a POSIX szálak programozási könyvtár `libpthreads`)
* Az OpenSSL kódtár (`libssl.so.1.0.0`)
* A megosztott szalagtár ALSA alkalmazásokhoz (`libasound.so.2`)

Ubuntu rendszeren a GNU C-függvénytárak már alapértelmezés szerint kell telepíteni. Az utolsó három is telepíthetők az alábbi parancsokkal:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2 wget
```

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
