---
title: Az SDK - beszédszolgáltatások beszédfelismerési-alkalmazások fejlesztése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre alkalmazásokat a beszédfelismerési SDK használatával.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 8fc27002af4ebef0825b23c806cfedbe7adf9642
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404814"
---
# <a name="ship-an-application"></a>Szállítási alkalmazás

Figyelje meg a [beszéd SDK licenc](https://aka.ms/csspeech/license201809), valamint a [harmadik féltől származó szoftverek értesítések](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) amikor az Azure Cognitive Services beszédfelismerő SDK terjesztése. Ezenkívül tekintse át a [Microsoft adatvédelmi nyilatkozatát](https://aka.ms/csspeech/privacy).

A platformtól függően eltérő függőség létezik, az alkalmazás végrehajtása.

## <a name="windows"></a>Windows

A Cognitive Services beszédfelismerő SDK a Windows 10 és Windows Server 2016 rendszerben lett tesztelve.

A Cognitive Services Speech SDK-hoz a [Microsoft C++ Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) -es verziójának terjeszthető változata szükséges a rendszeren. Letöltheti a legújabb verziójának telepítője a `Microsoft Visual C++ Redistributable for Visual Studio 2019` itt:

- [A Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Ha az alkalmazás felügyelt kódot, a `.NET Framework 4.6.1` vagy újabb szükséges a célszámítógépen.

Mikrofon bemeneti a Multimédia alaprendszer függvénytárak telepítve kell lennie. Ezek a könyvtárak a Windows 10 és Windows Server 2016 részét képezik. Akkor lehet a Speech SDK használata nélkül ezek a kódtárak mindaddig, amíg a hangbemeneti eszköz egy mikrofonnal nem használja.

A szükséges beszéd SDK-fájlokat is telepíthető az alkalmazás könyvtárába. Ezzel a módszerrel az alkalmazás közvetlenül hozzáférhet a kódtárakat. Győződjön meg arról, hogy válassza ki a megfelelő verzióját (Win32/x64), amely megfelel az alkalmazás.

| Name (Név) | Függvény
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK-t, natív és felügyelt üzembe helyezéséhez szükséges
| `Microsoft.CognitiveServices.Speech.csharp.dll` | felügyelt üzembe helyezéséhez szükséges

>[!NOTE]
> A (korábbi kiadásokban szállított) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` fájl kiadási 1.3.0 kezdve többé nem szükséges. A funkció most már integrálva van az alap SDK-val.

>[!NOTE]
> A Windows Forms-alkalmazás (.NET-keretrendszer C# ) projekt esetében ellenőrizze, hogy a könyvtárak szerepelnek-e a projekt központi telepítési beállításai között. Ezt a következő szakaszban `Properties -> Publish Section`tekintheti meg:. Kattintson a `Application Files` gombra, és keresse meg a megfelelő könyvtárakat a legördülő listából. Győződjön meg arról, hogy `Included`a érték a következőre van beállítva:. A Visual Studio tartalmazni fogja a fájlt a projekt közzétételekor vagy telepítésekor.

## <a name="linux"></a>Linux

A Speech SDK jelenleg az Ubuntu 16,04, az Ubuntu 18,04 és a Debian 9 disztribúciókat támogatja.
Egy natív alkalmazást, a beszéd SDK-könyvtár szállításra való szüksége `libMicrosoft.CognitiveServices.Speech.core.so`.
Jelölje ki, amely megfelel az alkalmazás verziója (x86, x64). A Linux verziójától függően is szüksége lehet a következő függőségeket tartalmaznak:

* A megosztott szalagtárakkal GNU C-függvénytár (beleértve a POSIX szálak programozási könyvtár `libpthreads`)
* Az OpenSSL-könyvtár`libssl.so.1.0.0` ( `libssl.so.1.0.2`vagy)
* A megosztott szalagtár ALSA alkalmazásokhoz (`libasound.so.2`)

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

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [A beszédfelismerést a C#-ban való használatáról](quickstart-csharp-dotnet-windows.md)
