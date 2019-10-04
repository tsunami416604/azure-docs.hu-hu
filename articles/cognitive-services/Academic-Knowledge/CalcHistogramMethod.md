---
title: Számítási hisztogram metódus – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A számítási hisztogram metódus használatával kiszámíthatja az attribútumok eloszlását a papír entitások készletéhez.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 9e84b1ad37b3224ec5553d0a66ba0fc84bc88f55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705079"
---
# <a name="calchistogram-method"></a>Számítási hisztogram metódus

A **számítási hisztogram** -REST API az attribútumok eloszlásának kiszámítására szolgál a papír entitások készlete számára.          


**REST-végpont:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>

## <a name="request-parameters"></a>A kérés paraméterei

Name (Név)  |Value | Kötelező?  |Leírás
-----------|----------|--------|----------
**kifejezés**    |Szöveges karakterlánc | Igen  |Egy lekérdezési kifejezés, amely meghatározza azokat az entitásokat, amelyeken a hisztogramok kiszámítása történik.
**modell** |Szöveges karakterlánc | Nem |Válassza ki a lekérdezni kívánt modell nevét.  Jelenleg az alapértelmezett érték a *legújabb*.
**attribútumok** | Szöveges karakterlánc | Nem<br>alapértelmezett | Vesszővel tagolt lista, amely meghatározza a válaszban szereplő attribútum-értékeket. Az attribútumok nevei megkülönböztetik a kis-és nagybetűket.
**count** |Number | Nem<br>Alapértelmezett: 10 |A visszaadni kívánt eredmények száma.
**eltolás**  |Number | Nem<br>Alapértelmezett: 0 |A visszatérés első eredményének indexe.
**időtúllépés**  |Number | Nem<br>Alapértelmezett: 1000 |Időtúllépés ezredmásodpercben. A rendszer csak az időtúllépés lejárta előtt talált értelmezéseket adja vissza.

## <a name="response-json"></a>Válasz (JSON)

Name (Név) | Leírás
--------|---------
**kifejezés**  |A kérelemben szereplő kifejezés paraméter.
**num_entities** | A megfelelő entitások száma összesen
**hisztogramok** |  Hisztogramok tömbje, a kérelemben megadott egyes attribútumok egyike.
**hisztogramok [x]. attribútum** | Annak az attribútumnak a neve, amelyen a hisztogram kiszámítására került.
**histograms[x].distinct_values** | Az attribútumhoz tartozó entitások közötti különböző értékek száma.
**histograms[x].total_count** | Az attribútumhoz tartozó egyező entitások közötti érték-példányok száma összesen
**histograms[x].histogram** | Az attribútum hisztogramjának adatterülete
**histograms[x].histogram[y].value** |  Az attribútum értéke.
**histograms[x].histogram[y].logprob**  |Az ehhez az attribútumérték-értékkel rendelkező entitások megfeleltetésének teljes természetes naplózási valószínűsége.
**histograms[x].histogram[y].count**  |Az attribútum értékkel rendelkező egyező entitások száma.
**megszakadt** | Értéke true, ha a kérelem időtúllépést adott meg.


#### <a name="example"></a>Példa:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Ebben a példában egy, a 2010 óta egy adott szerző számára a publikációk számának egy hisztogramjának létrehozásához először létre kell hozni a lekérdezési kifejezést az értelmező API és a lekérdezési karakterlánc használatával: a 2012-as számú, *Jaime teevan által készített dokumentumokat*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>A értelmező API által visszaadott első értelmezésben szereplő kifejezés *(kompozit (AA). AuN = = "Jaime teevan"), Y > 2012)* .
<br>A rendszer ezt a kifejezést adja át a **számítási hisztogram** API-nak. Az *attribútumok = Y, F. FN* paraméter azt jelzi, hogy a papírméret eloszlásának év és tanulmány mezőnek kell lennie, például:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>A kérelemre adott válasz először azt jelzi, hogy a lekérdezési kifejezésnek megfelelő 37-es papír szerepel.  Az *év* attribútum esetében 3 különböző érték létezik, a lekérdezésben meghatározottak szerint minden évben 2012 (azaz 2013, 2014 és 2015).  A 3 különböző értéknél a teljes papír száma 37.  A hisztogram minden *évben*megjeleníti az értéket, a természetes napló teljes valószínűségét és a megfelelő entitások számát.     

A tanulmányi *mező* hisztogramja azt mutatja, hogy 34 különböző tanulmányi mező van. Mivel a papír több tanulmányhoz is társítható, a teljes darabszám (53) nagyobb lehet, mint a megfelelő entitások száma.  Bár a 34 különböző értékeket tartalmaz, a válasz csak az első 4 értéket tartalmazza a *Count = 4* paraméter miatt.

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
