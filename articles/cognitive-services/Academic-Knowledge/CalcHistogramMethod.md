---
title: CalcHistogram metódus - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A terjesztési tanulmány entitásokban attribútumértékei kiszámításához a CalcHistogram módszert használja.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 915e2e5a67d068c418ce50eee9d84dc66e61ee00
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321291"
---
# <a name="calchistogram-method"></a>CalcHistogram metódus

A **calchistogram** REST API-t a papír entitásokban attribútumértékei terjesztési kiszámítására szolgál.          


**REST-végpont:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>A kérés paraméterei

Name (Név)  |Érték | Kötelező?  |Leírás
-----------|----------|--------|----------
**kifejezés**    |Szöveges karakterlánc | Igen  |A lekérdezési kifejezés, amely meghatározza az entitásokat, amelyen hisztogramok kiszámításához.
**Modell** |Szöveges karakterlánc | Nem |Válassza ki a modellt, amely a lekérdezni kívánt nevét.  Jelenleg az alapértelmezett érték *legújabb*.
**Attribútumok** | Szöveges karakterlánc | Nem<br>alapértelmezett érték: | Egy vesszővel tagolt listája, amely meghatározza az attribútum értékei, amelyek szerepelnek a választ. Attribútumnevek-és nagybetűk.
**count** |Szám | Nem<br>Alapértelmezett: 10 |Visszaadott eredmények száma.
**eltolás**  |Szám | Nem<br>Alapértelmezett: 0 |Az első eredmény index való visszatéréshez.
**Időtúllépés**  |Szám | Nem<br>Alapértelmezett: 1000 |Időkorlát ezredmásodpercben. Csak a az időtúllépési letelte előtt található értelmezés adja vissza.

## <a name="response-json"></a>Válasz (JSON)

Name (Név) | Leírás
--------|---------
**kifejezés**  |A kifejezés paraméter a kérelemből.
**num_entities** | Egyező entitások teljes száma.
**hisztogramok** |  Hisztogramok, minden attribútum a kérelemben megadott egy tömbje.
**[x] hisztogramok .attribute** | Az attribútum, amelyen számította ki a hisztogramhoz neve.
**[x] hisztogramok .distinct_values** | Megfelelő az attribútum az entitások között különböző értékek száma.
**[x] hisztogramok .total_count** | Teljes száma érték megfelelő az attribútum az entitások között.
**[x] hisztogramok .histogram** | Ez az attribútum hisztogram adatait.
**[x] .histogram [y] .value hisztogramok** |  Az attribútum értékét.
**[x] [y] .histogram .logprob hisztogramok**  |Az attribútumérték rendelkező entitások megfelelő teljes természetes logaritmusát valószínűségét.
**[x] [y] .histogram .count hisztogramok**  |Az attribútumérték egyező entitások száma.
**megszakítva** | IGAZ, ha a kérelem túllépte az időkorlátot.


#### <a name="example"></a>Példa:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Ebben a példában létrehozása érdekében a kiadványok száma hisztogram év szerint egy adott szerző 2010, mivel azt először generálhat a lekérdezési kifejezés használatával a **értelmezése** API a lekérdezési karakterlánc: *papers szerint 2012 után jaime teevan*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>A kifejezés a értelmezése API által visszaküldött első értelmezésében *és (összetett (AA. AuN == "jaime teevan"), Y > 2012)*.
<br>A kifejezés értéke kerül majd átadásra a **calchistogram** API-t. A *attributes=Y,F.FN* paraméter azt jelzi, hogy a disztribúciók tanulmány számát kell év és tanulmány mező, pl.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>A válasz a kérésre először azt jelzi, hogy nincsenek-e 37 tanulmányok, amelyek megfelelnek a lekérdezési kifejezésben.  Az a *év* attribútumot, 3 különböző értékek, egy 2012 (azaz 2013, 2014, és 2015.) a lekérdezésben megadott után minden évben.  Az összes tanulmány 3 különböző értékeket keresztül jelenleg 37.  Az egyes *év*, a hisztogram jeleníti meg az értéket, teljes természetes logaritmusát valószínűség és az egyező entitások száma.     

A hisztogram a *tanulmánya mező* jeleníti meg, hogy nincsenek-e tanulmány 34 egyedi mezők. Lehet, hogy a papír több mezőjére vonatkozó tanulmánya társítva, a számuk (53-as) egyező entitások száma nagyobb lehet.  Bár vannak 34 különböző értékeket, a válasz csak tartalmazza a top 4 miatt a *száma = 4* paraméter.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
