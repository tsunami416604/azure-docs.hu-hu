---
title: A Tudásbázis feltárása Service API metódus értelmezhetők |} Microsoft Docs
description: Megtudhatja, hogyan használja a Interpret metódust a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347094"
---
# <a name="interpret-method"></a>Módszer értelmezése
A *értelmezhetők* módszer veszi természetes nyelvű lekérdezési karakterláncot, és formázott felhasználói leképezés nyelvtan és az index alapján értelmezéseket ad vissza.  Arra, hogy az interaktív keresési élményt, az ezt a módszert is hívható meg, mert minden karaktert is meg kell adni a felhasználó által a *teljes* paraméter értéke 1 javaslatok automatikus kitöltés engedélyezése.

## <a name="request"></a>Kérés
`http://<host>/interpret?query=<query>[&<options>]`

Name (Név)|Érték| Leírás
----|----|----
lekérdezés    | Szöveges karakterlánc | Felhasználó által megadott lekérdezés.  Ha teljes értéke 1, lekérdezés egy előtagot előállítása automatikus-végrehajtási konstrukciókat hibaként értelmezi.        
végezze el | 0 (alapértelmezés) vagy 1 | 1 azt jelenti, hogy automatikus-végrehajtási javaslatok a nyelvtan és index adatok alapján jönnek létre.         
count    | Szám (alapértelmezett = 10) | Értelmezéseket a visszatérési maximális száma.         
offset   | Szám (alapértelmezett = 0) | Index első értelmezése való visszatéréshez.  Például *count = 2 & eltolás = 0* értelmezéseket 0 és 1 adja vissza. *Count = 2 & eltolás = 2* értelmezéseket a 2. és 3 adja vissza.       
timeout  | Szám (alapértelmezett = 1000) | Időtúllépése milliszekundumban. Csak a időtúllépés letelte előtt található értelmezéseket adja vissza.

Használja a *száma* és *eltolás* paraméterek, nagy számú eredmény szerezhető Növekményesen több kérés során.

## <a name="response-json"></a>Válasz (JSON)
JSONPath     | Leírás
---------|---------
$.query |*lekérdezés* paramétert a kérésből.
$.interpretations   |0 vagy további részleteket a felel meg a bemeneti lekérdezés szemben a nyelvtan tömbje.
$.interpretations [\*] .logprob   |Relatív napló valószínűség értelmezése (< = 0).  Valószínűbb a magasabb értékkel.
$.interpretations [\*] .parse |XML-karakterlánc, amely a lekérdezés részét képező összes értelmezését.
$.interpretations [\*] .rules |A nyelvtan meghívott értelmezése során megadott legalább 1 szabályok tömbje.
$.interpretations [\*] .rules [\*] .név    |A szabály nevét.
$.interpretations [\*] .rules [\*] .output  |A szabályok szemantikai kimenete.
$.interpretations [\*] .rules [\*]. output.type |A szemantikai kimeneti adattípus.
$.interpretations [\*] .rules [\*]. output.value|A szemantikai kimeneti értéke.  
$.aborted | Értéke TRUE, ha a kérelem túllépte az időkorlátot.

### <a name="parse-xml"></a>XML elemzése
A parse XML jelzi a információkat, hogyan felelnek a nyelvtan a szabályokat, és az index attribútumok ellen (kész) lekérdezés.  Az alábbiakban a következő egy példa a academic kiadványok tartományból:

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

A `<rule>` elem határoló a lekérdezés által megadott szabály megfelelő tartományon a `name` attribútum.  Ha a parse magában foglalja a nyelvtani szabály hivatkozások előfordulhat, hogy ágyazható.

A `<attr>` elem határoló a lekérdezés által megadott index attribútum megfelelő tartományon a `name` attribútum.  A bemeneti lekérdezés szinonimát jelentkezik az egyezés a `canonical` attribútum az indextől a szinonima megfelelő kanonikus értékét fogja tartalmazni.

## <a name="example"></a>Példa
Academic kiadványok példában a következő kérelem legfeljebb 2 automatikus-végrehajtási javaslatok "által írt cikkeket által jaime" előtag lekérdezés adja vissza:

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

A válasz tartalmazza a felső két ("count = 2"), amelyek befejezik a részleges lekérdezés "által írt cikkeket által jaime" valószínűleg értelmezéseket: "papers jaime teevan által" és "jaime zöld papers".  A generált szolgáltatás lekérdezés befejezésekhez helyett annak eldöntéséhez, hogy csak pontos megegyezik a Szerző "jaime", mert a kérés megadott "teljes = 1". Vegye figyelembe, hogy megfelelő-e a kanonikus érték "zöld j l" a "jamie zöld," szinonimát keresztül a parse jelöltük.


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

Ha szemantikai kimenet típusa "lekérdezés", mint ebben a példában a megfelelő objektumok lekérhető úgy, hogy *output.value* való a [ *kiértékelése* ](evaluateMethod.md) keresztül a API*kifejezés* paraméter.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
