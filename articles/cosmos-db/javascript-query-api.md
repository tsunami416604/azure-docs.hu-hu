---
title: A JavaScript nyelvintegrált lekérdezési API-t az Azure Cosmos DB használata
description: Ez a cikk fogalmait a JavaScript nyelvintegrált lekérdezési API-t az Azure Cosmos DB-ben tárolt eljárások és eseményindítók létrehozásához.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 101b5382eaa01ed87f05d83c82002fa1b93144b7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463939"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Az Azure Cosmos DB API a JavaScript-lekérdezés

Mellett dokumentumkeresési lekérdezések az Azure Cosmos DB SQL API használatával a [Cosmos DB kiszolgálóoldali SDK](https://azure.github.io/azure-cosmosdb-js-server/) optimalizált lekérdezések egy JavaScript-felület használatával végezhető el. Vegye figyelembe az SQL-nyelv, a JavaScript-felület használatához nem kell. A JavaScript-lekérdezés API lehetővé teszi a programozott módon hozhat létre a predikátum függvény függvény feladatütemezési történő átadásával lekérdezések meghívja a ECMAScript5 a tömb built-ins és a népszerű JavaScript-kódtárak Lodash hasonló szintaxissal. Lekérdezések a JavaScript futásidejű rendszer elemzi, és hatékonyan az Azure Cosmos DB-indexek használatával végrehajtott.

## <a name="supported-javascript-functions"></a>Támogatott JavaScript-függvények

| **Függvény** | **Leírás** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Value() –, be kell fejezni láncolt hívás kezdődik.|
|`filter(predicateFunction [, options] [, callback])`|A predikátum függvény, amely ad vissza igaz/hamis érték, ha a bejövő és kimenő bemeneti dokumentumok az eredményül kapott csoportba használatával bemeneti szűri. Ez a függvény működését hasonló WHERE záradék az SQL-ben.|
|`flatten([isShallow] [, options] [, callback])`|Egyesíti, és a egy egyetlen tömbbe simítja egybe a tömb minden egyes bemeneti konfigurációelemet. Ez a függvény működését a LINQ SelectMany hasonló.|
|`map(transformationFunction [, options] [, callback])`|Egy leképezési megadott átalakító függvény, amely minden egyes bemeneti elem képez le egy JavaScript-objektumot vagy értéket vonatkozik. Ez a függvény működését a SELECT záradékban az SQL hasonló.|
|`pluck([propertyName] [, options] [, callback])`|Ez a funkció akkor látható, hogy egyetlen tulajdonság értéke kigyűjti az egyes bemeneti elem parancsikont.|
|`sortBy([predicate] [, options] [, callback])`|Létrejön egy olyan új dokumentumok szerint rendezi a dokumentumokat a bemeneti dokumentum Stream növekvő sorrendben az adott predikátum használatával. Ez a függvény működését egy ORDER BY záradékban az SQL hasonló.|
|`sortByDescending([predicate] [, options] [, callback])`|Létrejön egy olyan új dokumentumok szerint rendezi a dokumentumokat a bemeneti dokumentum Stream csökkenő sorrendben az adott predikátum használatával. Ez a függvény működését egy x DESC ORDER BY záradékban az SQL hasonló.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Önillesztés belső tömbbel rendelkező végez, és hozzáadja eredmények mindkét oldalról rekordokat tartalmazó, az eredmény leképezése. Például egy személy dokumentum person.pets való csatlakozás akkor az eredmény [személy, kisállat] rekordokat. Ez hasonlít a .NET-hivatkozás SelectMany.|

Belefoglalja predikátum és/vagy választó funkciók belül, a következő JavaScript-szerkezetek automatikusan optimalizálja a közvetlenül az Azure Cosmos DB-indexek futtatásához:

- Egyszerű kezelők: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literálok, beleértve a objektumliterálhoz: {}
- var, visszatérési

A következő JavaScript-szerkezetek nem optimalizálja az Azure Cosmos DB-indexek:

- Átvitelvezérlés (például ha, miközben)
- Függvényhívások

További információkért lásd: a [Cosmos DB kiszolgáló ügyféloldali JavaScript-dokumentációt](https://azure.github.io/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>SQL-JavaScript – Adatlap

Az alábbi táblázat bemutatja a különböző SQL-lekérdezések és a kapcsolódó JavaScript-lekérdezéseket. SQL-lekérdezéseket, mint a Tulajdonságok (például item.id) nagybetűk között.

> [!NOTE]
> `__` (double-aláhúzásjelet) szerepel aliasként a `getContext().getCollection()` a JavaScript-lekérdezés API használatakor.

|**SQL**|**JavaScript-lekérdezési API**|**Leírás**|
|---|---|---|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;doc; vissza<br>});|Az összes dokumentum (többoldalas a folytatási token), az eredményeket.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message msg, mint<br>&nbsp;&nbsp;&nbsp;docs.Actions <br>DOKUMENTÁCIÓ|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projektek, a azonosítója, az üzenet (aliassal való msg) és a művelet az összes dokumentumot.|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc.id === "X998_Y998";<br>});|A predikátum a dokumentumok lekérdezések: azonosító = "X998_Y998".|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Címkék, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Lekérdezések, amelyek a Tags tulajdonságnak és címkék dokumentumok je Pole obsahující a az 123 érték.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.ID,<br>&nbsp;&nbsp;&nbsp;docs.Message, msg<br>DOKUMENTÁCIÓ<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|A dokumentumok, amelyek egy predikátum lekérdezések id = "X998_Y998", és ezután-projektek, az azonosítót és az üzenet (aliassal való msg).|
|SELECT VALUE címke<br>DOKUMENTÁCIÓ<br>CSATLAKOZZON a docs címke. A címkék<br>Az ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a dokumentumot. A címkék & & Array.isArray (doc. A címkék);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc._ts; vissza<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Vlastnost typu Pole, címkék, rendelkeznie dokumentumoknál szűri és az eredményül kapott dokumentumokat a _ts időbélyeg rendszertulajdonság, majd a projektek + simítja egybe a címkék tömb.|

## <a name="next-steps"></a>További lépések

További fogalmakat és útmutató írási és tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata az Azure Cosmos DB:

- [Tárolt eljárások és eseményindítók használatával a Javascript API-lekérdezés írása](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB-vel működő tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)
- [Az Azure Cosmos DB-ben tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata](how-to-use-stored-procedures-triggers-udfs.md)
- [Az Azure Cosmos DB a JavaScript kiszolgálóoldali API-referencia](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
