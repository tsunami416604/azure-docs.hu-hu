---
title: A Tudásbázis feltárása Service API szemantikai értelmező |} Microsoft Docs
description: Megtudhatja, hogyan használja a szemantikai értelmező a a Tudásbázis feltárása szolgáltatás (KES) API kognitív szolgáltatásban.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 022188464eb7269b69f96a058b444167b587387c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347158"
---
# <a name="semantic-interpretation"></a>Szemantikai értelmező
Szemantikai értelmező szemantikai kimeneti társítja a nyelvtan minden értelmezett elérési úttal.  Különösen a szolgáltatás értékeli az lévő utasítások sorrendje a `tag` elemek haladnia a végső kimenetet kiszámításához értelmezése.  

Egy utasítás hozzárendelése egy vagy egy változó egy másik változó lehet.  Egy változóhoz is rendelhet hozzá a kimenet egy függvény 0 vagy több paramétert.  Minden egyes függvényparaméter szövegkonstans vagy változó adható meg.  A függvény nem ad eredményül kimenetet, ha meg van adva a-hozzárendelés.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

A változó meg van adva, a betűvel kezdődő, és csak betűket (A – Z), számok (0-9) és a aláhúzásjelet tartalmaz azonosítója használatával (\_).  A literál implicit módon van típusára következtetni vagy függvény kimeneti értéke rendelve. 

Az alábbiakban olvashat egy listát a jelenleg támogatott adattípusok:

|Típus|Leírás|Példák|
|----|----|----|
|Sztring|0 vagy több karakter|"Hello World!"<br/>""|
|Logikai érték|Logikai érték|true<br/>false|
|Int32|32 bites előjeles egész szám.  -2.1e9 való 2.1e9|123<br/>-321|
|Int64|64 bites előjeles egész szám. -9.2e18 és 9.2e18|9876543210|
|Dupla|A dupla pontosságú lebegőpontos. 1.7E +/-308 (15 számjegy)|123.456789<br/>1.23456789e2|
|GUID|Globálisan egyedi azonosítója|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Lekérdezés|Kifejezés, amely megadja az adatok objektumok egy részét az index lekérdezése|All()<br/>És (*V1*, *v2*)|

<a name="semantic-functions"></a>
## <a name="semantic-functions"></a>A szemantikai funkciók
Nincs olyan szemantikai funkciók beépített készlete.  Ezek lehetővé teszik a bonyolult lekérdezések létrehozása, és adja meg a környezetfüggő ellenőrzése alatt tartja a nyelvtan értelmezéseket.

### <a name="and-function"></a>És funkció
`query = And(query1, query2);`

Egy lekérdezést össze a két bemeneti lekérdezések metszetét adja vissza.

### <a name="or-function"></a>Vagy nem működik
`query = Or(query1, query2);`

Két bemeneti lekérdezések Uniója alapján álló lekérdezés adja vissza.

### <a name="all-function"></a>Az összes funkció
`query = All();`

A lekérdezés, amely tartalmazza az összes adja vissza.

A következő példában használjuk a All() függvény alapján legalább 1 kulcsszavak metszetét lekérdezés ismételt kialakításához.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Egyik sem működik
`query = None();`

Egyetlen objektum sem adatokat tartalmazó adja vissza.

A következő példában használjuk a None() függvény legalább 1 kulcsszavak Uniója alapján lekérdezés ismételt kialakításához.

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

Adja vissza csak adatok objektumokat tartalmazó lekérdezést, amelyek attribútuma *attrName* megegyezik-e *érték* szerint a megadott művelet *op*, amely az alapértelmezett "eq".  Általában, egy `attrref` elem alapján a megfelelő lekérdezési karakterlánc-lekérdezés létrehozásához.  Értéket kap, vagy más módon szerezte be, ha a Query() funkció segítségével hozzon létre egy lekérdezést, ez az érték megfelelő.

A következő példában használjuk a Query() függvény valósítja meg az adott évtizedben academic kiadványok megadó támogatása.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

<a name="composite-function"/>
### <a name="composite-function"></a>Összetett függvény
`query = Composite(innerQuery);`

Visszaadja egy lekérdezést, amely magában foglalja egy *innerQuery* közös összetett attribútum alárendelt attribútumok egyezések álló *attr*.  A beágyazás megköveteli, az összetett attribútum *attr* bármely megfelelő adatobjektum rendelkeznie kell legalább egy értéket, amely egyedileg megfelel a *innerQuery*.  Vegye figyelembe, hogy a lekérdezés egy összetett attribútum alárendelt attribútumait egyesítse, mielőtt további lekérdezések kombinálható Composite() funkcióval.

Például a következő lekérdezés eredményében academic kiadványok szerint "harry shum" közben: "microsoft" volt:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

A következő lekérdezést, másrészt academic kiadványok esetében, ahol a szerzők egyik "harry shum" és a nézeteihez egyik "microsoft" adja vissza:
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable függvény
`value = GetVariable(name, scope);`

A változó értékét adja vissza *neve* a megadott definiált *hatókör*.  *név* azonosító betűvel kezdődik, és csak betűket (A – Z), számok (0-9) és aláhúzást (_) tartalmaz.  *hatókör* "kérelmek" vagy "rendszer" állítható be.  Vegye figyelembe, hogy más hatóköröknek meghatározott változókhoz egymástól, például definiálva szemantikai függvények kimenete keresztül.

Kérelem hatókör változók a jelenlegi interpret kérelem belül minden értelmezéseket között vannak megosztva.  A értelmezéseket keressen rá a nyelvtan keresztül vezéreljen használható.

A szolgáltatás által előre meghatározott rendszerváltozók, és segítségével különböző statisztikákról, a rendszer az aktuális állapotával kapcsolatos információkat lekérni.  Az alábbiakban az a jelenleg támogatott rendszerváltozók csoportja:

|Name (Név)|Típus|Leírás|
|----|----|----|
|IsAtEndOfQuery|Logikai érték|IGAZ, ha az aktuális értelmezési rendelkezik megfelel az összes lekérdezési szöveg|
|IsBeyondEndOfQuery|Logikai érték|IGAZ, ha az aktuális értelmezése javasolt fel a bemeneti lekérdezés szövegének túl|

### <a name="setvariable-function"></a>SetVariable függvény
`SetVariable(name, value, scope);`

Hozzárendel *érték* változóhoz *neve* a megadott *hatókör*.  *név* azonosító betűvel kezdődik, és csak betűket (A – Z), számok (0-9) és aláhúzást (_) tartalmaz.  Jelenleg az egyetlen érvényes érték a *hatókör* "kérelmek" van.  Nincsenek nem állítható be rendszerváltozók.

Kérelem hatókör változók a jelenlegi interpret kérelem belül minden értelmezéseket között vannak megosztva.  A értelmezéseket keressen rá a nyelvtan keresztül vezéreljen használható.

### <a name="assertequals-function"></a>AssertEquals függvény
`AssertEquals(value1, value2);`

Ha *érték1* és *érték2* egyenértékű, a függvény sikeres lesz, és nincs ügyféloldali hatással.  Ellenkező esetben a függvény nem sikerül, és értelmezési elutasítja.

### <a name="assertnotequals-function"></a>AssertNotEquals függvény
`AssertNotEquals(value1, value2);`

Ha *érték1* és *érték2* nem egyenértékű, a függvény sikeres lesz, és nincs ügyféloldali hatással.  Ellenkező esetben a függvény nem sikerül, és értelmezési elutasítja.


