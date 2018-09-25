---
title: Az Azure Machine Learning adat-előkészítési lehetséges példa szűrőkifejezésekben |} A Microsoft Docs
description: Ez a dokumentum számos új példák az Azure Machine Learning adat-előkészítési lehetőségeinek megismerése
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: a389007dea344de461b23b96f2942686005aa119
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982392"
---
# <a name="sample-of-filter-expressions-python"></a>A szűrőkifejezések (Python) minta 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Mielőtt, olvassa el a jelen függelék, olvassa el a [Python bővíthetőség áttekintése](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Egyenértékűségi teszt szűrése
Szűrés csak olyan sorok, ahol (numerikus) Col2 értéke nagyobb, mint a 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Több oszlop szűrése 
Csak olyan ahol Col1 értéket tartalmazza sorok szűrése **jó** és Col2 1 (numerikus) értéket tartalmazza. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Teszt szűrő null ellen
Szűrés csak azok a sorok ahol Col1 egy null értékű. 
```python
    pd.isnull(row["Col1"])
```
