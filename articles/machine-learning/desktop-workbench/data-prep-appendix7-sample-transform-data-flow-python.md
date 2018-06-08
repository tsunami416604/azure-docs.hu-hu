---
title: Példa átalakító adatfolyam-átalakításokat tesz lehetővé Azure Machine Learning adatok előkészítése |} Microsoft Docs
description: Ez a dokumentum számos példát átalakítási adatok folyamata átalakítások lehetséges az Azure Machine Learning adatok előkészítése
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 655e9d41911fbb008470cf58b2538407933787bd
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832074"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Egyéni adatok folyamata átalakítások (Python) minta 
A menü átalakító neve **átalakítási Adatfolyamblokk (parancsfájl)**. Ahhoz, hogy olvassa el a jelen függelék, olvassa el a [Python bővítési áttekintése](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Keret átalakítása
### <a name="create-a-new-column-dynamically"></a>Egy olyan új oszlop létrehozása dinamikusan 
Egy oszlop dinamikusan jön létre (**city2**) és egy, a meglévő város oszlopból egyezteti a San Francisco több különböző verziói.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Új összesítések hozzáadása
Az első és utolsó összesíti a pontszám oszlop számított hoz létre egy új keret. Ezek szerint vannak csoportosítva a **risk_category** oszlop.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Egy oszlop Winsorize 
Reformulates képlet csökkenti a kiugró egy oszlop felel meg az adatokat.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Átalakítás adatfolyama
### <a name="fill-down"></a>Lefelé kitölti 

Kitöltés lefelé két átalakítások igényel. Azt feltételezi, hogy a következő táblázat a következőképpen néz adatokat:

|Állapot         |Város       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquahi   |
|              |Seattle    |
|Kalifornia    |Los Angeles|
|              |San Diego  |
|              |San Jose   |
|Texas         |Dallas     |
|              |San Antonio|
|              |Houston    |

1. Hozzon létre egy "Oszlop (parancsfájl) hozzáadása" átalakító, a következő kódot:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Hozzon létre egy "Átalakító adatfolyama (parancsfájl)" átalakító, amely tartalmazza a következő kódot:
```python
    df = df.fillna( method='pad')
```

Az adatok mostantól néz ki a következő táblázatban:

|Állapot         |newState         |Város       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquahi   |
|              |Washington    |Seattle    |
|Kalifornia    |Kalifornia    |Los Angeles|
|              |Kalifornia    |San Diego  |
|              |Kalifornia    |San Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |


### <a name="min-max-normalization"></a>Minimális-maximális értéke
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```