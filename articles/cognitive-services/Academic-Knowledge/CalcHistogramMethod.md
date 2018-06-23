---
title: Academic Knowledge API-ja CalcHistogram metódus |} Microsoft Docs
description: A CalcHistogram módszert az attribútumértékek állítja be a Microsoft kognitív Services papír entitások terjesztési kiszámításához.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346902"
---
# <a name="calchistogram-method"></a>CalcHistogram módszer

A **calchistogram** REST API-t állítja be a papír entitások attribútumértékek eloszlását számításához.          


**REST-végpont:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>A kérelemben szereplő paraméterek

Name (Név)  |Érték | Kötelező?  |Leírás
-----------|----------|--------|----------
**kifejezés**    |Szöveges karakterlánc | Igen  |A lekérdezési kifejezés, amely meghatározza az entitásokat, amelyben hisztogram kiszámításához.
**modell** |Szöveges karakterlánc | Nem |Válassza ki a lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték *legújabb*.
**Attribútumok** | Szöveges karakterlánc | Nem<br>alapértelmezett: | Adja meg a válaszban szereplő attribútumértékek vesszővel tagolt listája. Attribútumnevek megkülönböztetik a kis-és nagybetűket.
**száma** |Szám | Nem<br>Alapértelmezett: 10 |Visszaadandó eredmények száma.
**Az offset**  |Szám | Nem<br>Alapértelmezett: 0 |Az első eredmény indexe való visszatéréshez.
<br>
## <a name="response-json"></a>Válasz (JSON)
Name (Név) | Leírás
--------|---------
**kifejezés**  |A kifejezés paramétert a kérésből.
**num_entities** | Egyező entitások száma.
**Hisztogram** |  Hisztogram, minden attribútum a kérelemben megadott egy tömbjét.
**[x] hisztogram .attribute** | Az attribútum, amelyben a hisztogram számította neve.
**[x] hisztogram .distinct_values** | Eltérő érték között megfelelő az attribútum entitások száma.
**[x] hisztogram .total_count** | Megfelelő az attribútum entitások közötti érték példányok száma összesen.
**[x] hisztogram .histogram** | Ez az attribútum hisztogram adatait.
**[x] [-y] .histogram .value hisztogram** |  Az attribútum értékét.
**[x] [-y] .histogram .logprob hisztogram**  |Az attribútumérték rendelkező entitások megfelelő teljes természetes napló valószínűségét.
**[x] [-y] .histogram .count hisztogram**  |Az attribútumérték rendelkező egyező entitások száma.
**megszakadt** | Értéke TRUE, ha a kérelem túllépte az időkorlátot.

 <br>
#### <a name="example"></a>Példa:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Ebben a példában generálásához kiadványok számú hisztogram évente az egy adott szerző 2010, mivel azt először létrehozhat a lekérdezési kifejezés használatával a **értelmezhetők** API-t a lekérdezési karakterlánc: *által papers Miután 2012 jaime teevan*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>A kifejezés a interpret API által visszaküldött első értelmezésekor *és (összetett (AA. AuN == "jaime teevan"), Y > 2012)*.
<br>A kifejezés értéke kerül majd átadásra a **calchistogram** API. A *attributes=Y,F.FN* paraméter arra utalnak, hogy a dokumentum számok disztribúciók kell év és a vizsgálat mező, pl.:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>A kérelem a válasz először jelzi, hogy 37 által írt cikkeket, amelyek megfelelnek a lekérdezési kifejezésben.  Az a *év* attribútumot, 3 eltérő érték, egy 2012 (azaz 2013, 2014 és 2015) a lekérdezésben megadott után minden évben.  A teljes dokumentum keresztül 3 különböző értékeket leképezésszáma 37.  Az egyes *év*, a hisztogram jeleníti meg az értéket, teljes természetes napló valószínűség és az egyező entitások száma.     

A hisztogram *tanulmányozzák mező* jeleníti meg, hogy vannak-e tanulmány 34 különböző mezőket. Lehet, hogy a dokumentum több mező vizsgálat van társítva, a számuk (53) nagyobb, mint a hozzá tartozó entitások válhat.  Habár 34 különböző értékeket, a válasz csak tartalmazza a top 4 miatt: a *count = 4* paraméter.

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
