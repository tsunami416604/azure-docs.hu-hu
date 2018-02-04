---
title: "Példa szűrőkifejezéseket lehetséges az Azure Machine Learning adatok előkészítése |} Microsoft Docs"
description: "Ez a dokumentum egy olyan készlete Példák lehetséges az Azure Machine Learning adatok előkészítése"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: df78e871625f4de406de5ba4ae0fea327b87b01e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-filter-expressions-python"></a>Minta szűrőkifejezéseket (Python) 
Ahhoz, hogy olvassa el a jelen függelék, olvassa el a [Python bővítési áttekintése](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Szűrhet alkalmasként tesztelése
Szűrés csak ezeket ahol (numerikus) Col2 érték nagyobb, mint 4 sorokat. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Több oszlopból álló szűrése 
Szűrés csak azok a sorok ahol Oszlop1 értéke **jó** és Col2 1 (numerikus) értéket tartalmazza. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Teszt szűrő null ellen
Szűrés csak azok a sorok ahol Oszlop1 értéke null. 
```python
    pd.isnull(row["Col1"])
```
