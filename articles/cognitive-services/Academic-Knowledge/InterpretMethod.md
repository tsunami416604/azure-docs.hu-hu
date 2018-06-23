---
title: Academic Knowledge API-ban. a metódus értelmezhetők |} Microsoft Docs
description: A Interpret metódus használatával visszaállíthatja a felhasználói lekérdezési karakterláncok Academic Diagramadatok és a Microsoft kognitív szolgáltatások Academic nyelvtani alapján formázott értelmezése.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a46c792f14fabf6562666d1067ef880bd505741f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346918"
---
# <a name="interpret-method"></a>Módszer értelmezése

A **értelmezhetők** REST API veszi a felhasználó (azaz egy lekérdezést az alkalmazás egy felhasználó által megadott) lekérdezési karakterláncot, és formázott felhasználói leképezés a Academic Diagramadatok és a Academic nyelvtan alapján értelmezéseket ad vissza.

Egy interaktív felhasználói élmény biztosítása érdekében, a metódus hívása többször után a felhasználó által beírt karakterek. Ebben az esetben állítsa be a **teljes** javaslatok automatikus kitöltés engedélyezése 1 paraméterrel. Ha az alkalmazás nem kell automatikus-befejezését, célszerű a **teljes** paraméter 0 értékre.

**REST-végpont:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>A kérelemben szereplő paraméterek

Name (Név)     | Érték | Kötelező?  | Leírás
---------|---------|---------|---------
**lekérdezés**    | Szöveges karakterlánc | Igen | Felhasználó által megadott lekérdezés.  Ha teljes értéke 1, lekérdezés egy előtagot előállítása automatikus-végrehajtási konstrukciókat hibaként értelmezi.        
**modell**    | Szöveges karakterlánc | Nem  | A lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték *legújabb*.        
**végezze el** | 0 vagy 1 | Nem<br>alapértelmezett: 0  | 1 azt jelenti, hogy automatikus-végrehajtási javaslatok a szintaxis és a graph adatok alapján jönnek létre.         
**száma**    | Szám | Nem<br>alapértelmezett: 10 | Értelmezéseket a visszatérési maximális száma.         
**Az offset**   | Szám | Nem<br>alapértelmezett: 0  | Index első értelmezése való visszatéréshez. Például *count = 2 & eltolás = 0* értelmezéseket 0 és 1 adja vissza. *Count = 2 & eltolás = 2* értelmezéseket a 2. és 3 adja vissza.       
**időtúllépés**  | Szám | Nem<br>alapértelmezett: 1000 | Időtúllépése milliszekundumban. Csak a időtúllépés letelte előtt található értelmezéseket adja vissza.
<br>
  
## <a name="response-json"></a>Válasz (JSON)
Name (Név)     | Leírás
---------|---------
**lekérdezés** |A *lekérdezés* paramétert a kérésből.
**értelmezéseket** |A felhasználói bemenetet a következők alapján a nyelvtan megfelelő 0 vagy több különböző módon tömbjét.
**[x] értelmezéseket .logprob**  |A relatív természetes napló valószínűség értelmezése. A nagyobb értékek valószínűbb.
**[x] értelmezéseket .parse**  |XML-karakterlánc, amely a lekérdezés részét képező összes értelmezését.
**[x] értelmezéseket .rules**  |1 vagy több szabály lett meghatározva a nyelvtanban értelmezése során definiálásra tömbjét. A Academic Knowledge API mindig lesz 1 szabály.
**[x] [-y] .rules .név értelmezéseket**  |A szabály nevét.
**[x] [-y] .rules .output értelmezéseket**  |A szabályok kimenete.
**[x] [-y] .rules.output.type értelmezéseket** |A szabály a kimeneti adattípus.  A Academic Knowledge API Ez minden esetben "query".
**[x] [-y] .rules.output.value értelmezéseket**  |A szabályok kimenete. Academic Knowledge API ez pedig lekérdezési kifejezés karakterlánc, amely a kiértékelés és calchistogram módszerek adható át.
**megszakadt** | Értéke TRUE, ha a kérelem túllépte az időkorlátot.

<br>
#### <a name="example"></a>Példa:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Az alábbi válasz tartalmazza a felső két (miatt a paraméter *száma = 2*), amelyek befejezik a részleges felhasználói bevitel valószínűleg értelmezéseket *által írt cikkeket jaime által*: *által írt cikkeket jaime teevan által*  és *által írt cikkeket jaime zöld*.  A generált szolgáltatás lekérdezés befejezésekhez helyett annak eldöntéséhez, hogy csak a szerző megfelelő találat pontos *jaime* , mert a kérés megadott *teljes = 1*. Vegye figyelembe, hogy a kanonikus érték *j l zöld* keresztül a szinonima egyező *jamie zöld*, a parse jelöltük.


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
<br>Entitás eredményeket egy értelmezéséhez lekéréséhez használja *output.value* a a **értelmezhetők** API-t, és adja át, amely az a **kiértékelése** keresztül API a *kifejezés*  paraméter. Ebben a példában a lekérdezés első értelmezési van: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 