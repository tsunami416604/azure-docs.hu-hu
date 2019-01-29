---
title: Módszer – Academic Knowledge API értelmezi.
titlesuffix: Azure Cognitive Services
description: A értelmezése módszer használatával felhasználói lekérdezési karakterláncok Academic Graph-adatok és a Microsoft Cognitive Services Academic nyelvtani alapján formázott értelmezéseket ad vissza.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: d79e14fa48631ec6ce3cde42a7dbe300034782cb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171653"
---
# <a name="interpret-method"></a>Módszer értelmezése

A **értelmezése** REST API-t vesz igénybe egy végfelhasználó lekérdezési karakterlánc (azaz egy lekérdezést az alkalmazás a felhasználó által megadott) és a formázott a felhasználói szándékot az Academic Graph-adatok és az oktatási nyelvtani alapján értelmezéseket ad vissza.

Ahhoz, hogy az interaktív élményt, meghívhatja ezt a módszert ismételten után a felhasználó által megadott karakterek. Ebben az esetben állítsa be a **teljes** paraméter 1-re automatikus kiegészítés javaslatok engedélyezéséhez. Ha az alkalmazás nem kell automatikus kiegészítését, kell beállítania a **teljes** paraméter 0 értékre.

**REST-végpont:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>A kérés paraméterei

Name (Név)     | Value | Kötelező?  | Leírás
---------|---------|---------|---------
**Lekérdezés**    | Szöveges karakterlánc | Igen | Felhasználó által megadott lekérdezés.  Ha teljes értéke 1, lekérdezés létrehozni lekérdezési automatikus kiegészítését javaslatok előtagjaként is értelmezhető.        
**model**    | Szöveges karakterlánc | Nem  | A lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték *legújabb*.        
**Hajtsa végre** | 0 vagy 1 | Nem<br>Alapértelmezés: 0  | 1 azt jelenti, hogy automatikus kiegészítését javaslatok jönnek létre a szintaxis és a grafikon adatai alapján.         
**count**    | Szám | Nem<br>Alapértelmezés: 10 | Maximális száma értelmezéseket ad vissza.         
**eltolás**   | Szám | Nem<br>Alapértelmezés: 0  | Az első értelmezése index való visszatéréshez. Ha például *száma = 2 & eltolás = 0* 0 és 1 értelmezéseket ad vissza. *Count = 2 & eltolás = 2* 2. és 3 értelmezéseket ad vissza.       
**Időtúllépés**  | Szám | Nem<br>alapértelmezett: 1000 | Időkorlát ezredmásodpercben. Csak a az időtúllépési letelte előtt található értelmezés adja vissza.
<br>
  
## <a name="response-json"></a>Válasz (JSON)
Name (Név)     | Leírás
---------|---------
**Lekérdezés** |A *lekérdezés* paraméter a kérelemből.
**értelmezése** |Az egyező felhasználói bevitel ellen a nyelvtani 0 vagy több különböző módon tömbje.
**interpretations[x].logprob**  |A relatív természetes logaritmusát valószínűsége értelmezése. Nagyobb értékek nagy valószínűséggel.
**interpretations[x].parse**  |XML-karakterlánc, amely megjeleníti a lekérdezés minden egyes részét értelmezését.
**interpretations[x].rules**  |1 vagy több meghatározott szabályok a gramatice értelmezése során definiálásra tömbje. Az Academic Knowledge API esetében mindig lesz 1 szabályt.
**interpretations[x].rules[y].name**  |a szabály nevét.
**interpretations[x].rules[y].output**  |A szabály kimenetét.
**interpretations[x].rules[y].output.type** |A szabály a kimeneti adattípus.  Az Academic Knowledge API Ez minden esetben az "query".
**interpretations[x].rules[y].output.value**  |A szabály kimenete. Az Academic Knowledge API esetén ez a kifejezés karakterlánc, amely a kiértékelés és calchistogram módszerek adható át.
**aborted** | IGAZ, ha a kérelem túllépte az időkorlátot.

<br>
#### <a name="example"></a>Példa:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>A válasz az alábbi tartalmazza az első két (paraméter miatt *száma = 2*) legvalószínűbb értelmezések, amelyek befejezik a részleges felhasználói bevitel *tanulmányok szerint jaime*: *tanulmányok szerint jaime teevan*  és *tanulmányok szerint jaime zöld*.  A szolgáltatás létrehozott lekérdezés befejezésekből helyett csak a mérlegeli pontos egyezés a Szerző *jaime* , mert a kérelemben megadott *teljes = 1*. Vegye figyelembe, hogy a canonical érték *j l zöld* keresztül a szinonima matched *jamie zöld*, ahogyan a parse.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Lekérdezheti az entitás eredmények egy értelmezéséhez, *output.value* származó a **értelmezése** API-t, és el kell küldenie azokat a **kiértékelése** API-n keresztül a *kifejezés*  paraméter. Ebben a példában a lekérdezés első értelmezésére van: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
