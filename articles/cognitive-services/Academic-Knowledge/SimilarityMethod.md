---
title: Hasonlósági módszer – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Hasonlósági módszert használja a két karakterlánc tudományos hasonlóságot kiszámításához.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 76e86eb78a06d98e3d5c6c54b244add3c0c245d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900461"
---
# <a name="similarity-method"></a>Hasonlósági metódus

A **hasonlóság** két karakterlánc tudományos koszinuszhasonlóságát kiszámításához használt REST API-t. 
<br>

**REST-végpont:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>A kérés paraméterei
Paraméter        |Adattípus      |Szükséges | Leírás
----------|----------|----------|------------
**S1**        |Sztring   |Igen  |Összehasonlítandó karakterlánc *
**S2**        |Sztring   |Igen  |Összehasonlítandó karakterlánc *
<sub> * Karakterláncok összehasonlítására van egy 1MB maximális hosszát. </sub>
<br>
## <a name="response"></a>Válasz
Name (Név) | Leírás
--------|---------
**SimilarityScore**        |Lebegőpontos érték, s1 és s2 értékek közelebb több hasonló 1.0-ás jelentése és értékeket közelebb-1.0, ami azt jelenti, kisebb cosine hasonlóságot
<br>

## <a name="successerror-conditions"></a>Sikeres/meghiúsult feltételek
HTTP-állapot | Ok | Válasz
-----------|----------|--------
**200**         |Sikeres | Lebegőpontos szám
**400**         | Hibás kérés vagy a kérelem érvénytelen | Hibaüzenet      
**500**         |Belső kiszolgálóhiba | Hibaüzenet
**Túllépte az időkorlátot**     | A kérelem túllépte az időkorlátot.  | Hibaüzenet
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Példa: Két részleges kivonatok hasonlóságot kiszámítása
#### <a name="request"></a>Kérés:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Ebben a példában az általunk létrehozott között két részleges kivonatok használatával a hasonlóság pontszám a **hasonlóság** API-t.
#### <a name="response"></a>Válasz:
```
0.520
```
#### <a name="remarks"></a>Megjegyzés:
A hasonló elemek keresztül word beágyazás academic fogalmak meghatározása határozza meg. Ebben a példában a 0.52 azt jelenti, hogy a két részleges kivonatok valamelyest hasonlóak.
<br>