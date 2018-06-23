---
title: A Tudásbázis feltárása Service API CalcHistogram metódus |} Microsoft Docs
description: Megtudhatja, hogyan használja a CalcHistogram metódust a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347034"
---
# <a name="calchistogram-method"></a>calchistogram módszer
A *calchistogram* metódus kiszámítja a strukturált lekérdezési kifejezésben megfelelő objektumok és azok attribútumértékek eloszlását számítja ki.

## <a name="request"></a>Kérés
`http://<host>/calchistogram?expr=<expr>[&options]` 

Name (Név)|Érték|Leírás
----|-----|-----------
kifejezés | Szöveges karakterlánc | Adja meg az index entitásokat, amelyben hisztogram kiszámításához strukturált lekérdezési kifejezés.
Attribútumok | Karakterlánc (alapértelmezett = "") | A válaszban szereplő attribútumot tartalmazó, vesszővel elválasztott listája.
count   | Szám (alapértelmezett = 10) | Visszaadandó eredmények száma.
offset  | Szám (alapértelmezett = 0) | Az első eredmény indexe való visszatéréshez.

## <a name="response-json"></a>Válasz (JSON)
JSONPath | Leírás
----|----
$.expr | *kifejezés* paramétert a kérésből.
$.num_entities | Egyező entitások száma.
$.histograms |  A kért attribútum hisztogram, minden tömbje.
$.histograms [\*] .attribute | Az attribútum, amelyben a hisztogram számította neve.
$.histograms [\*] .distinct_values | Eltérő érték között megfelelő az attribútum entitások száma.
$.histograms [\*] .total_count | Megfelelő az attribútum entitások közötti érték példányok száma összesen.
$.histograms [\*] .histogram | Ez az attribútum hisztogram adatait.
$.histograms [\*] .histogram [\*] .value | Attribútum-érték.
$.histograms [\*] .histogram [\*] .logprob  | Az attribútumérték rendelkező entitások megfelelő teljes természetes napló valószínűségét.
$.histograms [\*] .histogram [\*] .count    | Az attribútumérték rendelkező egyező entitások száma.
$.aborted | Értéke TRUE, ha a kérelem túllépte az időkorlátot.

### <a name="example"></a>Példa
Academic kiadványok példában a következő kiszámítja a kiadvány számok év és kulcsszó hisztogram az egy adott szerző 2013 óta:

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

A válasz azt jelzi, hogy vannak-e a lekérdezés kifejezésnek megfelelő 37 által írt cikkeket.  Az a *év* attribútumot, 3 eltérő érték, egy évente 2013 óta.  A teljes dokumentum keresztül 3 különböző értékeket leképezésszáma 37.  Az egyes *év*, a hisztogram jeleníti meg az értéket, teljes természetes napló valószínűség és az egyező entitások száma.     

A hisztogram *kulcsszó* jeleníti meg, hogy vannak-e 34 distinct kulcsszót. Lehet, hogy a dokumentum több kulcsszó van társítva, a számuk (53) nagyobb, mint a hozzá tartozó entitások válhat.  Habár 34 különböző értékeket, a válasz csak tartalmazza a top 4 miatt: a "count = 4" paraméter.

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
