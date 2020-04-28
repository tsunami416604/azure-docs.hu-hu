---
title: Csomagok telepítése a Jupyter-jegyzetfüzetekben – Azure Notebooks előzetes verzió
description: 'Megtudhatja, hogyan telepíthet Python-, R-és F #-csomagokat az Azure-ban futó Jupyter-jegyzetfüzetből.'
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646228"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Csomagok telepítése Azure Notebooks előzetes verzióból

Bár a projekt szintjén konfigurálhatja a [notebook környezetét](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), érdemes lehet csomagokat közvetlenül egy adott jegyzetfüzeten belül telepíteni.

A jegyzetfüzetből telepített csomagok csak az aktuális kiszolgálói munkamenetre érvényesek. A csomag telepítése nem szűnik meg, ha a kiszolgáló leáll.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

A Pythonban található csomagok a PIP vagy a Conda használatával telepíthetők a kód celláin belüli parancsok használatával:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Ha a parancs kimenete azt jelzi, hogy a követelmény már teljesül, az Azure Notebooks alapértelmezés szerint a csomagot is tartalmazhatja. Előfordulhat, hogy a csomagot a [Project Environment telepítési lépése](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)is telepíti.

## <a name="r"></a>R

Az R-csomagok a CRAN vagy a GitHub használatával telepíthetők `install.packages` a kód cellájában lévő függvénnyel:

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

Az F # csomagok az [nuget.org](https://www.nuget.org) -ből telepíthetők, ha a Paket-függőség kezelőjét a kód celláiból hívja meg. Először töltse be a Paket Managert:

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

Nyissa meg a könyvtárat:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>További lépések

- [Útmutató: projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: bemutató megjelenítése](present-jupyter-notebooks-slideshow.md)
