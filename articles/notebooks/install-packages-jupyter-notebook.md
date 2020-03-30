---
title: Csomagok telepítése Jupyter-jegyzetfüzetekben – Az Azure Notebooks előzetes verzió
description: Ismerje meg, hogyan telepítheti a Python, R és F# csomagokat az Azure-on futó Jupyter-jegyzetfüzetből.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646228"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Csomagok telepítése az Azure Notebookelőzetes verzióból

Bár [a környezetet a jegyzetfüzethez a projekt szintjén](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)is konfigurálhatja, előfordulhat, hogy a csomagokat közvetlenül az egyes jegyzetfüzeteken belül szeretné telepíteni.

A notebookról telepített csomagok csak az aktuális kiszolgálói munkamenetre vonatkoznak. A csomagtelepítések telepítése nem marad meg a kiszolgáló leállítása után.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

A Python-ban lévő csomagok pip vagy conda használatával telepíthetők a kódcellákon belüli parancsok segítségével:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Ha a parancs kimenete azt jelzi, hogy a követelmény már teljesült, majd az Azure Notebooks alapértelmezés szerint tartalmazhatja a csomagot. Előfordulhat, hogy a csomag [a projektkörnyezet beállítási lépésén](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)keresztül is telepíthető.

## <a name="r"></a>R

Az R-ben lévő csomagok a CRAN-ból `install.packages` vagy a GitHub-ból telepíthetők a kódcella függvényével:

```r
install.packages("package_name")
```

A GitHubról is telepítheti a kiadás előtti verziókat és más fejlesztői csomagokat a devtools-könyvtár használatával:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Az F# csomagjai [nuget.org](https://www.nuget.org) a Paket-függőségkezelő kódcellákból történő hívásával telepíthetők. Először töltse be a Paket menedzsert:

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

Ezután töltse be a Paket generátort:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Nyissa meg a könyvtárat:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>További lépések

- [Útmutató: Projektek konfigurálása és kezelése](configure-manage-azure-notebooks-projects.md)
- [Útmutató: Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
