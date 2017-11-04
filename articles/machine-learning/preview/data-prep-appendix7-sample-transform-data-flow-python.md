---
title: "Példa átalakító adatfolyam-átalakításokat tesz lehetővé Azure Machine Learning adatok előkészítése |} Microsoft Docs"
description: "Ez a dokumentum számos példát átalakítási adatok folyamata átalakítások lehetséges az Azure Machine Learning adatok előkészítése"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 5491548885709c1c1048e45d699ef385a7c49a74
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
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
Kitöltés lefelé két átalakítások igényel. Feltételezi, következőhöz hasonló, a következő adatokat:


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

Először hozzon létre egy oszlop hozzáadása (parancsfájl) átalakító, amely tartalmazza a következő kódot:
```python
    row['State'] if len(row['State']) > 0 else None
```
Data Flow átalakítás (parancsfájl) átalakító, amely tartalmazza a következő kódot létrehozni:
```python
    df = df.fillna( method='pad')
```

Az adatok mostantól a következőképpen néznek:

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

