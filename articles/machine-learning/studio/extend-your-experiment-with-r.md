---
title: A kísérletek bővítése R-rel
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasszikus) funkcióinak kiterjesztése az R nyelv használatával az R-szkript végrehajtása modullal.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218038"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klasszikus): a kísérlet kiterjesztése az R-vel 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Kiterjesztheti Azure Machine Learning Studio (klasszikus) funkcióit az R nyelvről az [r-szkript végrehajtása][execute-r-script] modul használatával.

Ez a modul több bemeneti adatkészletet fogad el, és kimenetként egyetlen adatkészletet eredményez. Az r-szkriptet az r szkript [végrehajtása][execute-r-script] modul **r script** paraméterében írhatja be.

A modul minden bemeneti portját a következőhöz hasonló kóddal érheti el:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Az összes jelenleg telepített csomag listázása
A telepített csomagok listája változhat. A jelenleg telepített csomagok listája megtalálható a [Azure Machine learning Studio (klasszikus) által támogatott R-csomagokban](https://msdn.microsoft.com/library/azure/mt741980.aspx).

A telepített csomagok teljes listáját a következő kód beírásával is lekérheti az [R-szkript végrehajtása][execute-r-script] modulba:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Ezzel elküldi a csomagok listáját az [R-parancsfájl végrehajtása][execute-r-script] modul kimeneti portjára.
A csomagok listájának megtekintéséhez csatlakoztasson egy konverziós modult, például [konvertálja a CSV][convert-to-csv] -t az [R-parancsfájl végrehajtása][execute-r-script] modul bal oldali kimenetére, futtassa a kísérletet, majd kattintson az átalakítási modul kimenetére, és válassza a **Letöltés**lehetőséget. 

![A "Konvertálás CSV-be" modul kimenetének letöltése](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Csomagok importálása
A még nem telepített csomagokat a következő parancsokkal importálhatja az [R-parancsfájl végrehajtása][execute-r-script] modulban:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

ahol a `my_favorite_package.zip` fájl tartalmazza a csomagot.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
