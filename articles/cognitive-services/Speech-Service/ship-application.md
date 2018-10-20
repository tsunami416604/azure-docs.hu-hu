---
title: Beszédfelismerési szolgáltatás SDK API-JÁNAK dokumentációja – oktatóanyagok és API-referencia
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre és fejleszthet alkalmazásokat a beszédfelismerési SDK-val
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 7510b1501051b79f6bdaf33ec57647a7982fafda
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469418"
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
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.dll` | felügyelt üzembe helyezéséhez szükséges

## <a name="linux"></a>Linux

Egy natív alkalmazást, a beszéd SDK-könyvtár szállításra való szüksége `libMicrosoft.CognitiveServices.Speech.core.so`.
Jelölje ki, amely megfelel az alkalmazás verziója (x86, x64). A Linux verziójától függően is szüksége lehet a következő függőségeket tartalmaznak:

* A megosztott szalagtárakkal GNU C-függvénytár (beleértve a POSIX szálak programozási könyvtár `libpthreads`)
* Az OpenSSL kódtár (`libssl.so.1.0.0`)
* A cURL-tár (`libcurl.so.4`)
* A megosztott szalagtár ALSA alkalmazásokhoz (`libasound.so.2`)

Az Ubuntu 16.04, például a GNU C-függvénytárak kell már alapértelmezés szerint telepítve. Az utolsó három is telepíthetők az alábbi parancsokkal:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
