---
title: Tárolt eljárások, eseményindítók és UDF-ek írása az Azure Cosmos DB-ben
description: Ismerje meg, hogyan definiálhatja a tárolt eljárásokat, eseményindítókat és a felhasználó által definiált függvényeket az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4dee017323bda5fc08598a9b24cadd11516807cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441736"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Tárolt eljárások, eseményindítók és felhasználó által definiált függvények írása az Azure Cosmos DB-ben

Az Azure Cosmos DB a JavaScript nyelvbe integrált, tranzakciós végrehajtását biztosítja, amely lehetővé teszi **a tárolt eljárások,** **eseményindítók**és **a felhasználó által definiált függvények (UDF) írását.** Az SQL API használata az Azure Cosmos DB, megadhatja a tárolt eljárások, eseményindítók és UDF-ek JavaScript nyelven. Megírhatja a logikát javascriptben, és végrehajthatja az adatbázis-motorban. Az [Azure Portal](https://portal.azure.com/), az [Azure Cosmos DB JavaScript-nyelvű integrált lekérdezési API-ja](javascript-query-api.md) és a [Cosmos DB SQL API-k SDK-k](sql-api-dotnet-samples.md)használatával hozhat létre és hajthat végre eseményindítókat, tárolt eljárásokat és UDF-eket. 

A tárolt eljárás, eseményindító és felhasználó által definiált függvény hívásához regisztrálnia kell azt. További információ: [A tárolt eljárások, eseményindítók, felhasználó által definiált függvények az Azure Cosmos DB.For more information, see How to work with stored procedures, triggers, user-defined functions in Azure Cosmos DB.](how-to-use-stored-procedures-triggers-udfs.md)

> [!NOTE]
> Particionált tárolók esetén a tárolt eljárás végrehajtásakor meg kell adni egy partíciókulcs-értéket a kérelembeállításokban. A tárolt eljárások hatóköre mindig partíciókulcsra terjed ki. A más partíciókulcs-értékkel rendelkező elemek nem lesznek láthatók a tárolt eljárás számára. Ez az eseményindítókra is vonatkozott.

> [!Tip]
> A Cosmos támogatja a tárolók üzembe helyezését tárolt eljárásokkal, eseményindítókkal és felhasználó által definiált függvényekkel. További információ: [Hozzon létre egy Azure Cosmos DB-tároló kiszolgálóoldali funkciókkal.](manage-sql-with-resource-manager.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Hogyan írjunk tárolt eljárásokat

A tárolt eljárások JavaScript használatával vannak írásban, létrehozhatnak, frissíthetnek, olvashatnak, lekérdezhetnek és törölhetnek elemeket az Azure Cosmos-tárolóban. A tárolt eljárások gyűjteményenként vannak regisztrálva, és a gyűjteményben található bármely dokumentumon vagy mellékleten működhetnek.

**Példa**

Itt van egy egyszerű tárolt eljárás, amely visszaadja a "Hello World" választ.

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

A környezetobjektum hozzáférést biztosít az Azure Cosmos DB-ben elvégezhető összes művelethez, valamint hozzáférést biztosít a kérés- és válaszobjektumokhoz. Ebben az esetben a válaszobjektum segítségével állítsa be az ügyfélnek visszaküldendő válasz törzsét.

A megírást követően a tárolt eljárást nyilvántartásba kell venni egy gyűjteményben. További információ: [A tárolt eljárások használata az Azure Cosmos DB cikkben.](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Elem létrehozása tárolt eljárással

Amikor egy elemet tárolt eljárással hoz létre, az elem bekerül az Azure Cosmos-tárolóba, és az újonnan létrehozott elem azonosítóját adja vissza. Az elem létrehozása aszinkron művelet, amely a JavaScript visszahívási függvényektől függ. A visszahívási függvénynek két paramétere van : az egyik a hibaobjektumhoz, ha a művelet sikertelen, a másik pedig egy visszatérési értékhez; ebben az esetben a létrehozott objektum. A visszahíváson belül vagy kezelni tudja a kivételt, vagy hibát okozhat. Abban az esetben, ha a visszahívás nem biztosított, és hiba van, az Azure Cosmos DB futásidejű fog dobni egy hiba. 

A tárolt eljárás tartalmaz egy paramétert is a leírás beállításához, ez egy logikai érték. Ha a paraméter értéke igaz, és a leírás hiányzik, a tárolt eljárás kivételt okoz. Ellenkező esetben a tárolt eljárás többi része továbbra is fut.

A következő példa tárolt eljárás egy új Azure Cosmos-elemet vesz be, beszúrja az Azure Cosmos-tárolóba, és visszaadja az újonnan létrehozott elem azonosítóját. Ebben a példában a [Quickstart .NET SQL API-ból](create-sql-api-dotnet.md) származó ToDoList mintát használjuk fel.

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

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Tömbök a tárolt eljárások bemeneti paramétereiként 

Ha egy tárolt eljárást definiál az Azure Portalon, a bemeneti paraméterek mindig a tárolt eljárás karakterláncként lesz elküldve. Még akkor is, ha egy tömb karakterláncok bemenetként, a tömb karakterlánc, és elküldi a tárolt eljárás. A probléma megoldásához definiálhat egy függvényt a tárolt eljáráson belül, hogy a karakterláncot tömbként elemezhesse. A következő kód bemutatja, hogyan elemezze a karakterlánc bemeneti paraméterét tömbként:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>A tárolt eljárásokon belüli ügyletek

A tárolón belüli cikkektranzakcióit tárolt eljárással valósíthatja meg. A következő példa egy fantasy futballjáték-alkalmazáson belüli tranzakciókat használ, hogy egyetlen műveletben két csapat között kereskedjen játékosokkal. A tárolt eljárás megpróbálja olvasni a két Azure Cosmos-elemek mindegyike megfelelő a játékos azonosítók átadott argumentumként. Ha mindkét játékos megtalálható, akkor a tárolt eljárás frissíti a tételeket a csapatok cseréjével. Ha bármilyen hibát észlel az út mentén, a tárolt eljárás egy JavaScript-kivételt hoz, amely implicit módon megszakítja a tranzakciót.

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

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Tárolt eljárásokon belüli kötött végrehajtás

Az alábbiakban egy olyan tárolt eljárás, amely tömegesen importálja az elemeket egy Azure Cosmos-tárolóba. A tárolt eljárás úgy kezeli a határolt végrehajtást, hogy ellenőrzi a logikai visszatérési értéket a programból, `createDocument`majd a tárolt eljárás minden egyes meghívásában beszúrt elemek számát használja a kötegek közötti előrehaladás nyomon követéséhez és folytatásához.

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

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Hogyan írjunk eseményindítókat

Az Azure Cosmos DB támogatja az elő- és utóindítókat. Az előindítók végrehajtása az adatbáziselem módosítása előtt történik, az utóindítók pedig az adatbáziselem módosítása után.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Előzetes eseményindítók

A következő példa bemutatja, hogyan egy elő-eseményindító használatával egy Azure Cosmos-elem, amely létrehozása alatt álló tulajdonságok érvényesítéséhez. Ebben a példában a [Quickstart .NET SQL API](create-sql-api-dotnet.md)ToDoList mintáját használjuk fel, hogy időbélyeg-tulajdonságot adjunk egy újonnan hozzáadott elemhez, ha az nem tartalmaz.

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

Az előzetes eseményindítóknak nem lehetnek bemeneti paramétereik. Az eseményindítóban lévő kérelemobjektum a művelethez társított kérésüzenet kezelésére szolgál. Az előző példában az előesemény egy Azure Cosmos-elem létrehozásakor fut, és a kérelem üzenet törzse tartalmazza a JSON formátumban létrehozandó elemet.

Amikor az eseményindítók regisztrálva vannak, megadhatja azokat a műveleteket, amelyekkel futtatható. Ezt az eseményindítót `TriggerOperation` a `TriggerOperation.Create`értékével kell létrehozni, ami azt jelenti, hogy az eseményindító t a csereműveletben a következő kódban látható módon nem lehet használni.

Példákat, hogyan lehet regisztrálni, és hívja meg a pre-trigger, [lásd: elő-eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [utáni eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkeket. 

### <a name="post-triggers"></a><a id="post-triggers"></a>Utólagos eseményindítók

A következő példa egy eseményindító utáni bemutatót mutat be. Ez az eseményindító lekérdezi a metaadatelemet, és frissíti az újonnan létrehozott elem részleteivel.


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

Egy dolog, ami fontos megjegyezni, hogy az Azure Cosmos DB eseményindítók tranzakciós végrehajtása. Az eseményindító utáni eseményindító ugyanazon tranzakció részeként fut az alapul szolgáló cikkhez. Az eseményindító utáni végrehajtás során egy kivétel a teljes tranzakció sikertelen lesz. Minden elkövetett lesz visszaállítva, és egy kivétel tért vissza.

Példákat, hogyan lehet regisztrálni, és hívja meg a pre-trigger, [lásd: elő-eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) és [utáni eseményindítók](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) cikkeket. 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Felhasználó által definiált függvények írása

A következő minta létrehoz egy UDF-et a különböző jövedelemadó-kategóriák kiszámításához. Ezt a felhasználó által definiált függvényt ezután egy lekérdezésen belül kell használni. Ennek a példának az alkalmazásában tegyük fel, hogy van egy "Jövedelmek" nevű tároló, amelynek tulajdonságai a következők:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

A következő függvény definíciója a különböző jövedelemadó-kategóriák kiszámításához:

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

Példák at, hogyan regisztrálhat és használhat egy felhasználó által definiált függvényt, lásd: [A felhasználó által definiált függvények használata az Azure Cosmos DB cikkben.](how-to-use-stored-procedures-triggers-udfs.md#udfs)

## <a name="logging"></a>Naplózás 

Ha tárolt eljárást, eseményindítókat vagy felhasználó által definiált függvényeket használ, a lépéseket a `console.log()` parancs segítségével naplózhatja. Ez a parancs egy karakterláncot `EnableScriptLogging` koncentrál a hibakereséshez, ha az alábbi példában látható módon igaz értékre van állítva:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>További lépések

További fogalmak és a tárolt eljárások, eseményindítók és a felhasználó által definiált függvények írásának és használatának módjáról az Azure Cosmos DB-ben:

* [Tárolt eljárások, eseményindítók és felhasználói függvények regisztrálása és használata az Azure Cosmos DB-ben](how-to-use-stored-procedures-triggers-udfs.md)

* [Tárolt eljárások és eseményindítók írása javascript-lekérdezési API használatával az Azure Cosmos DB-ben](how-to-write-javascript-query-api.md)

* [Az Azure Cosmos DB tárolt eljárásainak, eseményindítóinak és felhasználó által definiált függvényeinek együttműködése az Azure Cosmos DB-ben](stored-procedures-triggers-udfs.md)

* [JavaScript-nyelvű integrált lekérdezési API-val való együttműködés az Azure Cosmos DB-ben](javascript-query-api.md)
