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

Bár a projekt szintjén konfigurálhatja a [notebook környezetét](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), érdemes lehet csomagokat közvetlenül egy adott jegyzetfüzeten belül telepíteni.

A notebookból telepített csomagok csak az aktuális kiszolgáló munkamenet érvényesek. Csomag telepítését követően a kiszolgáló leállítása nem megőrzött.

## <a name="python"></a>Python

Python-csomagok pip vagy conda-parancsok használatával a kód cellák segítségével telepíthető:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Ha a parancs kimenete azt jelzi, hogy már teljesülnek a feltételek, majd Azure notebookok tartalmazhatnak a csomag alapértelmezés szerint. Előfordulhat, hogy a csomagot a [Project Environment telepítési lépése](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)is telepíti.

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

F# A csomagok a [nuget.org](https://www.nuget.org) telepíthetők, ha meghívja a Paket függőség kezelőjét a kód celláiból. Első lépésként töltse be a Paket manager:

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

- [Útmutató: projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
