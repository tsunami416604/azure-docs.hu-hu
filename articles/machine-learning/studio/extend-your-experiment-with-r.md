---
title: Kísérlet kiterjesztése az R |} A Microsoft Docs
description: Az R nyelv használatával az Azure Machine Learning Studio funkcióinak bővítése R-szkript végrehajtása-modul segítségével hogyan.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 30ed2a71fcf00a511802afb5b126829b918615d6
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263060"
---
# <a name="extend-your-experiment-with-r"></a>A kísérletek bővítése R-rel
Az Azure Machine Learning Studio funkciójának keresztül az R nyelv használatával kiterjesztheti a [R-szkript végrehajtása] [ execute-r-script] modul.

Ez a modul több bemeneti adatkészletet fogad, és egyetlen adatkészlet kimenetként eredményez. Beírhatja az R-szkriptet a **R-szkript** paraméterében a [R-szkript végrehajtása] [ execute-r-script] modul.

Minden egyes a modul bemeneti portjával hozzá a következő kód használatával:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Az összes jelenleg telepített csomagok listázása
Módosíthatja a telepített csomagok listáját. A jelenleg telepített csomagok listáját található [R csomagok az Azure Machine Learning által támogatott](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Emellett megtekintheti a teljes, a jelenlegi telepített csomagok listáját, írja be az alábbi kódot a [R-szkript végrehajtása] [ execute-r-script] modul:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Ez a csomagok listájában küld a kimeneti portjára, a [R-szkript végrehajtása] [ execute-r-script] modul.
Tekintse meg a csomag listáját, csatlakoztassa a konverziós modult például [átalakítás fürt megosztott Kötetévé] [ convert-to-csv] a bal oldali-kimenetre a [R-szkript végrehajtása] [ execute-r-script] modult, Futtassa a kísérletet, majd a konverzió a modul kimenetére, és válassza ki **letöltése**. 

![Töltse le a "Konvertálni a fürt megosztott kötetei szolgáltatás" modul kimenete](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Csomagok importálása
Csomagok, amelyek még nincsenek telepítve a következő parancsok használatával importálhatja a [R-szkript végrehajtása] [ execute-r-script] modul:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

Ha a `my_favorite_package.zip` fájl tartalmazza a csomagot.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
