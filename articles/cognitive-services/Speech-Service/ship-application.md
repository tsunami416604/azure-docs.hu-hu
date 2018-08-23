---
title: Az Azure Cognitive Services, a Cognitive Services beszédfelismerő SDK API dokumentációja – oktatóanyagok és API-referencia
description: Ismerje meg, hogyan hozhat létre, és a Cognitive Services beszédfelismerő SDK-alkalmazások fejlesztése
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 65ff0e47cf7a53d519bfd0c50ea4c3ebd09a5766
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41987968"
---
# <a name="shipping-an-application"></a>Egy alkalmazás szállítási

Figyelje meg a [beszéd SDK licenc](license.md), valamint a [harmadik féltől származó szoftverek értesítések](third-party-notices.md) a Cognitive Services beszédfelismerő SDK terjesztésekor. Ezenkívül tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy).

A platformtól függően eltérő függőség létezik, az alkalmazás végrehajtása.

## <a name="windows"></a>Windows

A Cognitive Services beszédfelismerő SDK a Windows 10 és Windows Server 2016 rendszerben lett tesztelve.

A Cognitive Services beszédfelismerő SDK-t igényel a [Microsoft Visual C++ újraterjeszthető csomag a Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) a rendszeren. Letöltheti a legújabb verziójának telepítője a `Microsoft Visual C++ Redistributable for Visual Studio 2017` itt:

- [A Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Ha az alkalmazás felügyelt kódot használ a `.NET Framework 4.6.1` vagy újabb szükséges a célszámítógépen.

Mikrofon bemeneti a Media Foundation kódtárakat kell telepíteni. Ezek a könyvtárak a Windows 10 és Windows Server 2016 részét képezik. Akkor lehet a Speech SDK használata nélkül ezek a kódtárak mindaddig, amíg a hangbemeneti eszköz mikrofon sem használatos.

A szükséges beszéd SDK-fájlokat is telepíthető az alkalmazás könyvtárába. Ezzel a módszerrel az alkalmazás közvetlenül hozzáférhet a kódtárakat. Győződjön meg arról, válassza ki a megfelelő verzióját (Win32/x64), az alkalmazás megfelelő.

| Name (Név) | Függvény
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK-t, natív és felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.dll` | felügyelt üzembe helyezéséhez szükséges

## <a name="linux"></a>Linux

A beszédfelismerés SDK-könyvtár szállításra való kell natív alkalmazás `libMicrosoft.CognitiveServices.Speech.core.so`.
Győződjön meg arról, hogy a verzió (x86, x64) választja, az alkalmazás megfelelő. A Linux verziójától függően szükség lehet a következő függőségeket tartalmazza:

* A megosztott szalagtárakkal GNU C-függvénytár (beleértve a POSIX szálak programozási könyvtár `libpthreads`)
* Az OpenSSL kódtár (`libssl.so.1.0.0`)
* A cURL-tár (`libcurl.so.4`)
* A megosztott szalagtár ALSA alkalmazásokhoz (`libasound.so.2`)

Az Ubuntu 16.04, például a GNU C-függvénytárak kell már alapértelmezés szerint telepítve. Az utolsó három telepíthető, az alábbi parancsokkal:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
