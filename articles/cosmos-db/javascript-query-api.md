---
title: A JavaScript nyelvvel integrált lekérdezési API használata Azure Cosmos DB
description: Ez a cikk bemutatja a JavaScript nyelvre épülő lekérdezési API fogalmait a tárolt eljárások és triggerek létrehozásához Azure Cosmos DBban.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 01e5e95da3c19c03d07c7f3c1d716f5f1e97de98
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717595"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript lekérdezési API a Azure Cosmos DB

A Azure Cosmos DBban található SQL API-val való lekérdezések mellett a [Cosmos db KISZOLGÁLÓOLDALI SDK](https://azure.github.io/azure-cosmosdb-js-server/) lehetővé teszi, hogy JavaScript-felület használatával optimalizált lekérdezéseket végezzen. Ennek a JavaScript-felületnek a használatához nem kell tisztában lennie az SQL-nyelvvel. A JavaScript lekérdezési API lehetővé teszi, hogy programozott módon hozza létre a lekérdezéseket úgy, hogy a predikátum függvényeit átadja a függvények sorrendjének, és egy olyan szintaxist, amely ismerős a ECMAScript5's Array beépített és népszerű JavaScript-kódtárak, például a Lodash használatával. A lekérdezéseket a JavaScript futtatókörnyezet elemzi, és a Azure Cosmos DB indexek használatával hatékonyan hajtja végre.

## <a name="supported-javascript-functions"></a>Támogatott JavaScript-függvények

| **Függvény** | **Leírás** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Value() –, be kell fejezni láncolt hívás kezdődik.|
|`filter(predicateFunction [, options] [, callback])`|A predikátum függvény, amely ad vissza igaz/hamis érték, ha a bejövő és kimenő bemeneti dokumentumok az eredményül kapott csoportba használatával bemeneti szűri. Ez a függvény működését hasonló WHERE záradék az SQL-ben.|
|`flatten([isShallow] [, options] [, callback])`|Az egyes bemeneti elemek tömbjét egyetlen tömbbe kombinálja és lelapulja. Ez a függvény működését a LINQ SelectMany hasonló.|
|`map(transformationFunction [, options] [, callback])`|Egy leképezési megadott átalakító függvény, amely minden egyes bemeneti elem képez le egy JavaScript-objektumot vagy értéket vonatkozik. Ez a függvény működését a SELECT záradékban az SQL hasonló.|
|`pluck([propertyName] [, options] [, callback])`|Ez a funkció akkor látható, hogy egyetlen tulajdonság értéke kigyűjti az egyes bemeneti elem parancsikont.|
|`sortBy([predicate] [, options] [, callback])`|Új dokumentumokat hoz létre azáltal, hogy a megadott predikátum használatával növekvő sorrendben rendezi a dokumentumokat a bemeneti dokumentum adatfolyamában. Ez a függvény működését egy ORDER BY záradékban az SQL hasonló.|
|`sortByDescending([predicate] [, options] [, callback])`|Új dokumentumokat hoz létre a bemeneti dokumentum adatfolyamában lévő dokumentumok csökkenő sorrendbe rendezésével a megadott predikátum használatával. Ez a függvény működését egy x DESC ORDER BY záradékban az SQL hasonló.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Egy önillesztést végez a belső tömbben, és a rekordok mindkét oldaláról az eredményeket adja eredményként. Például személyre szóló dokumentumot kell összekapcsolni. a háziállatok [person, PET] rekordok hoznak létre. Ez hasonló a .NET-hivatkozás SelectMany.|

Belefoglalja predikátum és/vagy választó funkciók belül, a következő JavaScript-szerkezetek automatikusan optimalizálja a közvetlenül az Azure Cosmos DB-indexek futtatásához:

- Egyszerű operátorok `=` : `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literálok, beleértve a objektumliterálhoz: {}
- var, visszatérési

A következő JavaScript-szerkezetek nem optimalizálja az Azure Cosmos DB-indexek:

- Átvitelvezérlés (például ha, miközben)
- Függvényhívások

További információ: [Cosmos db kiszolgálóoldali JavaScript-dokumentáció](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL – JavaScript Cheat Sheet

Az alábbi táblázat bemutatja a különböző SQL-lekérdezések és a kapcsolódó JavaScript-lekérdezéseket. Az SQL-lekérdezésekhez hasonlóan a tulajdonságok (például a item.id) is megkülönböztetik a kis-és nagybetűket.

> [!NOTE]
> `__`(dupla aláhúzás) a JavaScript lekérdezési API `getContext().getCollection()` használatakor használt alias.

|**SQL**|**JavaScript lekérdezési API**|**Leírás**|
|---|---|---|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;doc; vissza<br>});|Az összes dokumentum (többoldalas a folytatási token), az eredményeket.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message AS msg,<br>&nbsp;&nbsp;&nbsp;docs. Actions <br>DOKUMENTÁCIÓ|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projektek, a azonosítója, az üzenet (aliassal való msg) és a művelet az összes dokumentumot.|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc.id === "X998_Y998";<br>});|A predikátum a dokumentumok lekérdezések: azonosító = "X998_Y998".|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Címkék, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Lekérdezések, amelyek a Tags tulajdonságnak és címkék dokumentumok je Pole obsahující a az 123 érték.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. Message AS msg<br>DOKUMENTÁCIÓ<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|A dokumentumok, amelyek egy predikátum lekérdezések id = "X998_Y998", és ezután-projektek, az azonosítót és az üzenet (aliassal való msg).|
|SELECT VALUE címke<br>DOKUMENTÁCIÓ<br>CSATLAKOZZON a docs címke. A címkék<br>Az ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a dokumentumot. A címkék & & Array.isArray (doc. A címkék);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc._ts; vissza<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Vlastnost typu Pole, címkék, rendelkeznie dokumentumoknál szűri és az eredményül kapott dokumentumokat a _ts időbélyeg rendszertulajdonság, majd a projektek + simítja egybe a címkék tömb.|

## <a name="next-steps"></a>További lépések

További fogalmak és útmutató: tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása és használata Azure Cosmos DBban:

- [Tárolt eljárások és triggerek írása JavaScript-lekérdezési API használatával](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata](stored-procedures-triggers-udfs.md)
- [Tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Az Azure Cosmos DB a JavaScript kiszolgálóoldali API-referencia](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
