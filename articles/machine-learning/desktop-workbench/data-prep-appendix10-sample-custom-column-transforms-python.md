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
ms.openlocfilehash: d9f8bb20325ff15b6ba67253de5e66d1d1d8e643
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645266"
---
# <a name="sample-of-custom-column-transforms-python"></a>Példák egyéni oszlopátalakításokra (Python) 
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




