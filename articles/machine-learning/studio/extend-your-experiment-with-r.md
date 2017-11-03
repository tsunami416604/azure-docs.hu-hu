---
title: "Kiterjeszti a az R nyelv használatával |} Microsoft Docs"
description: "How Azure Machine Learning Studio funkcióit az R nyelv használatával kiterjesztése az R-parancsfájl végrehajtása modul használatával."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: e9f11ec987a5dd71998f6b23399228554f1dcc11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-experiment-with-r"></a>A kísérletek bővítése R-rel
Azure Machine Learning Studio funkcióit keresztül az R nyelv használatával kiterjesztheti a [R-parancsfájl végrehajtása] [ execute-r-script] modul.

Ez a modul több bemeneti adatkészletek fogad, és egyetlen dataset kimenetként eredményez. Az R-parancsfájl beírhatja a **R-parancsfájl** paramétere a [R-parancsfájl végrehajtása] [ execute-r-script] modul.

Minden modul bemeneti portját a következőhöz hasonló kód használatával éri el:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Az összes jelenleg telepített csomagok listázása
Módosíthatja a telepített csomagok listáját. A jelenleg telepített csomagok listáját található [R csomagok támogatott Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Is kaphat a teljes, a jelenlegi telepített csomagok listáját az alábbi kódot írja be a [R-parancsfájl végrehajtása] [ execute-r-script] modul:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Ez a csomagok listájában küld kimeneti portját a [R-parancsfájl végrehajtása] [ execute-r-script] modul.
Tekintse meg a csomag listáját, csatlakoztassa a átalakítás modult például [CSV átalakítása] [ convert-to-csv] a bal oldali kimeneti a [R-parancsfájl végrehajtása] [ execute-r-script] modul, Futtassa a kísérletet, majd kattintson a konverziós modul kimenete, és válassza ki **letöltése**. 

![Töltse le a "CSV való átalakítása" modul kimenete](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Csomagok importálása
Csomagok, amelyek még nincsenek telepítve a következő parancsok segítségével importálhatja a [R-parancsfájl végrehajtása] [ execute-r-script] modul:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Ha a `my_favorite_package.zip` fájl tartalmazza a csomag.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
