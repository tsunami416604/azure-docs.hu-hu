---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: dea6a1afaa2348fc5054bee20c534936dcafe5b5
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656600"
---
:::row:::
    :::column span="3":::
        A Beszéd SDK támogatja a Windows 10 és a Windows Server 2016 vagy újabb verziókat. A korábbi verziók hivatalosan **nem** támogatottak. A beszédbeszéd SDK egyes részei a Windows korábbi verzióival használhatók, bár ez nem ajánlott.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Rendszerkövetelmények

A Windows beszédfelismerési SDK használatához a <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ <span class="docon docon-navigate-external x-hidden-focus"></span> újraterjeszthető a Visual Studio 2019-hez</a> a rendszeren.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Telepítés x86-hoz<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Telepítés x64-hez<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Telepítés az ARMx64-hez<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Mikrofonbevitel esetén telepíteni kell a Media Foundation tárakat. Ezek a tárak a Windows 10 és a Windows Server 2016 részét képezik. A beszédfelismerési SDK-t e könyvtárak nélkül is használhatja, feltéve, hogy a mikrofont nem használják hangbemeneti eszközként.

A szükséges beszédsdka-fájlok az alkalmazással azonos könyvtárban telepíthetők. Így az alkalmazás közvetlenül hozzáférhet a könyvtárakhoz. Győződjön meg arról, hogy az alkalmazásnak megfelelő verziót (x86/x64) válassza ki.

| Név                                            | Függvény                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, a natív és felügyelt telepítéshez szükséges |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Felügyelt telepítéshez szükséges                      |

> [!NOTE]
> Az 1.3.0-s kiadástól kezdve a fájlra `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (a korábbi kiadásokban szállítva) már nincs szükség. The functionality is now integrated in the core SDK.

> [!IMPORTANT]
> A Windows Forms app (.NET Framework) C# projekt esetében győződjön meg arról, hogy a könyvtárak szerepelnek a projekt telepítési beállításaiközött. Akkor ellenőrizze ezt `Properties -> Publish Section`a . Kattintson `Application Files` a gombra, és keresse meg a megfelelő könyvtárakat a lefelé görgetési listából. Ellenőrizze, hogy az `Included`érték értéke . A Visual Studio a projekt közzétételekor/telepítésekor tartalmazza a fájlt.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
