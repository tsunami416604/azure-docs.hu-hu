---
title: Hasonlósági módszer – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A hasonlósági módszer használatával kiszámíthatja a két karakterlánc Academic hasonlóságát.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 855d29d2c55b841bbbe4e9eadce8c29ad85fad90
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704855"
---
# <a name="similarity-method"></a>Hasonlósági módszer

A **hasonlóság** REST API a két karakterlánc közötti akadémiai hasonlóság kiszámítására szolgál. 
<br>

**REST-végpont:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>A kérés paraméterei

Paraméter        |Adattípus      |Kötelező | Leírás
----------|----------|----------|------------
**s1**        |Sztring   |Igen  |Összehasonlítandó karakterlánc *
**s2**        |Karakterlánc   |Igen  |Összehasonlítandó karakterlánc *

<sub>* Az összehasonlítandó karakterláncok maximális hossza 1 MB.</sub>
<br>

## <a name="response"></a>Válasz

Name (Név) | Leírás
--------|---------
**SimilarityScore**        |Az S1 és S2 koszinusz hasonlóságát jelképező lebegőpontos érték, amelynek az értéke a 1,0-hoz közelebbi, és közelebb van a-1,0 értékhez, ami kevésbé hasonlít

<br>

## <a name="successerror-conditions"></a>Sikeres/hiba feltételei

HTTP-állapot | Reason | Válasz
-----------|----------|--------
**200**         |Siker | Lebegőpontos szám
**400**         | Hibás kérelem vagy kérelem érvénytelen | Hibaüzenet      
**500**         |Belső kiszolgálóhiba | Hibaüzenet
**Időtúllépés**     | A kérelem túllépte az időkorlátot.  | Hibaüzenet

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Példa: Két részleges absztrakt hasonlóságának kiszámítása
#### <a name="request"></a>Kérés:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Ebben a példában a hasonlóságot a két részleges absztrakt között a **hasonlósági** API használatával generáltuk.
#### <a name="response"></a>Válasz:
```
0.520
```
#### <a name="remarks"></a>Megjegyzéseket tartalmazó
A hasonlósági pontszám meghatározása az akadémiai fogalmak Word-beágyazáson keresztüli értékelésével történik. Ebben a példában a 0,52 azt jelenti, hogy a két részleges absztrakt némileg hasonló.
<br>
