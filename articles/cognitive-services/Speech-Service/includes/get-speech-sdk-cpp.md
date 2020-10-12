---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399960"
---
:::row:::
    :::column span="3":::
        A C++ Speech SDK Windows, Linux és macOS rendszeren érhető el. További információ: <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. CognitiveServices. Speech <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet-csomag

A C++ Speech SDK a következő paranccsal telepíthető a **Package managerből** `Install-Package` .

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++ bináris fájlok és fejlécek

Másik lehetőségként a C++ Speech SDK telepíthető bináris fájlokból is. Töltse le az SDK-t <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">. tar <span class="docon docon-navigate-external x-hidden-focus"></span> -csomagként</a> , és csomagolja ki a fájlokat egy tetszőleges könyvtárba. A csomag tartalma (amely tartalmazza az x86-és x64-es megcélzott architektúrák fejléc-fájljait is) a következőképpen strukturált:

  | Elérési út                   | Leírás                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licenc                                              |
  | `ThirdPartyNotices.md` | Harmadik felekkel kapcsolatos közlemények                                  |
  | `include`              | A C++ fejléc fájljai                                 |
  | `lib/x64`              | Natív x64-es függvénytár az alkalmazással való összekapcsoláshoz |
  | `lib/x86`              | Natív x86-függvénytár az alkalmazással való összekapcsoláshoz |

  Alkalmazás létrehozásához másolja vagy helyezze át a szükséges bináris fájlokat (és könyvtárakat) a fejlesztői környezetbe. A létrehozási folyamat során szükség szerint vegye fel őket.

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux és macOS gyors C++ forráskód <span class="docon docon-navigate-external x-hidden-focus"></span></a>