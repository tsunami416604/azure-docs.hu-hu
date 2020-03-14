---
title: Szemantikai értelmezés – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a szemantikai értelmezést a Knowledge Exploration Service (KES) API-ban.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220060"
---
# <a name="semantic-interpretation"></a>Szemantikai értelmezés

A szemantikai értelmezés szemantikai kimenetet társít az összes értelmezett útvonalhoz a nyelvtanon keresztül.  A szolgáltatás különösen kiértékeli az utasítások sorát az értelmezés által áthaladó `tag` elemekben a végső kimenet kiszámításához.  

Egy utasítás lehet egy literál vagy egy változó hozzárendelése egy másik változóhoz.  Egy függvény kimenetét is hozzárendelheti 0 vagy több paraméterrel egy változóhoz.  Az egyes functions paramétereket literál vagy változó használatával lehet megadni.  Ha a függvény nem ad vissza kimenetet, a hozzárendelés kimarad.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Egy változót egy betűvel kezdődő azonosítóval kell megadni, amely csak betűkből (A-Z), számokból (0-9) és az aláhúzásból (\_) áll.  A típust implicit módon következtetik ki a hozzá rendelt literál vagy függvény kimeneti értékből. 

Az alábbi lista a jelenleg támogatott adattípusokat tartalmazza:

|Típus|Leírás|Példák|
|----|----|----|
|Sztring|0 vagy több karakteres sorszám|""Helló világ!"alkalmazás!"<br/>""|
|Bool|Logikai érték|true<br/>false|
|Int32|32 bites előjeles egész szám.  -2.1 E9 – 2.1 E9|123<br/>-321|
|Int64|64 bites előjeles egész szám. -9.2 E18 és 9.2 E18|9876543210|
|Dupla|Dupla pontosságú lebegőpontos pont. 1.7 e +/-308 (15 számjegy)|123.456789<br/>1.23456789e2|
|Guid|Globálisan egyedi azonosító|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Lekérdezés|Az indexben található adatobjektumok részhalmazát megadó lekérdezési kifejezés|Összes ()<br/>És (*Q1*, *Q2*)|

## <a name="semantic-functions"></a>Szemantikai függvények

A szemantikai függvények beépített halmaza.  Lehetővé teszik a kifinomult lekérdezések kiépítését, és környezetfüggő szabályozást biztosítanak a nyelvtani értelmezésekhez.

### <a name="and-function"></a>És függvény

`query = And(query1, query2);`

A két bemeneti lekérdezés metszéspontján álló lekérdezést ad vissza.

### <a name="or-function"></a>Vagy függvény

`query = Or(query1, query2);`

Egy olyan lekérdezést ad vissza, amely a két bemeneti lekérdezés Uniójának tagjaiból áll.

### <a name="all-function"></a>Minden függvény

`query = All();`

Egy olyan lekérdezést ad vissza, amely az összes adatobjektumot tartalmazza.

Az alábbi példában az All () függvényt használjuk, hogy iteratív egy lekérdezést az 1 vagy több kulcsszó metszéspontja alapján.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Nincs függvény

`query = None();`

Az adatobjektumokat nem tartalmazó lekérdezést ad vissza.

A következő példában a none () függvényt használjuk, hogy iteratív egy lekérdezést az 1 vagy több kulcsszóból álló Unió alapján.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Lekérdezési függvény

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Egy olyan lekérdezést ad vissza, amely csak olyan adatobjektumokat tartalmaz, amelyek attribútuma a megadott *attrName* *megfelel az értéknek* *, ami*alapértelmezés szerint "EQ".  Általában egy `attrref` elemmel hozhat létre egy lekérdezést a megfeleltetett bemeneti lekérdezési karakterlánc alapján.  Ha egy értéket adott meg vagy más módon szerez be, a lekérdezés () függvény használatával létrehozhat egy, az értéknek megfelelő lekérdezést.

A következő példában a Query () függvényt használjuk egy adott évtizedből származó akadémiai kiadványok megadásának támogatásához.

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

Egy olyan lekérdezést ad vissza, amely egy, a közös összetett attribútum *attr*tartozó alattribútumokból álló *innerQuery* ágyaz be.  A beágyazáshoz a megfelelő adatobjektumhoz tartozó összetett attribútum *attr* legalább egy olyan értékkel kell rendelkeznie, amely külön megfelel a *innerQuery*.  Vegye figyelembe, hogy egy összetett attribútum alattribútumain lévő lekérdezést az összetett () függvény használatával kell beágyazni, mielőtt más lekérdezésekkel kombinálhatók.

Például a következő lekérdezés a "Harry Sum" nevű akadémiai kiadványokat adja vissza, miközben a "Microsoft" volt:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Másfelől a következő lekérdezés olyan akadémiai kiadványokat ad vissza, amelyekben a szerzők egyike a "Harry Sum", a másik pedig a "Microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable függvény

`value = GetVariable(name, scope);`

A megadott *hatókörben*definiált változó *nevének* értékét adja vissza.  a *Name* olyan azonosító, amely betűvel kezdődik, és csak betűkből (a-Z), számokból (0-9) és aláhúzásból (_) áll.  a *hatókör* beállítható "kérelem" vagy "System" értékre.  Vegye figyelembe, hogy a különböző hatókörökben definiált változók különböznek egymástól, beleértve azokat is, amelyeket a szemantikai függvények kimenete határoz meg.

A kérelem hatókör-változói az aktuális értelmezési kérelemben szereplő összes értelmezés között megoszthatók.  Használhatók a nyelvtani értelmezések keresésének szabályozására.

A szolgáltatás a rendszerváltozókat előre definiálja, és a rendszer aktuális állapotával kapcsolatos különböző statisztikák lekérésére használható.  Alább láthatók a jelenleg támogatott rendszerváltozók:

|Name (Név)|Típus|Leírás|
|----|----|----|
|IsAtEndOfQuery|Bool|igaz, ha az aktuális értelmezés megfelelt az összes bemeneti lekérdezési szövegnek|
|IsBeyondEndOfQuery|Bool|igaz, ha az aktuális értelmezés a bemeneti lekérdezési szövegen felüli befejezést javasolt|

### <a name="setvariable-function"></a>SetVariable függvény

`SetVariable(name, value, scope);`

Az *értéket* a megadott *hatókörben*lévő változó *neveként* rendeli hozzá.  a *Name* olyan azonosító, amely betűvel kezdődik, és csak betűkből (a-Z), számokból (0-9) és aláhúzásból (_) áll.  Jelenleg a *hatókör* egyetlen érvényes értéke "Request".  Nincsenek beállítható rendszerváltozók.

A kérelem hatókör-változói az aktuális értelmezési kérelemben szereplő összes értelmezés között megoszthatók.  Használhatók a nyelvtani értelmezések keresésének szabályozására.

### <a name="assertequals-function"></a>AssertEquals függvény

`AssertEquals(value1, value2);`

Ha a *érték1* és a *érték2* egyenértékű, a függvény sikeres lesz, és nincs mellékhatása.  Ellenkező esetben a függvény meghiúsul, és elutasítja az értelmezést.

### <a name="assertnotequals-function"></a>AssertNotEquals függvény

`AssertNotEquals(value1, value2);`

Ha a *érték1* és a *érték2* nem egyenértékűek, a függvény sikeres lesz, és nincs mellékhatása.  Ellenkező esetben a függvény meghiúsul, és elutasítja az értelmezést.


