---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 33cc9b52c4b687ca8147867b99dc70e8d71a9223
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88944395"
---
:::row:::
    :::column span="3":::
        A Speech SDK a Windows 10 és a Windows Server 2016-es vagy újabb verzióit támogatja. A korábbi verziók **nem** támogatottak hivatalosan. A Speech SDK egyes részeit a Windows korábbi verzióival is használhatja, bár ez nem ajánlott.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Rendszerkövetelmények

A Windowson futó Speech SDK használatához a <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ újraterjeszthető változata szükséges a Visual <span class="docon docon-navigate-external x-hidden-focus"></span> Studio 2019-hez</a> a rendszeren.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Telepítés x86-ra <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Telepítés x64-re <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Telepítés a ARMx64 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

A mikrofonos bevitelhez telepíteni kell a Multimédia alaprendszer kódtárakat. Ezek a kódtárak a Windows 10 és a Windows Server 2016 rendszer részét képezik. Ezen könyvtárak nélkül is használhatja a Speech SDK-t, ha a mikrofon nem hangbemeneti eszközként van használatban.

A szükséges Speech SDK-fájlokat az alkalmazással megegyező könyvtárba lehet telepíteni. Így az alkalmazás közvetlenül hozzáférhet a könyvtárakhoz. Ügyeljen arra, hogy a megfelelő verziót (x86/x64) válassza, amely megfelel az alkalmazásnak.

| Name (Név)                                            | Függvény                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | A natív és felügyelt központi telepítéshez szükséges Core SDK |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Felügyelt központi telepítéshez szükséges                      |

> [!NOTE]
> A `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (korábbi kiadásokban szállított) fájl kiadási 1.3.0 kezdve többé nem szükséges. A funkció most már integrálva van az alap SDK-val.

> [!IMPORTANT]
> A Windows Forms-alkalmazás (.NET-keretrendszer) C# projekt esetében ellenőrizze, hogy a kódtárak szerepelnek-e a projekt központi telepítési beállításaiban. Ezt a következő szakaszban tekintheti meg: `Properties -> Publish Section` . Kattintson a `Application Files` gombra, és keresse meg a megfelelő könyvtárakat a legördülő listából. Győződjön meg arról, hogy a érték a következőre van beállítva: `Included` . A Visual Studio tartalmazni fogja a fájlt a projekt közzétételekor vagy telepítésekor.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
