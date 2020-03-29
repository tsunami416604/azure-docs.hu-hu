---
title: JavaScript-alapú integrált lekérdezési API-val dolgozhat az Azure Cosmos DB tárolt eljárásaiban és eseményindítóiban
description: Ez a cikk bemutatja a JavaScript-nyelvintegrált lekérdezési API-k fogalmait tárolt eljárások és eseményindítók létrehozásához az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901835"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript-lekérdezési API az Azure Cosmos DB-ben

Az Azure Cosmos DB SQL API-t használó lekérdezések kiadása mellett a [Cosmos DB kiszolgálóoldali SDK](https://azure.github.io/azure-cosmosdb-js-server/) javascript-felületet biztosít a Cosmos DB tárolt eljárások és eseményindítók optimalizált lekérdezéseinek végrehajtásához. A JavaScript-felület használatához nem kell tisztában lennie az SQL nyelvével. A JavaScript lekérdezési API lehetővé teszi, hogy programozott módon építeni lekérdezések átadásával predikátum függvények sorrendbe függvényhívások, a szintaxis ismerős ECMAScript5 a tömb beépített és népszerű JavaScript könyvtárak, mint a Lodash. A lekérdezéseket a JavaScript-futásidejű elemzi, és hatékonyan hajtja végre az Azure Cosmos DB indexek használatával.

## <a name="supported-javascript-functions"></a>Támogatott JavaScript-függvények

| **Függvény** | **Leírás** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Láncolt hívást indít, amelyet értékkel() kell leállítani.|
|`filter(predicateFunction [, options] [, callback])`|A bemeneti adatokat egy predikátumfüggvény segítségével szűri, amely igaz/hamis értéket ad vissza, hogy a bemeneti dokumentumok be- és kiszűrése az eredményül kapott halmazba. Ez a függvény az SQL WHERE záradékához hasonlóan viselkedik.|
|`flatten([isShallow] [, options] [, callback])`|Az egyes bemeneti elemek tömbjeit egyetlen tömbbe egyesíti és összeolvasztja. Ez a függvény a LINQ SelectMany függvényhez hasonlóan viselkedik.|
|`map(transformationFunction [, options] [, callback])`|Egy transzformációs függvényt alkalmaz, amely minden bemeneti elemet JavaScript-objektumhoz vagy értékhez rendel. Ez a függvény az SQL SELECT záradékához hasonlóan viselkedik.|
|`pluck([propertyName] [, options] [, callback])`|Ez a függvény egy olyan térkép parancsikonja, amely minden egyes bemeneti elemből egyetlen tulajdonság értékét nyeri ki.|
|`sortBy([predicate] [, options] [, callback])`|Új dokumentumkészletet hoz létre a bemeneti dokumentumadatfolyamban lévő dokumentumok növekvő sorrendben történő rendezésével a megadott predikátum használatával. Ez a függvény az SQL ORDER BY záradékához hasonlóan viselkedik.|
|`sortByDescending([predicate] [, options] [, callback])`|Új dokumentumkészletet hoz létre a bemeneti dokumentumadatfolyamban lévő dokumentumok csökkenő sorrendben történő rendezésével a megadott predikátum használatával. Ez a függvény az SQL ORDER BY x DESC záradékához hasonlóan viselkedik.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Önillesztést hajt végre a belső tömbbel, és mindkét oldalról ad eredményt az eredményvetéshez. Például, követ egy személy dokumentum -val person.pets akar termel [személy, kisállat] tuples. Ez hasonló a .NET LINK SelectMany alkalmazásához.|

Ha a predikátum- és/vagy választófüggvényeken belül található, a következő JavaScript-konstrukciók automatikusan optimalizálva lesznek, hogy közvetlenül az Azure Cosmos DB-indexein fussanak:

- Egyszerű `=` `+` `-` `*` `/` `%` `|` operátorok: `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Konstansok, beleértve az objektum konstans:{}
- var, visszatérés

A következő JavaScript-konstrukciók nem lesznek optimalizálva az Azure Cosmos DB indexeihez:

- Vezérlési folyamat (például, ha, például, while)
- Függvényhívások

További információt a [Cosmos DB Server Side JavaScript dokumentációjában talál.](https://azure.github.io/azure-cosmosdb-js-server/)

## <a name="sql-to-javascript-cheat-sheet"></a>SQL a JavaScript cheat sheet

Az alábbi táblázat különböző SQL-lekérdezéseket és a megfelelő JavaScript-lekérdezéseket mutatja be. Az SQL-lekérdezésekhez hasonlóan a tulajdonságok (például item.id) is a kis- és nagybetűket megkülönböztetők.

> [!NOTE]
> `__`(dupla aláhúzás) egy `getContext().getCollection()` alias, ha a JavaScript lekérdezési API használatakor.

|**SQL**|**JavaScript-lekérdezési API**|**Leírás**|
|---|---|---|
|VÁLASSZA A *<br>FROM dokumentumok| __.térkép(függvény(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc- ot;<br>});|Az eredmények az összes dokumentumot (oldalszámmal folytatólagos token), ahogy van.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM dokumentumok|__.térkép(függvény(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;műveletek:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Kivetíti az azonosítót, az üzenetet (aliasos msg) és a műveletet az összes dokumentumból.|
|VÁLASSZA A *<br>FROM dokumentumok<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;visszaad doc.id ==="X998_Y998";<br>});|Predikátummal rendelkező dokumentumok lekérdezései: id = "X998_Y998".|
|VÁLASSZA A *<br>FROM dokumentumok<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs. Címkék, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;x.Tags && x.Tags.indexOf(123) > -1;<br>});|A Címkék tulajdonsággal és Címkékkel rendelkező dokumentumok lekérdezése a 123-as értéket tartalmazó tömb.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM dokumentumok<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.lánc()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;visszaad doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.érték();|Predikátumú dokumentumok lekérdezései, id = "X998_Y998", majd kivetítik az azonosítót és az üzenetet (alias nevén msg).|
|ÉRTÉK címke kiválasztása<br>FROM dokumentumok<br>JOIN címke a dokumentumokban. Címkék<br>RENDELÉS DOCS._ts|__.lánc()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc. Címkék && Array.isArray(doc. Címkék);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza adja a doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.kopasztás("Címkék")<br>&nbsp;&nbsp;&nbsp;&nbsp;.leolvasztó()<br>&nbsp;&nbsp;&nbsp;&nbsp;.érték()|A tömbtulajdonsággal, Címkékkel és az eredményül kapott dokumentumoknak az _ts időbélyegrendszer-tulajdonsággal, majd a projektek + a Címkék tömb összeolvasztásával kapcsolatos dokumentumok szűrői.|

## <a name="next-steps"></a>További lépések

További fogalmak és a tárolt eljárások, eseményindítók és a felhasználó által definiált függvények írásának és használatának módjáról az Azure Cosmos DB-ben:

- [Tárolt eljárások és eseményindítók írása javascript-lekérdezési API használatával](how-to-write-javascript-query-api.md)
- [Az Azure Cosmos DB tárolt eljárásainak, eseményindítóinak és felhasználó által definiált függvényeknek a használatával](stored-procedures-triggers-udfs.md)
- [Tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript kiszolgálóoldali API-referencia](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
