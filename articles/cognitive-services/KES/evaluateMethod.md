---
title: Knowledge Exploration Service API-módszer – kiértékelése
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az értékelés módszer a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: dcfa9bb7931cf3b682bacf722b67acd6d4a370c0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860728"
---
# <a name="evaluate-method"></a>Módszer kiértékelése

A *kiértékelése* metódus kiértékeli, és a kimenet strukturált lekérdezési kifejezés index adatok alapján ad vissza.

Általában egy kifejezés fogja meghatározni a értelmezése módszert választ.  De is létrehozhat lekérdezési kifejezések magát (lásd: [strukturált lekérdezési kifejezés](Expressions.md)).  

## <a name="request"></a>Kérés 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name (Név)|Érték|Leírás
----|----|----
kifejezés       | Szöveges karakterlánc | Strukturált lekérdezési kifejezés, amely egy index entitások részhalmazát.
Attribútumok | Szöveges karakterlánc | Válasz foglalandó attribútumok vesszővel tagolt listája.
count      | Szám (alapértelmezés = 10) | Visszaadott eredmények maximális száma.
offset     | Szám (alapértelmezés = 0) | Az első eredmény index való visszatéréshez.
OrderBy |   Szöveges karakterlánc | Nem kötelező rendezési sorrend követ, az eredmények rendezéséhez használt attribútum neve (alapértelmezés = asc): "*attrname*[: (asc&#124;desc)]".  Ha nincs megadva, a rendszer visszairányítja az eredményeket, természetes logaritmusát valószínűség csökkentésével.
timeout  | Szám (alapértelmezés = 1000) | Időkorlát ezredmásodpercben. Csak az időtúllépési letelte előtt számított eredményeket a rendszer adja vissza.

Használatával a *száma* és *eltolás* paraméterek, nagy számú eredményt szerezhetők be Növekményesen több kérelmeihez képest.
  
## <a name="response-json"></a>Válasz (JSON)
JSONPath|Leírás
----|----
$.expr | *kifejezés* paraméter a kérelemből.
$.entities | 0 vagy több objektum entitásokat a strukturált lekérdezési kifejezés egyeztetése tömbje. 
$.aborted | IGAZ, ha a kérelem túllépte az időkorlátot.

Minden entitás tartalmaz egy *logprob* érték és a kért attribútumokhoz értékeit.

## <a name="example"></a>Példa
Oktatási kiadványok példában a következő kérés átadja egy strukturált lekérdezési kifejezés (vélhetően kimenetéből származó egy *értelmezése* kérés), és lekéri az entitások megfelelő felső 2 néhány attribútumai:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

A válasz tartalmazza az első 2 ("count = 2") entitásokat leginkább megfelelő.  Minden entitás a cím, az év, a szerző neve és a szerző ID attribútum adott vissza.  Vegye figyelembe, hogyan összetett struktúráját az attribútumértékek megegyezik a fájlt a megadott módon. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
