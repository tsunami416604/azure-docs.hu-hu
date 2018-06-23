---
title: A Tudásbázis feltárása Service API módszer kiértékelésének |} Microsoft Docs
description: Megtudhatja, hogyan használja a kiértékelés metódust a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347059"
---
# <a name="evaluate-method"></a>Módszer kiértékelésének
A *kiértékelése* metódus kiértékeli, és az index alapján strukturált lekérdezési kifejezés-kimenet visszaadása.

Általában a kifejezés szerzi be a válasz a interpret metódus.  De is létrehozhat lekérdezési kifejezések saját kezűleg (lásd: [strukturált lekérdezési kifejezésben](Expressions.md)).  

## <a name="request"></a>Kérés 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name (Név)|Érték|Leírás
----|----|----
kifejezés       | Szöveges karakterlánc | Strukturált lekérdezési kifejezésben, ami egy index entitások részét.
Attribútumok | Szöveges karakterlánc | Ahhoz, hogy szerepeljen a válasz attribútumok vesszővel tagolt listája.
count      | Szám (alapértelmezett = 10) | Térjen vissza az eredmények maximális száma.
offset     | Szám (alapértelmezett = 0) | Az első eredmény indexe való visszatéréshez.
OrderBy |   Szöveges karakterlánc | Az eredmények választható rendezési sorrend követ rendezéséhez használt attribútum neve (alapértelmezett = asc): "*attrname*[: (asc&#124;desc)]".  Ha nincs megadva, a rendszer visszairányítja az eredményeket, természetes napló valószínűség csökkentésével.
timeout  | Szám (alapértelmezett = 1000) | Időtúllépése milliszekundumban. Csak a számított időkorlát letelte előtt eredmény akkor minősül.

Használja a *száma* és *eltolás* paraméterek, nagy számú eredmény szerezhető Növekményesen több kérés során.
  
## <a name="response-json"></a>Válasz (JSON)
JSONPath|Leírás
----|----
$.expr | *kifejezés* paramétert a kérésből.
$.entities | 0 vagy több objektum entitások a structured query kifejezésnek megfelelő tömbje. 
$.aborted | Értéke TRUE, ha a kérelem túllépte az időkorlátot.

Minden entitás tartalmaz egy *logprob* érték és a kért attribútum értéke.

## <a name="example"></a>Példa
Academic kiadványok példában a következő kérelmet továbbítja a strukturált lekérdezési kifejezésben (vélhetően kimenetéből egy *értelmezhetők* kérelem), és lekéri a felső 2 entitások megfelelő néhány attribútumok:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

A válasz tartalmazza a top 2 ("count = 2") entitásokat valószínűleg megfelelő.  Minden entitás a cím, az év, a szerző nevét és a szerző ID attribútumokkal adott vissza.  Vegye figyelembe a módját struktúráját Összetett attribútumértékek megegyezik a módszer az adatfájl vannak megadva. 

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
