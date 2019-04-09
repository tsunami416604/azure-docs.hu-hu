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
ms.openlocfilehash: 504158f248cde3a399475cdec99de3e6a4ebfcc5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276881"
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

R-csomagok CRAN vagy a GitHub használatával is telepíthető a `install.packages` egy kódcellába függvény:

```r
install.packages("package_name")
```

Előzetes verziók és más fejlesztési csomagok a Githubról a devtools kódtár használatával is telepítheti:

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

## <a name="next-steps"></a>További lépések

- [Útmutató: Konfigurálhatja és kezelheti a projektek](configure-manage-azure-notebooks-projects.md)
- [Útmutató: Diavetítés bemutatása](present-jupyter-notebooks-slideshow.md)
