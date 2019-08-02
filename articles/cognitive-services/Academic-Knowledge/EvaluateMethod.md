---
title: Módszer kiértékelése – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A kiértékelési módszer használatával a lekérdezési kifejezés alapján tudományos entitások készletét adhatja vissza.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 69e701d6727e5410b71e6cf8fbe20a1cd038ddb0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705009"
---
# <a name="evaluate-method"></a>Módszer kiértékelése

A **kiértékelés** REST API egy lekérdezési kifejezésen alapuló akadémiai entitások egy készletének visszaadására szolgál.
<br>

**REST-végpont:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>A kérés paraméterei  

Name (Név)     | Value | Kötelező?  | Leírás
-----------|-----------|---------|--------
**kifejezés**       | Szöveges karakterlánc | Igen | Egy lekérdezési kifejezés, amely megadja, hogy mely entitásokat adja vissza.
**modell**      | Szöveges karakterlánc | Nem  | A lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték a *legújabb*.        
**attribútumok** | Szöveges karakterlánc | Nem<br>alapértelmezett Id | Vesszővel tagolt lista, amely meghatározza a válaszban szereplő attribútum-értékeket. Az attribútumok nevei megkülönböztetik a kis-és nagybetűket.
**count**        | Number | Nem<br>Alapértelmezett: 10 | A visszaadni kívánt eredmények száma.
**eltolás**     | Number |   Nem<br>Alapértelmezett: 0    | A visszatérés első eredményének indexe.
**OrderBy** |   Szöveges karakterlánc | Nem<br>Alapértelmezett: a szonda csökkentésével | Az entitások rendezéséhez használt attribútum neve. Igény szerint növekvő/csökkenő értéket is megadhat. A formátum: *név: ASC* vagy *Name: desc*.
  
 <br>

## <a name="response-json"></a>Válasz (JSON)

Name (Név) | Leírás
-------|-----   
**kifejezés** |  A kérelemben szereplő *kifejezés* paraméter.
**szervezetek** |  A lekérdezési kifejezésnek megfelelő 0 vagy több entitás tömbje. Minden entitás tartalmaz egy természetes naplóbeli valószínűségi értéket és a többi kért attribútum értékét.
**megszakadt** | Értéke true, ha a kérelem időtúllépést adott meg.

<br>

#### <a name="example"></a>Példa:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Általában egy kifejezés fog megjelenni az értelmező metódusra adott válasz alapján.  Saját maga is létrehozhat lekérdezési kifejezéseket (lásd: [lekérdezési kifejezés szintaxisa](QueryExpressionSyntax.md)).  
  
A *darabszám* és az *eltolás* paraméterek használatával nagy mennyiségű eredményt lehet elérni anélkül, hogy egyetlen kérést kellene elküldeni, amely hatalmas (és potenciálisan lassú) választ eredményez.  Ebben a példában a kérelem a értelmező API-válasz első értelmezésének kifejezését használta *kifejezés értékeként* . A *Count = 2* paraméter azt adja meg, hogy a rendszer 2 entitási eredményt kér. És az *attribútumok = ti, Y, CC, AA. AuN, AA. A AuId* paraméter azt jelzi, hogy a címet, az évet, az idézetek darabszámát, a szerző nevét és a szerző azonosítóját a rendszer minden eredményre kéri.  Az attribútumok listáját az [entitás attribútumaiban](EntityAttributes.md) tekintheti meg.
  
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
 
