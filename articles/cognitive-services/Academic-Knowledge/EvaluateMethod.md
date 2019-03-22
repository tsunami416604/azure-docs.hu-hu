---
title: Módszer – Academic Knowledge API kiértékelése
titlesuffix: Azure Cognitive Services
description: Az Evaluate módszert a lekérdezés alapján tudományos entitások egy készletét adja vissza.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d2e628fb7fc502ef9ba81d20680d66f24fd7d138
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004929"
---
# <a name="evaluate-method"></a>Módszer kiértékelése

A **kiértékelése** REST API segítségével a lekérdezés alapján tudományos entitások egy készletét adja vissza.
<br>

**REST-végpont:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>A kérés paraméterei  

Name (Név)     | Érték | Kötelező?  | Leírás
-----------|-----------|---------|--------
**kifejezés**       | Szöveges karakterlánc | Igen | A lekérdezési kifejezés, amely meghatározza, mely entitások vissza kell adni.
**model**      | Szöveges karakterlánc | Nem  | A lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték *legújabb*.        
**Attribútumok** | Szöveges karakterlánc | Nem<br>alapértelmezett érték: Azonosító | Egy vesszővel tagolt listája, amely meghatározza az attribútum értékei, amelyek szerepelnek a választ. Attribútumnevek-és nagybetűk.
**count**        | Szám | Nem<br>Alapértelmezett: 10 | Visszaadott eredmények száma.
**eltolás**     | Szám |   Nem<br>Alapértelmezett: 0    | Az első eredmény index való visszatéréshez.
**OrderBy** |   Szöveges karakterlánc | Nem<br>Alapértelmezés: csökkenő valószínűség szerint | Az entitások rendezéshez használt attribútum neve. Igény szerint növekvő vagy csökkenő adható meg. A formátum: *name: asc* vagy *name: desc*.
  
 <br>

## <a name="response-json"></a>Válasz (JSON)

Name (Név) | Leírás
-------|-----   
**kifejezés** |  A *expr* paraméter a kérelemből.
**Entitások** |  0 vagy több entitás, amely megfelel a lekérdezési kifejezés tömbje. Minden entitás tartalmaz egy természetes logaritmusát valószínűségi érték és egyéb kért attribútumának értéke.
**aborted** | IGAZ, ha a kérelem túllépte az időkorlátot.

<br>

#### <a name="example"></a>Példa:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Általában egy kifejezés szerzi be a választ a **értelmezése** metódust.  De is létrehozhat lekérdezési kifejezések magát (lásd: [lekérdezési kifejezés szintaxisa](QueryExpressionSyntax.md)).  
  
Használatával a *száma* és *eltolás* paramétereket, az eredmények nagy számú beszerezhetők egyetlen kérelem küldése a eredményeket egy hatalmas (és esetleg lassan) válaszban nélkül.  Ebben a példában a kérés az első értelmezése használt kifejezés a **értelmezése** API-válasz, a *expr* értéket. A *száma = 2* paraméter határozza meg, hogy 2 entitás eredmények kérnek. És a *attribútumok Ti, Y, CC, AA =. AuN, AA. AuId* paraméter azt jelzi, hogy a cím, év, idézet száma, Szerző neve és Szerző azonosító minden eredmény kérik.  Lásd: [tevékenységentitás-attribútumok](EntityAttributes.md) attribútumok listáját.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
