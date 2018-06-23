---
title: Academic Knowledge API-ja hasonlóság metódus |} Microsoft Docs
description: A hasonló alkalmazásesemények módszerrel kognitív Microsoft-szolgáltatásokban két karakterláncok academic hasonlóság kiszámításához.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 472498d6bfe06ae4477a30f892d44e79c901acf5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346879"
---
# <a name="similarity-method"></a>Hasonlóság módszer

A **hasonlóság** REST API két karakterláncok közötti academic hasonlóság számításához. 
<br>

**REST-végpont:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>A kérelemben szereplő paraméterek
Paraméter        |Adattípus      |Szükséges | Leírás
----------|----------|----------|------------
**S1**        |Sztring   |Igen  |Össze kell hasonlítania karakterlánc *
**s2**        |Sztring   |Igen  |Össze kell hasonlítania karakterlánc *
<sub> * Az összehasonlítandó karakterláncok 1MB maximális hossza rendelkezik. </sub>
<br>
## <a name="response"></a>Válasz
Name (Név) | Leírás
--------|---------
**SimilarityScore**        |Egy lebegőpontos érték a koszinusz hasonlóság s1 és s2 értékek közelebb 1.0-ás jelentése több hasonló és értékek közelebb tehát kisebb-1.0
<br>

## <a name="successerror-conditions"></a>Sikeres/hibaállapotok
HTTP-állapot | Ok | Válasz
-----------|----------|--------
**200**         |Sikeres | Lebegőpontos szám
**400**         | Helytelen kérelem vagy a kérelem érvénytelen | Hibaüzenet      
**500**         |Belső kiszolgálóhiba | Hibaüzenet
**Túllépte az időkorlátot**     | A kérelem túllépte az időkorlátot.  | Hibaüzenet
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Példa: A két részleges kivonatok hasonlóság kiszámítása
#### <a name="request"></a>A kérelem:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Ebben a példában a hasonlóság pontszám között két részleges kivonatok használatával azt létrehozni a **hasonlóság** API.
#### <a name="response"></a>Válasz:
```
0.520
```
#### <a name="remarks"></a>Megjegyzés:
A hasonló alkalmazásesemények értékeli a word-beágyazás academic fogalmak határozza meg. Ebben a példában 0.52 azt jelenti, hogy a két részleges kivonatok némileg hasonló.
<br>