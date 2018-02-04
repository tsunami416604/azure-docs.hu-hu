---
title: "Az Azure Machine Learning adatok előkészítése az új oszlopok való származtatás Python minta |} Microsoft Docs"
description: "Ez a dokumentum példákat Python kódját Azure Machine Learning adatok előkészítése az új oszlopok létrehozásához."
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
ms.openlocfilehash: 128a4ae124f01a0514160450a3e9069b488274cd
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>A minta a egyéni oszlop átalakítások (Python) 
A menüben a transzformáció neve **oszlop hozzáadása (parancsfájl)**.

Ahhoz, hogy olvassa el a jelen függelék, olvassa el a [Python bővítési áttekintése](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Alkalmasként tesztelése, és cserélje le az értékeket 
Ha Oszlop1 érték kisebb, mint 4, majd az új oszlop értéke 1 kell rendelkeznie. Ha az Oszlop1 4-nél több, az új oszlop értéke 2. 

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
## <a name="evaluate-for-nullness"></a>A nullness kiértékelése 
Ha Oszlop1 egy NULL értéket tartalmaz, majd jelölje meg az új oszlopot numerikusként **rossz**. Ha nem, jelölje meg **helyes.** 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Új számított oszlop 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch számítási 
A Unix Epoch (feltételezve Oszlop1 már egy date) óta eltelt percek száma: 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>Egy új oszlopba kivonat-oszlop értéke
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




