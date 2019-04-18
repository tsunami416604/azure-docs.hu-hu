---
title: Az Azure Cosmos DB-ben tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása
description: Ismerje meg az Azure Cosmos DB-ben tárolt eljárások, eseményindítók és felhasználó által definiált függvények definiálása
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: c94509fb39d1c5ebb9aec1acfe1cbacc9cd6fd4a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59268411"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása

Az Azure Cosmos DB, amely lehetővé teszi, hogy írását a JavaScript nyelvintegrált, a tranzakciós végrehajtását biztosítja **tárolt eljárások**, **eseményindítók**, és **felhasználó által definiált függvények (UDF)**. Ha az SQL API használatával az Azure Cosmos DB, a tárolt eljárások, eseményindítók és felhasználói függvények meghatározhatja a JavaScript nyelven. A logika írását a JavaScript, és hajtsa végre, az adatbázis motorjában. Hozhat létre, és hajtsa végre az UDF-EK, eseményindítók és tárolt eljárások használatával [az Azure portal](https://portal.azure.com/), a [JavaScript nyelvű lekérdezési API-t az Azure Cosmos DB integrált](javascript-query-api.md) és a [Cosmos DB SQL API-ügyfél SDK-k](sql-api-dotnet-samples.md). 

Egy tárolt eljárás, eseményindítók és felhasználó által definiált függvény meghívásához, azt regisztrálnia kell. További információkért lásd: [tárolt eljárások, eseményindítók, felhasználó által definiált függvények az Azure Cosmos DB használata](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> A particionált tárolók, a tárolt eljárás végrehajtása közben partíciókulcs-értékkel kell adni a kérés beállításokat. Tárolt eljárások mindig hatóköre egy partíciókulcsot. Egy másik partíciókulcs-értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárás. Ez is alkalmazandó, valamint eseményindítók.

## <a id="stored-procedures"></a>Tárolt eljárások írása

Tárolt eljárások a JavaScript használatával írt, azokat is létrehozása, frissítése, olvassa el, lekérdezése és törölhet elemeket egy Azure Cosmos-tárolóban. Tárolt eljárások gyűjteményenként regisztrálva van, és működhet a dokumentumtípus vagy a melléklet szerepel a gyűjteményben.

**Példa**

Íme egy egyszerű tárolt eljárást, amely egy "Hello World" választ ad vissza.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

A context objektumot biztosít hozzáférést az Azure Cosmos DB-ben végrehajtott összes műveletet, valamint a kérések és válaszok objektumok elérését. Ebben az esetben használhatja a Válaszobjektum, állítsa vissza az ügyfél nem küldött válasz törzsében.

Miután írt, a tárolt eljárás regisztrálni kell egy gyűjteményt. További tudnivalókért lásd: [tárolt eljárások használata az Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) cikk.

### <a id="create-an-item"></a>Tárolt eljárással elem létrehozása

Tárolt eljárás használatával létrehoz egy elemet, ha az elem beilleszti az Azure Cosmos DB-tárolók és a egy azonosítót az újonnan létrehozott elemet adott vissza. Elem létrehozása egy aszinkron művelet, és a JavaScript-visszahívási függvényekben függ. A visszahívási függvény két paramétert - egyet a hibaobjektum abban az esetben a művelet sikertelen lesz, és egy másik, a visszaadott érték; rendelkezik Ebben az esetben a létrehozott objektum. A visszahívás belül kezelni a kivétel vagy is hibát. Abban az esetben egy visszahívást nem áll rendelkezésre, és hiba történik, az Azure Cosmos DB modul kivételt fogja kijelezni hiba. 

A tárolt eljárás is tartalmaz egy paraméter segítségével állítsa be a a leírást, hogy egy logikai érték. Ha a paraméter értéke igaz, és a leírás nincs megadva, a tárolt eljárás kivételt. Ellenkező esetben a tárolt eljárás további részét továbbra is fusson.

Az alábbi példa a tárolt eljárás tart egy új Azure Cosmos DB-elemet bemenetként, illeszt be az Azure Cosmos DB-tárolóhoz, és adja vissza az újonnan létrehozott elem azonosítója. Ebben a példában a ToDoList minta azt kihasználva a [rövid .NET SQL API-hoz](create-sql-api-dotnet.md)

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Tárolt eljárás bemeneti paraméterek tömbök 

Az Azure Portalon tárolt eljárás meghatározásakor bemeneti paraméterek mindig érkeznek karakterláncként a tárolt eljárást. Akkor is, ha a felhasználó egy karakterláncokból álló tömbre bemenetként, a tömb karakterlánccá és a tárolt eljárás küldött. Ennek megoldásához definiálhat a függvény tömbként a karakterláncot elemezni a tárolt eljárásból. A következő kód bemutatja, hogyan elemzése egy karakterlánc bemeneti paraméter egy tömb:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>A tárolt eljárásokból tranzakciók

Tranzakciók a tárolóban lévő elemek megvalósítható a tárolt eljárás használatával. Az alábbi példában a tranzakciók belül a játék futballalkalmazás kereskedelmi lejátszók egyetlen művelettel két csapatok között. A tárolt eljárás megkísérli beolvasni a két Azure Cosmos DB-elemet argumentumként átadott minden megfelelő player azonosítókra. Ha mindkét lejátszók található, majd a tárolt eljárás frissíti az elemeket a teams felcserélésével. Ha bármilyen hiba történik a vizualizáción, akkor a tárolt eljárás, amely implicit módon megszakítja a tranzakciót JavaScript kivételt okoz.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>A tárolt eljárásokból körülhatárolt végrehajtása

Az alábbiakban látható egy példa egy tárolt eljárást, amely tömeges-imports elemek egy Azure Cosmos-tárolóba. A tárolt eljárás körülhatárolt végrehajtási kezeli a logikai visszatérési érték a ellenőrzésével `createDocument`, és majd a tárolt eljárás egyes elindításaihoz beszúrt elemek száma segítségével nyomon követheti és kötegekre folyamat folytatásához.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>Eseményindítók írása

Az Azure Cosmos DB támogatja az eseményindítók előtti és utáni eseményindítók. Egy adatbázis-elem módosítása előtt végrehajtása előtti eseményindítók, és utáni eseményindítók egy adatbázis-elem módosítása után lesznek végrehajtva.

### <a id="pre-triggers"></a>Pre-triggers

Az alábbi példa bemutatja, hogyan előtti eseményindító létrehozása folyamatban van egy Azure Cosmos DB elem tulajdonságainak ellenőrzésére szolgál. Ebben a példában a ToDoList minta azt kihasználva a [rövid .NET SQL API](create-sql-api-dotnet.md), egy időbélyeg-tulajdonság hozzáadása egy újonnan hozzáadott elem, ha egy nem tartalmaz.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Üzem előtti eseményindítók nem lehet bemeneti paramétereket. Az eseményindító támogatásikérelem-objektum a kérelemüzenet a művelethez társított módosítására szolgál. Az előző példában az üzem előtti trigger futtatása az Azure Cosmos DB-elem létrehozásakor, és a kérés törzse tartalmazza az elem hozhatók létre JSON formátumban.

Eseményindítók regisztrálásakor való futtatás műveleteket is megadhat. Ez az eseményindító kell létrehozni egy `TriggerOperation` értékét `TriggerOperation.Create`, ami azt jelenti, hogy az eseményindító használatával az alábbi kódban látható módon a cserét nem engedélyezett.

Hogyan kell regisztrálni, és a egy üzem előtti trigger meghívása példákért lásd [előtti eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [utáni eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkek. 

### <a id="post-triggers"></a>Post-triggers

Az alábbi példa bemutatja egy utáni eseményindítót. Ez az eseményindító lekérdezése metaadat-elemhez, és frissíti, az újonnan létrehozott elemek részleteit.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Egy dolog, amire a vegye figyelembe az Azure Cosmos DB eseményindítók tranzakciós végrehajtása. Az alkalmazásnak eseményindító magát az alapul szolgáló elemet az ugyanazon tranzakció részeként futtatja. Az alkalmazásnak az eseményindító végrehajtása során kivétel sikertelen lesz a teljes tranzakciót. Bármi is vissza lesz állítva, és kivételt adja vissza.

Hogyan kell regisztrálni, és a egy üzem előtti trigger meghívása példákért lásd [előtti eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [utáni eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkek. 

## <a id="udfs"></a>Felhasználó által definiált függvények írása

Az alábbi minta létrehoz egy UDF-ben a különféle bevételi zárójelben adó kiszámításához. Ez a felhasználó által definiált függvény használni kívánt majd egy lekérdezésbe. Ebben a példában az alkalmazásában tegyük fel, van egy "Jövedelmek" a következő tulajdonságokkal rendelkező nevű tárolóban:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Az alábbiakban látható a különböző jövedelem zárójelben adó kiszámításához definici funkce:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Regisztrálásához és használatához a felhasználó által definiált függvény bemutató példákért lásd: [felhasználó által definiált függvények használata az Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) cikk.

## <a name="next-steps"></a>További lépések

További fogalmakat és útmutató írási vagy tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata az Azure Cosmos DB:

* [Regisztrálása és használata tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md)

* [Hogyan írhat a tárolt eljárások és eseményindítók az Azure Cosmos DB a Javascript Query API használatával](how-to-write-javascript-query-api.md)

* [Azure Cosmos DB-vel működő tárolt eljárások, eseményindítók és felhasználó által definiált függvények az Azure Cosmos DB-ben](stored-procedures-triggers-udfs.md)

* [Működő JavaScript nyelvvel integrált lekérdezési API-t az Azure Cosmos DB-ben](javascript-query-api.md)
