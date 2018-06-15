---
title: Az Azure kognitív, kognitív Services beszéd SDK API-dokumentáció – oktatóanyagok API-referencia |} Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és hogyan fejleszthetnek alkalmazásokat az kognitív szolgáltatások beszéd SDK-val
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: d410dda09fdd30181b633c454b1d44610b10c472
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35349986"
---
# <a name="shipping-an-application"></a>Egy alkalmazás szállítási

Figyelje meg a [beszéd SDK licenc](license.md), valamint a [harmadik féltől származó szoftver értesítéseket](third-party-notices.md) a kognitív szolgáltatások beszéd SDK terjesztésekor. Emellett tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy).

Attól függően, hogy a platform különböző függőség létezik, az alkalmazás végrehajtásához.

## <a name="windows"></a>Windows

A kognitív beszéd SDK-t a Windows 10 és Windows Server 2016 lett tesztelve.

A kognitív beszéd SDK-t igényel a [Microsoft Visual C++ terjeszthető csomag Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) a rendszeren. Telepítőcsomagokat, a legújabb verzióját letöltheti a `Microsoft Visual C++ Redistributable for Visual Studio 2017` itt:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Ha az alkalmazás nem használja a felügyelt kód a `.Net Framework 4.6.1` vagy újabb szükséges a célszámítógépen.

A Media Foundation könyvtárak mikrofon bemeneti kell telepíteni. Ezek a könyvtárak a Windows 10 és Windows Server 2016 részét képezik. Is lehet a beszédfelismerés SDK használata nélkül a tárak, amíg a mikrofon nem használatos a hangbemeneti eszközöket.

A szükséges beszéd SDK-fájlok is telepíthető az alkalmazás könyvtárába. Így az alkalmazás közvetlenül hozzáférhetnek a könyvtárak. Gondoskodjon róla, hogy az alkalmazás megfelelő helyes verzióját (Win32/x64).

| Name (Név) | Függvény
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK, natív és felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.dll` | felügyelt üzembe helyezéséhez szükséges

## <a name="linux"></a>Linux

Küldje el a beszédfelismerés SDK-könyvtár kell natív alkalmazások `libMicrosoft.CognitiveServices.Speech.core.so`.
Mindenképpen jelölje ki a verziót (x86, x64) az alkalmazás megfelelő. A Linux verziójától függően is szükség lehet az alábbi függőségeket tartalmaz:

* A megosztott szalagtárakkal GNU C-függvénytár (beleértve a POSIX szálak programozási könyvtárban `libpthreads`)
* A OpenSSL könyvtárban (`libssl.so.1.0.0`)
* A cURL könyvtárban (`libcurl.so.4`)
* A megosztott szalagtár ALSA alkalmazások (`libasound.so.2`)

Az Ubuntu 16.04 például a GNU C-függvénytárak már telepítve kell lennie alapértelmezés szerint. Az utolsó három telepítheti az alábbi parancsokkal:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
