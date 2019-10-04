---
title: Értelmezési módszer – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: A tolmácsolási módszer használatával a felhasználói lekérdezési karakterláncok formázott értelmezéseit lehet visszaadni a tudományos gráfok és a Microsoft Cognitive Services Academic nyelvtana alapján.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d960aff109e0eca70cb87463770620093e563f63
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706677"
---
# <a name="interpret-method"></a>Értelmezési módszer

A **tolmácsolási** REST API egy végfelhasználói lekérdezési karakterláncot (azaz egy, az alkalmazás felhasználója által megadott lekérdezést) használ, és a tudományos diagram adatai és a tudományos nyelvtan alapján a felhasználói szándék formázott értelmezéseit adja vissza.

Ha interaktív élményt szeretne biztosítani, akkor ezt a metódust ismételten meghívhatja a felhasználó által megadott karakterek után. Ebben az esetben a **teljes** paramétert 1-re kell állítania az automatikus kiegészítési javaslatok engedélyezéséhez. Ha az alkalmazás nem igényel automatikus kiegészítést, állítsa a **teljes** paramétert 0-ra.

**REST-végpont:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>A kérés paraméterei

Name (Név)     | Value | Kötelező?  | Leírás
---------|---------|---------|---------
**query**    | Szöveges karakterlánc | Igen | A felhasználó által megadott lekérdezés.  Ha a Complete (1) értékre van állítva, a lekérdezés előtagként lesz értelmezve a lekérdezés automatikus befejezésére vonatkozó javaslatok létrehozásához.        
**modell**    | Szöveges karakterlánc | Nem  | A lekérdezni kívánt modell neve.  Jelenleg az alapértelmezett érték a *legújabb*.        
**teljes** | 0 vagy 1 | Nem<br>alapértelmezett: 0  | 1 azt jelenti, hogy az automatikus kiegészítési javaslatok a nyelvtani és a Graph-adatértékek alapján jönnek létre.         
**count**    | Number | Nem<br>alapértelmezett: 10 | A visszaadni kívánt értelmezések maximális száma.         
**eltolás**   | Number | Nem<br>alapértelmezett: 0  | A visszatérés első értelmezésének indexe. Például: *Count = 2 & eltolás = 0* – a 0 és az 1 értelmezést adja vissza. *Count = 2 & eltolás = 2* – a 2. és a 3. értelmezést adja vissza.       
**időtúllépés**  | Number | Nem<br>alapértelmezett: 1000 | Időtúllépés ezredmásodpercben. A rendszer csak az időtúllépés lejárta előtt talált értelmezéseket adja vissza.

<br>
  
## <a name="response-json"></a>Válasz (JSON)

Name (Név)     | Leírás
---------|---------
**query** |A kérelem lekérdezési paramétere.
**értelmezések** |0 vagy több különböző módon, a nyelvtanhoz való felhasználói bevitel egyeztetésére szolgáló tömb.
**interpretations[x].logprob**  |Az értelmezés relatív természetes naplójának valószínűsége. Nagyobb a valószínűsége.
**interpretations[x].parse**  |Egy XML-karakterlánc, amely a lekérdezés egyes részeinek értelmezését mutatja.
**értelmezések [x]. szabályok**  |A nyelvtanban az értelmezés során meghívott 1 vagy több szabály tömbje. A Academic Knowledge API esetében mindig 1 szabály lesz.
**interpretations[x].rules[y].name**  |A szabály neve.
**interpretations[x].rules[y].output**  |A szabály kimenete.
**interpretations[x].rules[y].output.type** |A szabály kimenetének adattípusa.  A Academic Knowledge API esetében ez mindig "lekérdezés" lesz.
**értelmezések [x]. rules [y]. output. Value**  |A szabály kimenete. A Academic Knowledge API esetében ez egy lekérdezési kifejezés karakterlánca, amely átadható a kiértékelési és számítási hisztogram metódusoknak.
**megszakadt** | Értéke true, ha a kérelem időtúllépést adott meg.

<br>

#### <a name="example"></a>Példa:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Az alábbi válasz tartalmazza a legfelső kettőt (a paraméterek *száma = 2*) a legvalószínűbb értelmezéseket, amelyek a részleges felhasználói adatbevitelt végzik *el Jaime*szerint: dokumentumok, Jaime *teevan* és Papers by *Jaime Green*.  A szolgáltatás által generált lekérdezések befejezése ahelyett, hogy csak a pontos egyezéseket kellene figyelembe venni a szerzői *Jaime* esetében, mert a kérelem *teljes = 1*értéket adott meg. Vegye figyelembe, hogy a " *j l* " kanonikus érték a *Jamie Green*szinonimán keresztül illeszkedik, az elemzésben jelzett módon.


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
<br>Ha egy értelmezéshez szeretné beolvasni az entitások eredményeit, használja a *output. Value értéket* az **értelmező** API-ból, és adja át ezt a kiértékelési API-nak a *kifejezés* paraméter használatával. Ebben a példában az első értelmezés lekérdezése a következő: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
