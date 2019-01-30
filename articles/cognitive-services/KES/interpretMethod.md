---
title: Knowledge Exploration Service API-módszer – értelmezése
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a értelmezése metódus az a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 4f950734081be6cf76770b1c6cb2feca7efdae70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221403"
---
# <a name="interpret-method"></a>Módszer értelmezése

A *értelmezése* metódushoz a természetes nyelvű lekérdezési karakterláncot, és a formázott, nyelvtani és index adatokon alapuló felhasználói szándékot értelmezéseket ad vissza.  Ahhoz, hogy egy interaktív keresési funkciót, ez a módszer eltérő elnevezése lehet egyes karakterek megadásakor a felhasználó által beírt a *teljes* paraméter értéke 1, automatikus kiegészítés javaslatok engedélyezéséhez.

## <a name="request"></a>Kérés

`http://<host>/interpret?query=<query>[&<options>]`

Name (Név)|Value| Leírás
----|----|----
lekérdezés    | Szöveges karakterlánc | Felhasználó által megadott lekérdezés.  Ha teljes értéke 1, lekérdezés létrehozni lekérdezési automatikus kiegészítését javaslatok előtagjaként is értelmezhető.        
Hajtsa végre | 0 (alapértelmezés) vagy 1 | 1 azt jelenti, hogy automatikus kiegészítését javaslatok jönnek létre, nyelvtani és index adatok alapján.         
count    | Szám (alapértelmezés = 10) | Maximális száma értelmezéseket ad vissza.         
offset   | Szám (alapértelmezés = 0) | Az első értelmezése index való visszatéréshez.  Ha például *száma = 2 & eltolás = 0* 0 és 1 értelmezéseket ad vissza. *Count = 2 & eltolás = 2* 2. és 3 értelmezéseket ad vissza.       
timeout  | Szám (alapértelmezés = 1000) | Időkorlát ezredmásodpercben. Csak a az időtúllépési letelte előtt található értelmezés adja vissza.

Használatával a *száma* és *eltolás* paraméterek, nagy számú eredményt szerezhetők be Növekményesen több kérelmeihez képest.

## <a name="response-json"></a>Válasz (JSON)

JSONPath     | Leírás
---------|---------
$.query |*lekérdezés* paraméter a kérelemből.
$.interpretations   |0 vagy további részleteket a egyezik a bemeneti lekérdezést a nyelvtani tömbje.
$.interpretations[\*].logprob   |Relatív log valószínűség értelmezése (< = 0).  A magasabb értékek valószínűbb.
$.interpretations[\*].parse |XML-karakterlánc, amely megjeleníti a lekérdezés minden egyes részét értelmezését.
$.interpretations [\*] .rules |1 vagy több szabályok értelmezése során hív nyelvtani meghatározott tömbje.
$.interpretations[\*].rules[\*].name    |a szabály nevét.
$.interpretations[\*].rules[\*].output  |A szabály szemantikai kimenetét.
$.interpretations[\*].rules[\*].output.type |A szemantikai kimeneti adattípus.
$.interpretations[\*].rules[\*].output.value|A szemantikai kimeneti értékét.  
$.aborted | IGAZ, ha a kérelem túllépte az időkorlátot.

### <a name="parse-xml"></a>XML feldolgozása

A parse XML jelzi a (kész) lekérdezés információkat, hogyan illeszkedik a szintaxis szabályainak és az index attribútumainak szemben.  Az alábbi, az oktatási kiadványok tartományból. példa:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

A `<rule>` elemének a tartományt a lekérdezésben, a szabály által megadott megfelelő határoló annak `name` attribútum.  Előfordulhat, hogy beágyazott, amikor a parse magában foglalja a gramatice szabályra mutató.

A `<attr>` elemének a tartományt a lekérdezésben az index attribútum által megadott megfelelő határoló annak `name` attribútum.  Amikor az egyezés a bemeneti lekérdezés, szinonima magában foglalja a `canonical` attribútum tartalmazza a canonical értéket a szinonima az indexből.

## <a name="example"></a>Példa

Az academic kiadványok példában a következő kérelmet értéket ad vissza, legfeljebb 2 automatikus kiegészítését javaslatok "tanulmányok szerint jaime" előtag lekérdezés:

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

A válasz tartalmazza az első két ("count = 2") legvalószínűbb értelmezések, amelyek befejezik a részleges lekérdezés "tanulmányok szerint jaime": "papers jaime teevan által" és "jaime zöld papers".  A szolgáltatás létrehozott lekérdezés befejezésekből helyett csak a mérlegeli pontos egyezés a Szerző "jaime", mert a kérelemben megadott "teljes = 1". Vegye figyelembe, hogy a canonical érték "zöld j l" a "jamie zöld" szinonimát keresztül egyezik-e a parse leírtak szerint.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Típus esetén a szemantikus kimeneti "lekérdezés", mint ebben a példában a megfelelő objektumokat is lekérdezhetők átadásával *output.value* , a [ *kiértékelése* ](evaluateMethod.md) API-n keresztül a *expr* paraméter.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
