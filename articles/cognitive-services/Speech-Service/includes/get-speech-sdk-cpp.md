---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 296dea2e92d494cb9feaeb9f0c942b6a7da57abb
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656489"
---
:::row:::
    :::column span="3":::
        A C++ speech SDK Windows, Linux és macOS rendszeren érhető el. További információt a <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech című témakörben talál. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet csomag

A C++ speech SDK a **csomagkezelőből** `Install-Package` a következő paranccsal telepíthető.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++ bináris fájlok és fejlécfájlok

Másik lehetőségként a C++ speech SDK bináris fájlokból is telepíthető. Töltse le az <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">SDK-t .tar csomagként, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> és csomagolja ki a fájlokat egy ön által választott könyvtárba. A csomag tartalma (amely mind az x86-os, mind az x64-es célarchitektúrák fejlécfájljait tartalmazza) a következőképpen épül fel:

  | Útvonal                   | Leírás                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licenc                                              |
  | `ThirdPartyNotices.md` | Harmadik felekkel kapcsolatos közlemények                                  |
  | `include`              | C++ fejlécfájljai                                 |
  | `lib/x64`              | Natív x64-es könyvtár az alkalmazással való összekapcsoláshoz |
  | `lib/x86`              | Natív x86-os könyvtár az alkalmazással való összekapcsoláshoz |

  Alkalmazás létrehozásához másolja vagy helyezze át a szükséges bináris fájlokat (és könyvtárakat) a fejlesztői környezetbe. Szükség szerint adja meg őket a létrehozási folyamatba.

#### <a name="additional-resources"></a>További források

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux és macOS gyorsindítás C++ forráskód<span class="docon docon-navigate-external x-hidden-focus"></span></a>