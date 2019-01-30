---
title: CalcHistogram metódus - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a CalcHistogram metódus az a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: f994a254e661cd245d2e953efd3bd595d50c6fc7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214313"
---
# <a name="calchistogram-method"></a>calchistogram metódus
A *calchistogram* módszer kiszámítja a strukturált a lekérdezés megfelelő objektumok és azok attribútumértékek eloszlását számítja ki.

## <a name="request"></a>Kérés
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name (Név)|Value|Leírás
----|-----|-----------
kifejezés | Szöveges karakterlánc | Strukturált lekérdezési kifejezés, amely meghatározza az index entitások keresztül hisztogramok értékének kiszámításához.
Attribútumok | Szöveges karakterlánc (alapértelmezés = "") | A válaszba foglalandó attribútumot tartalmazó, vesszővel elválasztott listája.
count   | Szám (alapértelmezés = 10) | Visszaadott eredmények száma.
offset  | Szám (alapértelmezés = 0) | Az első eredmény index való visszatéréshez.

## <a name="response-json"></a>Válasz (JSON)
JSONPath | Leírás
----|----
$.expr | *kifejezés* paraméter a kérelemből.
$.num_entities | Egyező entitások teljes száma.
$.histograms |  Hisztogramok, minden kért attribútum egy tömbje.
$.histograms [\*] .attribute | Az attribútum, amelyen számította ki a hisztogramhoz neve.
$.histograms[\*].distinct_values | Megfelelő az attribútum az entitások között különböző értékek száma.
$.histograms [\*] .total_count | Teljes száma érték megfelelő az attribútum az entitások között.
$.histograms [\*] .histogram | Ez az attribútum hisztogram adatait.
$.histograms[\*].histogram[\*].value | Attribútumérték.
$.histograms [\*] .histogram [\*] .logprob  | Az attribútumérték rendelkező entitások megfelelő teljes természetes logaritmusát valószínűségét.
$.histograms [\*] .histogram [\*] .count    | Az attribútumérték egyező entitások száma.
$.aborted | IGAZ, ha a kérelem túllépte az időkorlátot.

### <a name="example"></a>Példa
A tanulmányi kiadványok esetében például a következő számítja ki egy kiadvány számát évenként és kulcsszó alapján hisztogramja egy adott szerző tevékenykedtem 2013:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

A válasz azt jelzi, hogy nincsenek-e a lekérdezési kifejezés egyeztetése 37 tanulmányok.  Az a *év* attribútumot, 3 különböző értékek, egy az évente, mivel a 2013-hoz.  Az összes tanulmány 3 különböző értékeket keresztül jelenleg 37.  Az egyes *év*, a hisztogram jeleníti meg az értéket, teljes természetes logaritmusát valószínűség és az egyező entitások száma.     

A hisztogram a *kulcsszó* látható, hogy 34 distinct kulcsszót. Lehet, hogy a papír több kulcsszavak társítva, a számuk (53-as) egyező entitások száma nagyobb lehet.  Bár vannak 34 különböző értékeket, a válasz csak tartalmazza a top 4 miatt a "count = 4" paraméter.

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
