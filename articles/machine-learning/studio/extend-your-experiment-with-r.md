---
title: A kísérletek bővítése R-rel
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning Studio (klasszikus) funkcióinak kiterjesztése az R-nyelven az R-parancsfájl végrehajtása modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218038"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klasszikus): Bővítse ki a kísérletet r 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Az Azure Machine Learning Studio (klasszikus) funkcióit az R-nyelven kiterjesztheti az [R-parancsfájl végrehajtása][execute-r-script] modul használatával.

Ez a modul több bemeneti adatkészletet fogad el, és egyetlen adatkészletet eredményez kimenetként. Az R-parancsfájl t az [R-parancsfájl végrehajtása][execute-r-script] modul **R-parancsfájl** paraméterébe is beírhat.

A modul minden bemeneti portját a következőhöz hasonló kóddal érheti el:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Az összes jelenleg telepített csomag felsorolása
A telepített csomagok listája változhat. A jelenleg telepített csomagok listája megtalálható az [Azure Machine Learning Studio által támogatott R csomagokban (klasszikus).](https://msdn.microsoft.com/library/azure/mt741980.aspx)

A telepített csomagok teljes, aktuális listáját is beszerezheti, ha beírja a következő kódot az [R-parancsfájl végrehajtása][execute-r-script] modulba:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Ezzel elküldi a csomagok listáját az [R-parancsfájl végrehajtása][execute-r-script] modul kimeneti portjára.
A csomaglista megtekintéséhez csatlakoztasson egy konverziós modult, például a [Konvertálás CSV-re][convert-to-csv] az [R-parancsfájl végrehajtása][execute-r-script] modul bal oldali kimenetéhez, futtassa a kísérletet, majd kattintson a konverziós modul kimenetére, és válassza a **Letöltés**lehetőséget. 

![A "Konvertálás CSV-re" modul letöltése](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Csomagok importálása
A még nem telepített csomagok at az [R parancsfájl végrehajtása][execute-r-script] modul következő parancsaival importálhatja:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

ahol `my_favorite_package.zip` a fájl tartalmazza a csomagot.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
