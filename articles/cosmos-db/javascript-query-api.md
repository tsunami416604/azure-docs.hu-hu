---
title: A JavaScript integrált lekérdezési API használata Azure Cosmos DB
description: Ez a cikk bemutatja a JavaScript nyelvre épülő lekérdezési API fogalmait a tárolt eljárások és triggerek létrehozásához Azure Cosmos DBban.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 8396608cdbc5638a3640f94c94b44ad7c5f52a73
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445315"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript lekérdezési API a Azure Cosmos DB

A Azure Cosmos DBban található SQL API-val való lekérdezések mellett a [Cosmos db KISZOLGÁLÓOLDALI SDK](https://azure.github.io/azure-cosmosdb-js-server/) lehetővé teszi, hogy JavaScript-felület használatával optimalizált lekérdezéseket végezzen. Ennek a JavaScript-felületnek a használatához nem kell tisztában lennie az SQL-nyelvvel. A JavaScript lekérdezési API lehetővé teszi, hogy programozott módon hozza létre a lekérdezéseket úgy, hogy a predikátum függvényeit átadja a függvények sorrendjének, és egy olyan szintaxist, amely ismerős a ECMAScript5's Array beépített és népszerű JavaScript-kódtárak, például a Lodash használatával. A lekérdezéseket a JavaScript futtatókörnyezet elemzi, és a Azure Cosmos DB indexek használatával hatékonyan hajtja végre.

## <a name="supported-javascript-functions"></a>Támogatott JavaScript-függvények

| **Függvény** | **Leírás** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Elindít egy láncolt hívást, amelyet a Value () értékkel kell megszakítani.|
|`filter(predicateFunction [, options] [, callback])`|Kiszűri a bemenetet egy olyan predikátum-függvénnyel, amely igaz/hamis értéket ad vissza a bemeneti dokumentumok a létrejövő készletbe való szűréséhez. Ez a függvény a WHERE záradékhoz hasonlóan viselkedik az SQL-ben.|
|`flatten([isShallow] [, options] [, callback])`|Az egyes bemeneti elemek tömbjét egyetlen tömbbe kombinálja és lelapulja. Ez a függvény a LINQ SelectMany hasonló módon viselkedik.|
|`map(transformationFunction [, options] [, callback])`|Egy olyan átalakítási függvényt alkalmaz, amely az egyes bemeneti elemeket egy JavaScript-objektumra vagy-értékre képezi le. Ez a függvény az SQL SELECT záradékához hasonlóan viselkedik.|
|`pluck([propertyName] [, options] [, callback])`|Ez a függvény egy olyan Térkép parancsikonja, amely kibontja az egyes bemeneti elemek egyetlen tulajdonságának értékét.|
|`sortBy([predicate] [, options] [, callback])`|Új dokumentumokat hoz létre azáltal, hogy a megadott predikátum használatával növekvő sorrendben rendezi a dokumentumokat a bemeneti dokumentum adatfolyamában. Ez a függvény az SQL ORDER BY záradékához hasonlóan viselkedik.|
|`sortByDescending([predicate] [, options] [, callback])`|Új dokumentumokat hoz létre a bemeneti dokumentum adatfolyamában lévő dokumentumok csökkenő sorrendbe rendezésével a megadott predikátum használatával. Ez a függvény az SQL-ben az ORDER BY x DESC záradékhoz hasonlóan viselkedik.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Egy önillesztést végez a belső tömbben, és a rekordok mindkét oldaláról az eredményeket adja eredményként. Például személyre szóló dokumentumot kell összekapcsolni. a háziállatok [person, PET] rekordok hoznak létre. Ez hasonló a .NET-hivatkozás SelectMany.|

A predikátum és/vagy választó függvények részét képező következő JavaScript-összeállítások automatikusan Azure Cosmos DB indexeken való futtatásra vannak optimalizálva:

- Egyszerű operátorok: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literálok, beleértve az objektum literál: {}
- var, visszatérés

A következő JavaScript-szerkezetek nem optimalizáltak Azure Cosmos DB indexekhez:

- Vezérlési folyamat (például, ha,, míg)
- Függvények hívásai

További információ: [Cosmos db kiszolgálóoldali JavaScript-dokumentáció](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL – JavaScript Cheat Sheet

Az alábbi táblázat különböző SQL-lekérdezéseket és a kapcsolódó JavaScript-lekérdezéseket ismerteti. Az SQL-lekérdezésekhez hasonlóan a tulajdonságok (például a item.id) is megkülönböztetik a kis-és nagybetűket.

> [!NOTE]
> a `__` (dupla aláhúzás) a JavaScript lekérdezési API használatakor `getContext().getCollection()` alias.

|**SQL**|**JavaScript lekérdezési API**|**Leírás**|
|---|---|---|
|Válassza<br>A docs-ból| __. map (függvény (doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;visszatérési doc;<br>});|A rendszer az összes dokumentumot (a folytatási tokent tartalmazó tördeléssel) jeleníti meg.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message AS msg,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>A docs-ból|__. map (függvény (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;visszatérés {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;azonosítója: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc. Message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;műveletek: doc. Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Megtervezi az azonosítót, az üzenetet (az msg-aliast) és az összes dokumentum műveletét.|
|Válassza<br>A docs-ból<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. id = "X998_Y998"|__. Filter (függvény (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;visszatérési doc.id = = = "X998_Y998";<br>});|Dokumentumok lekérdezése a következő predikátummal: azonosító = "X998_Y998".|
|Válassza<br>A docs-ból<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Címkék, 123)|__. Filter (függvény (x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza x. címkék & & x. Tags. indexOf (123) >-1;<br>});|A címkék tulajdonsággal rendelkező dokumentumok lekérdezései egy olyan tömb, amely a 123 értéket tartalmazza.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message AS msg<br>A docs-ból<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs. id = "X998_Y998"|__. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (függvény (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;visszatérési doc.id = = = "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. map (függvény (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;azonosítója: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc. Message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>. Value ();|A dokumentumok lekérdezése predikátummal, azonosító = "X998_Y998", majd a projekt az azonosító és az üzenet (msg-ben alias).|
|ÉRTÉK címke kiválasztása<br>A docs-ból<br>JOIN tag a docs-ban. Címkék<br>ORDER BY docs. _ts|__. Chain ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Filter (függvény (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;visszatérési doc. Címkék & & Array. isArray (doc). Címkék);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. sortBy (Function (doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;visszatérési doc. _ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;. összeszedés ("címkék")<br>&nbsp;&nbsp;&nbsp;&nbsp;. simítsa ()<br>&nbsp;&nbsp;&nbsp;&nbsp;. Value ()|A Array tulajdonsággal rendelkező dokumentumok szűrői, a címkék és az eredményül kapott dokumentumok rendezése a _ts timestamp System tulajdonsággal, majd a projects + lelapul a címkék tömbjét.|

## <a name="next-steps"></a>Következő lépések

További fogalmak és útmutató: tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása és használata Azure Cosmos DBban:

- [Tárolt eljárások és triggerek írása JavaScript-lekérdezési API használatával](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata](stored-procedures-triggers-udfs.md)
- [Tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript kiszolgálóoldali API-referenciája](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
