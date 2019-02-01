---
title: Hasonlósági módszer – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Hasonlósági módszert használja a két karakterlánc tudományos hasonlóságot kiszámításához.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 0e42d5e05268d4a572376f2bdf25d0ce6f8f4be7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490124"
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
**s1**        |String   |Igen  |Összehasonlítandó karakterlánc *
**s2**        |String   |Igen  |Összehasonlítandó karakterlánc *
<sub> * Karakterláncok összehasonlítására rendelkezik egy legfeljebb 1MB. </sub>
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
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Példa: Hasonlósági, két részben kivonatok számítása
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
