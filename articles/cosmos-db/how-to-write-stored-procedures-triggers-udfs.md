---
title: Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása Azure Cosmos DB
description: Megtudhatja, hogyan határozhat meg tárolt eljárásokat, eseményindítókat és felhasználó által definiált függvényeket Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: cdac8321ec4ac7b2e13c5545a2483527118daae3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606263"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása Azure Cosmos DB

A Azure Cosmos DB lehetővé teszi a JavaScript nyelvre integrált, tranzakciós végrehajtását, amely lehetővé teszi **tárolt eljárások**, **Eseményindítók**és **felhasználó által definiált függvények (UDF-EK)** írását. Ha az SQL API-t Azure Cosmos DBban használja, megadhatja a tárolt eljárásokat, eseményindítókat és UDF JavaScript nyelven. Megírhatja a logikát a JavaScriptben, és végrehajthatja azt az adatbázismotor használatával. Eseményindítókat, tárolt eljárásokat és UDF hozhat létre és futtathat [Azure Portal](https://portal.azure.com/)használatával, a [JavaScript nyelv integrált lekérdezési API-ját Azure Cosmos DB](javascript-query-api.md) és az [Cosmos db SQL API ügyféloldali SDK](sql-api-dotnet-samples.md)-kat. 

A tárolt eljárások, triggerek és felhasználó által definiált függvények meghívásához regisztrálnia kell. További információ: a [tárolt eljárások, eseményindítók, felhasználó által definiált függvények használata a Azure Cosmos DBban](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Particionált tárolók esetén a tárolt eljárás végrehajtásakor meg kell adni egy partíciós kulcs értékét a kérés beállításai között. A tárolt eljárásokat a rendszer mindig a partíciós kulcsra szűkíti. A másik partíciós kulcs értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárásban. Ez is a triggerekre is vonatkozik.

> [!Tip]
> A Cosmos támogatja a tárolók üzembe helyezését tárolt eljárásokkal, eseményindítókkal és felhasználó által definiált függvényekkel. További információ: [Azure Cosmos db tároló létrehozása kiszolgálóoldali funkciókkal.](manage-sql-with-resource-manager.md#create-sproc)

## <a id="stored-procedures"></a>Tárolt eljárások írása

A tárolt eljárások JavaScript használatával íródnak, és az Azure Cosmos-tárolóban lévő elemeket hozhatnak létre, frissíthetnek, olvashatnak, lekérhetnek és törölhetnek. A tárolt eljárások gyűjtemény szerint vannak regisztrálva, és a gyűjteményben található bármilyen dokumentumon vagy mellékleten működhetnek.

**Példa**

Az alábbi egyszerű tárolt eljárás egy ""Helló világ!"alkalmazás" választ ad vissza.

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

A környezeti objektum hozzáférést biztosít a Azure Cosmos DBban végrehajtható összes művelethez, valamint a kérés és a válasz objektumokhoz való hozzáféréshez is. Ebben az esetben a válasz objektum segítségével állíthatja be a válasz törzsét, amelyet vissza szeretne adni az ügyfélnek.

Az írást követően a tárolt eljárást regisztrálni kell egy gyűjteményben. További információ: [tárolt eljárások használata Azure Cosmos db](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) cikkben.

### <a id="create-an-item"></a>Elemek létrehozása tárolt eljárás használatával

Amikor tárolt eljárással hoz létre egy tételt, az elem bekerül az Azure Cosmos-tárolóba, és az újonnan létrehozott elem AZONOSÍTÓját adja vissza. Egy elem létrehozása aszinkron művelet, amely a JavaScript visszahívási függvénytől függ. A visszahívási függvény két paraméterrel rendelkezik – egyet a hiba objektumhoz abban az esetben, ha a művelet meghiúsul, és egy másik a visszatérési értékhez. Ebben az esetben a létrehozott objektum. A visszahíváson belül kezelheti a kivételt, vagy hibát okozhat. Ha nincs megadva visszahívás, és hiba történt, akkor a Azure Cosmos DB futtatókörnyezet hibát jelez. 

A tárolt eljárás egy paramétert is tartalmaz a Leírás megadásához, ez egy logikai érték. Ha a paraméter értéke TRUE (igaz), és a Leírás hiányzik, akkor a tárolt eljárás kivételt jelez. Ellenkező esetben a tárolt eljárás többi része továbbra is fut.

A következő példa tárolt eljárás egy új Azure Cosmos-elem bemenetként való behelyezését hajtja végre, beszúrja az Azure Cosmos tárolóba, és visszaadja az újonnan létrehozott elem AZONOSÍTÓját. Ebben a példában a ToDoList mintát használjuk a gyors üzembe helyezési [.net SQL API](create-sql-api-dotnet.md) -ból

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

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Tömbök bemeneti paraméterként a tárolt eljárásokhoz 

Azure Portal tárolt eljárás definiálásakor a bemeneti paraméterek mindig karakterláncként lesznek elküldve a tárolt eljáráshoz. Még ha sztringek tömbjét is át kell adni bemenetként, a rendszer karakterlánccá alakítja át a tömböt, és elküldi a tárolt eljárásnak. Ennek megkerüléséhez definiálhat egy függvényt a tárolt eljáráson belül a sztring tömbként való elemzéséhez. A következő kód bemutatja, hogyan elemezheti a karakterlánc bemeneti paramétereit tömbként:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>Tárolt eljárásokon belüli tranzakciók

A tároló elemein belüli tranzakciókat tárolt eljárással is megvalósíthatja. Az alábbi példa egy fantasy Football Gaming-alkalmazáson belüli tranzakciókat használ, hogy egyetlen művelet keretében két csapat között kereskedni lehessen. A tárolt eljárás megkísérli beolvasni a két Azure Cosmos-elemet, amelyek megfelelnek az argumentumként átadott Player-azonosítóknak. Ha mindkét játékos megtalálható, a tárolt eljárás a csapatuk cseréjével frissíti az elemeket. Ha bármilyen hiba fordul elő, a tárolt eljárás olyan JavaScript-kivételt mutat be, amely implicit módon megszakítja a tranzakciót.

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

### <a id="bounded-execution"></a>Kötött végrehajtás a tárolt eljárásokon belül

Az alábbi példa egy olyan tárolt eljárást mutat be, amely tömegesen importál elemeket egy Azure Cosmos-tárolóba. A tárolt eljárás a határolt végrehajtást a `createDocument`logikai visszatérési értékének ellenőrzésével kezeli, majd a tárolt eljárás minden egyes meghívása során beszúrt elemek számát használja a kötegek előrehaladásának nyomon követéséhez és folytatásához.

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

Azure Cosmos DB támogatja az előtriggereket és az eseményindítókat. Az adatbázis-elemek módosítása és az eseményindítók végrehajtása előtt az eseményindítók végrehajtása az adatbázis-elemek módosítása után történik.

### <a id="pre-triggers"></a>Trigger előtti

Az alábbi példa azt mutatja be, hogyan használható a pre-trigger a létrehozott Azure Cosmos-elemek tulajdonságainak ellenőrzésére. Ebben a példában kihasználjuk a ToDoList mintát a gyors üzembe helyezési [.net SQL API](create-sql-api-dotnet.md)-ból, hogy egy timestamp tulajdonságot adjon hozzá egy újonnan hozzáadott elemhez, ha az nem tartalmaz egyet.

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

Az előzetes eseményindítók nem rendelkezhetnek bemeneti paraméterekkel. A triggerben található kérelem objektum a művelethez társított kérelem üzenetének kezelésére szolgál. Az előző példában a pre-trigger fut egy Azure Cosmos-elem létrehozásakor, és a kérelem üzenet törzse tartalmazza a JSON formátumban létrehozandó tételt.

Ha a triggerek regisztrálva vannak, megadhatja azokat a műveleteket, amelyeket futtathat a használatával. Ezt a triggert `TriggerOperation.Create``TriggerOperation` értékkel kell létrehozni, ami azt jelenti, hogy az triggert egy csere műveletben használja, ahogy az a következő kódban nem engedélyezett.

A pre-triggerek regisztrálásával és meghívásával kapcsolatos Példákért lásd: [Pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [trigger utáni](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkek. 

### <a id="post-triggers"></a>Triggerek utáni

Az alábbi példa egy trigger utáni műveletet mutat be. Ez a trigger lekérdezi a metaadat-elemeket, és frissíti azt az újonnan létrehozott elemmel kapcsolatos részletekkel.


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

Fontos megjegyezni, hogy az eseményindítók tranzakciós végrehajtása Azure Cosmos DBban. Az utólagos indítás a mögöttes elemhez tartozó tranzakció részeként fut. Az indítás utáni végrehajtás során felmerülő kivétel esetén a teljes tranzakció sikertelen lesz. Minden véglegesítés visszavonásra kerül, és a rendszer kivételt ad vissza.

A pre-triggerek regisztrálásával és meghívásával kapcsolatos Példákért lásd: [Pre-triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [trigger utáni](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkek. 

## <a id="udfs"></a>Felhasználó által definiált függvények írása

Az alábbi minta létrehoz egy UDF-t a jövedelemadó kiszámításához a különböző bevételi zárójelek esetében. Ezt a felhasználó által definiált függvényt ezután egy lekérdezésen belül fogjuk használni. Ebben a példában feltételezhető, hogy egy "jövedelem" nevű tároló található a következő tulajdonságokkal:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

A következő egy függvény definíciója a jövedelemadó kiszámításához a különböző bevételi zárójelek esetében:

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

A felhasználó által definiált függvények regisztrálásával és használatával kapcsolatos Példákért lásd: [felhasználói függvények használata Azure Cosmos db](how-to-use-stored-procedures-triggers-udfs.md#udfs) cikkben.

## <a name="next-steps"></a>További lépések

További fogalmak és útmutató: tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása vagy használata Azure Cosmos DBban:

* [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények regisztrálása és használata Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Tárolt eljárások és eseményindítók írása a JavaScript lekérdezési API használatával Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Azure Cosmos DB tárolt eljárások, eseményindítók és felhasználó által definiált függvények használata Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [A JavaScript nyelvi integrált lekérdezési API használata a Azure Cosmos DBban](javascript-query-api.md)
