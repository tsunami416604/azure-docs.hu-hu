---
title: Csomagok telepítéséhez a Jupyter notebook az Azure-ban
description: Hogyan telepítheti a Python, R, és F# belül Jupyter notebook az Azure-ban a csomagokat.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b0881cb6dac9ec83d2126942c758508e760f9c83
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274422"
---
# <a name="install-packages-from-within-a-notebook"></a>Egy jegyzetfüzetet a csomagok telepítése

Jóllehet konfigurálhat a [környezetet a notebookot a projekt szintjén](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), érdemes közvetlenül belül az egyes jegyzetfüzet-csomagok telepítéséhez.

A notebookból telepített csomagok csak az aktuális kiszolgáló munkamenet érvényesek. Csomag telepítését követően a kiszolgáló leállítása nem megőrzött.

## <a name="python"></a>Python

Python-csomagok pip vagy conda-parancsok használatával a kód cellák segítségével telepíthető:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Ha a parancs kimenete azt jelzi, hogy már teljesülnek a feltételek, majd Azure notebookok tartalmazhatnak a csomag alapértelmezés szerint. Előfordulhat, hogy a csomag is telepítve keresztül egy [projekt környezet telepítési lépés](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Az R-csomagok a Cran vagy a GitHub használatával telepíthetők `install.packages` a kód cellájában lévő függvénnyel:

```r
install.packages("package_name")
```

A GitHubon az előzetes verzió és más fejlesztői csomagok is telepíthetők a devtools könyvtár használatával:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

A csomagok F# telepíthető [nuget.org](https://www.nuget.org) meghívásával Paket függőség-kezelőt a kód cellák. Első lépésként töltse be a Paket manager:

```fsharp
#load "Paket.fsx"
```

Ezután telepítse a csomagokat:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Ezután töltse be a Paket-generátort:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Nyissa meg a libray-t:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>További lépések

- [Útmutató: Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: Bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
