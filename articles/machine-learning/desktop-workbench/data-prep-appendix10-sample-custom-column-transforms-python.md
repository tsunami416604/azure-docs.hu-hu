---
title: Minta Python for Azure Machine Learning adat-előkészítés az új oszlopok származtatás |} A Microsoft Docs
description: Ez a dokumentum a új oszlopok létrehozásához az Azure Machine Learning adat-előkészítési hitelesítésikód-példák Python nyújt.
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
ms.openlocfilehash: 43a66efde2623a848065bbde2be4b56fd6c7fc8b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969308"
---
# <a name="sample-of-custom-column-transforms-python"></a>Példák egyéni oszlopátalakításokra (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Ez a menü átalakító neve **oszlop hozzáadása (szkript)**.

Mielőtt, olvassa el a jelen függelék, olvassa el a [Python bővíthetőség áttekintése](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Egyenértékűségi tesztelése, és cserélje le az értékeket 
Ha az Oszlop1 kevesebb mint 4, az új oszlop 1 értékkel kell rendelkeznie. Ha az Oszlop1 több mint 4, az új oszlop értéke 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Aktuális dátum és idő 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Typecasting 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Nullness kiértékeléséhez 
Ha Col1 egy NULL értéket tartalmaz, majd jelölje meg az új oszlopot **hibás**. Ha nem, akkor jelölje meg **jó.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Új számított oszlop 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Alapidőpont számítási 
A Unix alapidőpont (feltételezve Col1 már egy date) óta eltelt percek száma: 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Egy oszlop értékét kivonata be egy olyan új oszlop
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




