---
title: Csomagok telepítéséhez a Jupyter notebook az Azure-ban
description: Hogyan telepítheti a Python, R, és F# belül Jupyter notebook az Azure-ban a csomagokat.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 5d85c8e936ce7c8bf38ec7bc9c27d9066cc8b155
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277532"
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

Az R-csomagok a CRAN vagy a GitHub használatával telepíthetők a Code cella `install.packages` függvényével:

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

## <a name="next-steps"></a>Következő lépések

- [Hogyan: konfigurálása és projektek felügyelete](configure-manage-azure-notebooks-projects.md)
- [Hogyan: diavetítés bemutatásához](present-jupyter-notebooks-slideshow.md)
