---
title: Előkészíti az adatokat a Machine Learning Data Prep SDK-val az Pythonnal – Azure
description: Ismerje meg, hogyan használhatja az Azure Machine Learning Data Prep SDK Pythonhoz készült betölteni az adatokat a különböző formátumok, átalakítja a további használatához és az adatokat írni az eléréséhez a modellek helyét.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 83373f5d6e4a2227bcafdbc4b25b686b0b8d651d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867187"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Adatok előkészítése az Azure Machine Learning modellezés
 
Adat-előkészítés a machine learning munkafolyamat fontos része. A modellek több pontos és hatékony, ha ők is hozzáférhetnek, amely könnyebben felhasználása formátumú adatokat megtisztítani lesz. 

Az adatok a Python használatával készítheti elő a [az Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Adat-előkészítési folyamat

A fő adat-előkészítési lépéseket a következők:

1. [Adatok betöltése](how-to-load-data.md), amely számos formátumú lehet
2. [Átalakítás](how-to-transform-data.md) őket egy használhatóbbá struktúra
3. [Írási](how-to-write-data.md) az adatokat egy olyan helyre, a modellek

![Adatok előkészítési folyamatának](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>További lépések
Tekintse át az [példa notebook](https://github.com/Microsoft/MSDataPrepDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) az adat-előkészítés az Azure Machine Learning Data Prep SDK használatával.
