---
title: Csomagok telepítéséhez a Jupyter notebook az Azure-ban
description: Hogyan telepítheti a Python, R, és F# belül Jupyter notebook az Azure-ban a csomagokat.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed57cb458cc1f71c5d6fd2475c54cc712257dd84
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084872"
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

R-csomagok CRAN vagy a Github használatával is telepíthető a `install.packages` egy kódcellába függvény:

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

- [Hogyan: konfigurálása és projektek felügyelete](configure-manage-azure-notebooks-projects.md)
- [Hogyan: diavetítés bemutatásához](present-jupyter-notebooks-slideshow.md)
