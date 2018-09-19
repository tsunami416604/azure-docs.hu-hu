---
title: Szemantikai értelmezés – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a szemantikai értelmezés a a Knowledge Exploration Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 5fcc7b760b5445e57b41787d8818ef11ed926e6c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129352"
---
# <a name="semantic-interpretation"></a>Szemantikai értelmezés

Szemantikai értelmezés szemantikai kimeneti mindegyik értelmezett elérési út a szintaxis használatával társítja.  Ilyen például a szolgáltatás kiértékeli az parancsfájlblokkokban lévő utasítások sorrendje a `tag` számítja ki a végső kimenet értelmezésének haladnia elemek.  

Lehet, hogy egy utasítás szövegkonstans vagy egy változót, egy másik változó-hozzárendelés.  Változóhoz is rendelhet hozzá a kimenet egy függvény 0 vagy több paramétert.  Minden függvény paramétere egy vagy egy változó adható meg.  Ha a függvény nem ad vissza kimenetet, a hozzárendelés van hagyva.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

A változó meg van adva, amely betűvel kezdődik, és csak betűket (A – Z), számjegyek (0 – 9) és az aláhúzás tartalmaz alkalmazásnév-azonosító használatával (\_).  A típusa implicit módon fióktól vette a beállításait az a konstans vagy függvény kimeneti hozzárendelt érték. 

Az alábbiakban felsoroljuk a jelenleg támogatott adattípusok:

|Típus|Leírás|Példák|
|----|----|----|
|Sztring|0 vagy több karakter sorozata|"Hello World!"<br/>""|
|Logikai|Logikai érték|true<br/>false|
|Int32|32 bites előjeles egész számokat.  -2.1e9 való 2.1e9|123<br/>-321|
|Int64|64 bites előjeles egész számokat. -9.2e18 és 9.2e18|9876543210|
|Dupla|Kétszeres pontosságú lebegőpontos. 1.7E +/-308 (15 számjegy)|123.456789<br/>1.23456789e2|
|GUID|Globálisan egyedi azonosító|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Lekérdezés|Lekérdezési kifejezés, amely meghatározza az objektumok egy részét az indexben|All()<br/>És (*q1*, *q2*)|

## <a name="semantic-functions"></a>Szemantikai funkciók

Nincs beépített szemantikai funkciók készletét.  Lehetővé teszi az összetett lekérdezések építését, és adja meg a nyelvtani értelmezések környezetfüggő felett.

### <a name="and-function"></a>És a függvény

`query = And(query1, query2);`

A lekérdezés feloldásra használt két bemeneti lekérdezést metszetét adja vissza.

### <a name="or-function"></a>Vagy függvény

`query = Or(query1, query2);`

Egy lekérdezés feloldásra használt két bemeneti lekérdezést unióját adja vissza.

### <a name="all-function"></a>Minden függvény

`query = All();`

A lekérdezés, amely tartalmazza az összes adat objektum adja vissza.

A következő példában a All() függvényt használjuk iteratív építse fel a lekérdezés 1 vagy több kulcsszavak metszetét alapul.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Egyik sem működik

`query = None();`

A lekérdezés, amely tartalmazza az objektumok nem ad vissza.

A következő példában a None() függvényt használjuk iteratív építse fel egy lekérdezést a union, 1 vagy több kulcsszavak alapján.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Lekérdezés függvény

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Adja vissza egy lekérdezést, amely csak az adatok objektumokat tartalmazza, amelyek attribútuma *attrName* érték megegyezik *érték* szerint a megadott művelet *op*, amely az alapértelmezett "eq".  Általában egy `attrref` elem az egyező bemeneti lekérdezési karakterlánc alapján egy lekérdezés létrehozásához.  Ha értéket adott vagy egyéb módon beszerzett, a Query() függvény ezt az értéket a megfelelő lekérdezés létrehozására használható.

A következő példában a Query() függvényt használjuk adjon meg egy adott évtizede nyújt védelmet a tanulmányi kiadványok támogatása megvalósításához.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Összetett függvény

`query = Composite(innerQuery);`

Adja vissza egy lekérdezést, amely magában foglalja egy *innerQuery* mikroszolgáltatásokból álló, egy közös összetett attribútum alárendelt attribútumai egyezések *attr*.  A beágyazás igényel az összetett attribútum *attr* bármely megfelelő adatok objektum legalább egy olyan értékkel, amely külön-külön eleget tesz a *innerQuery*.  Vegye figyelembe, hogy rendelkezik-e a lekérdezés egy összetett attribútum alárendelt attribútumok egyesítse a Composite() függvény használatával, mielőtt kombinálható más lekérdezések.

Például a következő lekérdezés adja vissza "harry shum" academic kiadványok szerint közben a "Microsoft" volt:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

A következő lekérdezést, másrészt academic kiadványok esetében, ahol a szerző egyik "harry shum" és a tagságok egyik "microsoft" adja vissza:
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable függvény

`value = GetVariable(name, scope);`

A változó értékét adja vissza *neve* alatt a megadott meghatározott *hatókör*.  *név* azonosítója, amely betűvel kezdődik, és csak betűket (A – Z), számjegyek (0 – 9) és aláhúzást (_) tartalmaz.  *hatókör* "kérés" vagy "rendszer" értékre lehet beállítani.  Vegye figyelembe, hogy a különböző hatókörökhöz definiált változókat elkülönülnek egymástól, beleértve a szemantikai függvények kimenete keresztül definiálva.

Kérelem hatókör változókat az aktuális kérelem értelmezése belül minden értelmezések vannak megosztva.  Értelmezések keresése szabályozhatóbbá nyelvtani használható.

Rendszerváltozók a szolgáltatás által előre meghatározott, és használható különböző statisztikákról, a rendszer az aktuális állapotával kapcsolatos információkat lekérni.  Alul látható a jelenleg támogatott rendszerváltozók készletét:

|Name (Név)|Típus|Leírás|
|----|----|----|
|IsAtEndOfQuery|Logikai|IGAZ, ha az aktuális értelmezése felelt meg az összes bemeneti lekérdezés szövege|
|IsBeyondEndOfQuery|Logikai|IGAZ, ha az aktuális értelmezése javasolt befejezésekből túl a bemeneti lekérdezés szövege|

### <a name="setvariable-function"></a>SetVariable függvény

`SetVariable(name, value, scope);`

Hozzárendeli *érték* változóhoz *neve* a megadott *hatókör*.  *név* azonosítója, amely betűvel kezdődik, és csak betűket (A – Z), számjegyek (0 – 9) és aláhúzást (_) tartalmaz.  Jelenleg az egyetlen érvényes érték a *hatókör* van a "request".  Nincsenek nem állítható be rendszerváltozók.

Kérelem hatókör változókat az aktuális kérelem értelmezése belül minden értelmezések vannak megosztva.  Értelmezések keresése szabályozhatóbbá nyelvtani használható.

### <a name="assertequals-function"></a>AssertEquals függvény

`AssertEquals(value1, value2);`

Ha *érték1* és *value2* megfelelője, a függvény sikeres lesz, és még nincsenek hatásai.  Ellenkező esetben a függvény sikertelen lesz, és értelmezése elutasítja.

### <a name="assertnotequals-function"></a>AssertNotEquals függvény

`AssertNotEquals(value1, value2);`

Ha *érték1* és *value2* nem egyenértékű, a függvény sikeres lesz, és még nincsenek hatásai.  Ellenkező esetben a függvény sikertelen lesz, és értelmezése elutasítja.


