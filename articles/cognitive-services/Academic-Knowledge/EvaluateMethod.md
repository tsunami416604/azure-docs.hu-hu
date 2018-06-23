---
title: Academic Knowledge API-ban. a módszer kiértékelésének |} Microsoft Docs
description: A kiértékelés módszert egy lekérdezési kifejezésben, a Microsoft kognitív szolgáltatások alapján academic entitások tért vissza.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 3005ae1f6df042a49db086de4982d8206f6938a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346874"
---
# <a name="evaluate-method"></a>Módszer kiértékelésének

A **kiértékelése** REST API használatával egy lekérdezési kifejezésben alapján academic entitások tért vissza.
<br>

**REST-végpont:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>A kérelemben szereplő paraméterek  
Name (Név)     | Érték | Kötelező?  | Leírás
-----------|-----------|---------|--------
**kifejezés**       | Szöveges karakterlánc | Igen | A lekérdezési kifejezés, amely meghatározza, mely entitások vissza kell adni.
**modell**      | Szöveges karakterlánc | Nem  | A lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték *legújabb*.        
**Attribútumok** | Szöveges karakterlánc | Nem<br>alapértelmezett: azonosítója | Adja meg a válaszban szereplő attribútumértékek vesszővel tagolt listája. Attribútumnevek megkülönböztetik a kis-és nagybetűket.
**száma**        | Szám | Nem<br>Alapértelmezett: 10 | Visszaadandó eredmények száma.
**Az offset**     | Szám |   Nem<br>Alapértelmezett: 0    | Az első eredmény indexe való visszatéréshez.
**OrderBy** |   Szöveges karakterlánc | Nem<br>Alapértelmezett: csökkentésével valószínűség | Az entitások rendezéshez használt attribútum neve. Igény szerint növekvő/csökkenő is megadható. A formátum: *name: asc* vagy *name: desc*.
  
 <br>
## <a name="response-json"></a>Válasz (JSON)
Name (Név) | Leírás
-------|-----   
**kifejezés** |  A *kifejezés* paramétert a kérésből.
**Entitások** |  0 vagy több entitás, amely megfelel a lekérdezési kifejezésben tömbjét. Minden entitás természetes napló valószínűségi érték és a többi kért attribútumainak értékeit tartalmazza.
**megszakadt** | Értéke TRUE, ha a kérelem túllépte az időkorlátot.

<br>
#### <a name="example"></a>Példa:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Általában egy kifejezés szerzi be a válasz a **értelmezhetők** metódust.  De is létrehozhat lekérdezési kifejezések saját kezűleg (lásd: [lekérdezési kifejezés szintaxisa adható](QueryExpressionSyntax.md)).  
  
Használja a *száma* és *eltolás* paraméterek, az nagy számú eredmény szerezhető anélkül, hogy egy túl nagy (és esetleg lassú) eredmények válasz küldését az egy kérelemhez.  Ebben a példában a kérelmet az első értelmezési használja a kifejezés a **értelmezhetők** API választ, mint a *kifejezés* érték. A *count = 2* paraméter határozza meg, hogy 2 entitás eredmények kérnek. És a *attribútumok Ti, Y, CC, AA =. AuN, AA. AuId* paraméter azt jelzi, hogy a cím, év, hivatkozás száma, Szerző és Szerző azonosító kért minden eredmény.  Lásd: [Entitásattribútumhoz](EntityAttributes.md) attribútumok listáját.
  
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
 
