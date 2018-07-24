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
ms.openlocfilehash: a960197c89e1d051de0e9b8b73cbab231982cf52
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216503"
---
# <a name="sample-of-filter-expressions-python"></a>A szűrőkifejezések (Python) minta 
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
