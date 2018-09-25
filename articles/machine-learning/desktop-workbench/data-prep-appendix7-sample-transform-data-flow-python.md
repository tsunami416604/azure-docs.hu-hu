---
title: Példa átalakító adatfolyam lehetséges az Azure Machine Learning adat-előkészítési átalakítások |} A Microsoft Docs
description: Ez a dokumentum számos új példák átalakítási adatok folyamatot átalakítások lehetséges az Azure Machine Learning adat-előkészítés
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
ms.openlocfilehash: 82295e412c70065ff8ce3a686aec790614f39f2e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947200"
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Egyéni adatok folyamatot átalakítások (Python) minta 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


A menüben megtekintheti az átalakítás neve **adatfolyam átalakítása (szkript)**. Mielőtt, olvassa el a jelen függelék, olvassa el a [Python bővíthetőség áttekintése](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Keret átalakítása
### <a name="create-a-new-column-dynamically"></a>Dinamikusan egy olyan új oszlop létrehozása 
Oszlop dinamikusan jön létre (**város2**) és egy, a meglévő Város oszlop összehangolja a San Franciscóban több különböző verzióit.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Új összesítések hozzáadása
A kiszámított pontszámot oszlop első és utolsó összesítések hoz létre egy új keretet. Ezek csoportosítva a **risk_category** oszlop.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Egy oszlop Winsorize 
Reformulates csökkentése érdekében a kiugró értékek egy oszlopot a képlet az adatokat.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Adatfolyam átalakítása
### <a name="fill-down"></a>Kitöltés lefelé 

Kitöltés lefelé két átalakítások van szükség. Feltételezi, hogy néz ki a következő táblázat adatainak:

|Állapot         |Város       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquahi   |
|              |Seattle    |
|Kalifornia    |Los Angeles|
|              |A San Diego  |
|              |San Jose   |
|Texas         |Dallas     |
|              |A San Antonio|
|              |Houston    |

1. Hozzon létre egy "Hozzáadása (szkript) oszlop" átalakítás az alábbi kód használatával:
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Hozzon létre egy "Átalakító adatfolyama (szkript)" átalakítás, amely a következő kódot tartalmazza:
```python
    df = df.fillna( method='pad')
```

Az adatok most már a következő táblázat hasonlóan néz ki:

|Állapot         |Új állapot         |Város       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquahi   |
|              |Washington    |Seattle    |
|Kalifornia    |Kalifornia    |Los Angeles|
|              |Kalifornia    |A San Diego  |
|              |Kalifornia    |San Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |A San Antonio|
|              |Texas         |Houston    |


### <a name="min-max-normalization"></a>Min-max normalizálási
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```