---
title: Az Azure Cosmos DB kiszolgálóoldali JavaScript programozása
description: Ismerje meg, hogyan lehet Azure Cosmos DB segítségével tárolt eljárások, adatbázis-eseményindítók és felhasználó által definiált függvények (UDF-EK) írását a JavaScript. Adatbázis olyan programozási tippek és egyéb beolvasása.
keywords: Adatbázis-eseményindítók, tárolt eljárás, a tárolt eljárás, adatbázis program, sproc, azure, Microsoft azure
services: cosmos-db
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: 9f36d6761e61f0de610cb3f612cd069092cdc242
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083699"
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Az Azure Cosmos DB kiszolgálóoldali programozása: tárolt eljárások, adatbázis-eseményindítók és felhasználói függvények

Ismerje meg, hogyan JavaScript nyelvintegrált, a tranzakciós végrehajtása az Azure Cosmos DB lehetővé teszi, hogy a fejlesztők írási **tárolt eljárások**, **eseményindítók**, és **felhasználó által definiált függvények (UDF-EK)**  a natív módon egy [ECMAScript 2015](https://www.ecma-international.org/ecma-262/6.0/) JavaScript. A JavaScript integrációja lehetővé teszi, hogy a program logika, amely tartalmazza a szükséges, és közvetlenül az adatbázis tárolási partíciók belül írását. 

Ebben a cikkben megtudhatja a válaszokat az alábbi kérdésekre:  

* Hogyan írni egy tárolt eljárást, eseményindító vagy UDF JavaScript használatával?
* Hogyan nem garantálja a Cosmos DB ACID?
* Hogyan működik a Cosmos dB-ben a tranzakciók?
* Mik a előre aktivál, és utáni aktivál, és hogyan tegye írni egy?
* Hogyan regisztrálásához és végrehajtásához egy tárolt eljárást, eseményindító vagy UDF RESTful módon HTTP használatával?
* Milyen Cosmos DB SDK elérhető hozhat létre, és hajtsa végre tárolt eljárások, eseményindítók és felhasználói függvények?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Tárolt eljárás és UDF programozási bemutatása
Ez a megközelítés az *"JavaScript egy T-SQL modern napot"* felszabadítja a rendszer nem egyezik és objektumrelációs leképezés technológiák kiküszöböli az alkalmazásfejlesztők. Belső milyen előnyökkel jár az intelligens alkalmazásokat készíthet, amellyel számos is rendelkezik:  

* **Eljárási logikai:** JavaScript egy magas szintű programozási nyelv, üzleti logikát express gazdag és ismerős felületet biztosít. Az adatokhoz bárhol is tartózkodjanak műveletek összetett feladatütemezések hajthat végre.
* **Elemi tranzakciókat:** Cosmos DB garantálja, hogy az adatbázis-műveletet hajtott végre egyetlen tárolt eljárás vagy eseményindító belül elemiek. A atomi funkció lehetővé teszi, hogy egy alkalmazás egyesítése az egy kötegben a kapcsolódó műveleteket, hogy az összes sikeres vagy egyiket sem sikerült. 
* **Teljesítmény:** lehetővé teszi, hogy a JSON a Javascript nyelvű típus rendszer belsőleg van leképezve, és egyben a Cosmos DB storage alapvető egysége optimalizálások Lusta materialization a pufferkészletben JSON-dokumentumok, például számos és a végrehajtó kód elérhető igény szerinti így. Nincsenek további teljesítmény előnye szállítási üzleti logikát az adatbázishoz:
  
  * Kötegelés – a fejlesztők csoportban műveletek, mint a beszúrások, és küldheti el ezeket a tömegesen. A hálózati forgalom késéssel költségek és a tároló terhelését a létrehozott különálló tranzakciók jelentősen csökken. 
  * Üzem előtti összeállítása – Cosmos DB precompiles tárolt eljárások, eseményindítók és felhasználó által definiált függvények (UDF) egyes elindításaihoz JavaScript fordítási költségek elkerülése érdekében. A terhelés létrehozásának eljárási logika a byte kódot van amortizált minimális értéket.
  * Alkalmazás-előkészítés – sok műveletek szükségességét mellékhatása ("eseményindító"), amely potenciálisan magában foglalja egy vagy több másodlagos store műveleteket is. Azokat a atomitást, kivéve ennek az az további nagy teljesítményű, amikor a kiszolgáló kerül. 
* **Beágyazás:** a tárolt eljárások segítségével csoport üzleti logika egy helyen tudhatja, amely két előnnyel jár:
  * Hozzáadja a nyers adatokat, amely lehetővé teszi az adatok architects való összpontosításnak köszönhetően az alkalmazásaikhoz egymástól függetlenül az adatok felett egy olyan absztrakciós réteget. Absztrakciós réteg akkor előnyös, ha az adatok séma nélküli, miatt a törékennyé teszi feltételezéseket kell számlázásnak az alkalmazásba, ha közvetlenül az adatok kezelésére van szükség lehet.  
  * Ez az absztrakció lehetővé teszi a vállalatok számára adataik egyszerűsítheti a hozzáférést a parancsfájlok biztonsága.  

A létrehozási és egyéni lekérdezési operátorokkal, adatbázis-eseményindítók és tárolt eljárások végrehajtása keresztül támogatott a [az Azure portal](https://portal.azure.com), a [REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), és [ügyféloldali SDK-k](sql-api-sdk-dotnet.md) , beleértve a .NET, Node.js és JavaScript számos platformon.

Ebben az oktatóanyagban a [Node.js SDK-val Q Ígéreteinket](https://azure.github.io/azure-documentdb-node-q/) szintaxis és a tárolt eljárások, eseményindítók és felhasználói függvények használatát mutatja be.   

## <a name="stored-procedures"></a>Tárolt eljárások
### <a name="example-write-a-stored-procedure"></a>Példa: Egy tárolt eljárást írása
Kezdjük az egyszerű tárolt eljárások, amelyek egy "Hello World" választ ad vissza.

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

Tárolt eljárások gyűjteményenként regisztrálva van, és működhet a dokumentum és a melléklet szerepel a gyűjteményben. Az alábbi kódrészlet bemutatja, hogyan helloWorld tárolt eljárás regisztrálja egy gyűjteményben. 


```javascript
// register the stored procedure
var createdStoredProcedure;
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
    .then(function (response) {
        createdStoredProcedure = response.resource;
        console.log("Successfully created stored procedure");
    }, function (error) {
        console.log("Error", error);
    });
```


A tárolt eljárás regisztrálása után futtathatja a gyűjteményre, és olvassa el az ügyfélnél vissza az eredményeket. 

```javascript
// execute the stored procedure
client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
    .then(function (response) {
        console.log(response.result); // "Hello, World"
    }, function (err) {
        console.log("Error", error);
    });
```

A context objektumot biztosít hozzáférést a Cosmos DB storage elvégezhető összes műveletet, valamint a kérések és válaszok objektumok elérését. A válasz objektum ebben az esetben az ügyfélnek küldött válasz törzsében beállításához használható. További információkért lásd: a [Azure Cosmos DB a JavaScript API-referencia a kiszolgálóoldali](https://azure.github.io/azure-cosmosdb-js-server/).  

Ossza meg velünk az ebben a példában bontsa ki, és további adatbázissal kapcsolatos funkciók hozzáadása a tárolt eljárást. Tárolt eljárások is létrehozni, frissíteni, olvassa el, lekérdezése és dokumentumok és mellékletek belül a gyűjtemény törlése.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Példa: Hozzon létre egy dokumentumot egy tárolt eljárást írása
A következő kódrészletet bemutatja, hogyan használhatja a context objektumot Cosmos DB-erőforrásokat.

```javascript
var createDocumentStoredProc = {
    id: "createMyDocument",
    serverScript: function createMyDocument(documentToCreate) {
        var context = getContext();
        var collection = context.getCollection();

        var accepted = collection.createDocument(collection.getSelfLink(),
              documentToCreate,
              function (err, documentCreated) {
                  if (err) throw new Error('Error' + err.message);
                  context.getResponse().setBody(documentCreated.id)
              });
        if (!accepted) return;
    }
}
```


Ez a tárolt eljárás bemeneti documentToCreate, az aktuális gyűjtemény hozható létre a dokumentum törzsében, vesz igénybe. Minden ilyen művelet aszinkron és a JavaScript-függvény visszahívások függenek. A visszahívási függvény két paramétert, egy hiba objektum abban az esetben a művelet sikertelen lesz, és egy a létrehozott objektum rendelkezik. A visszahívás belüli felhasználók kezeléséhez a kivétel, vagy hibát. Abban az esetben egy visszahívást nem áll rendelkezésre, és hiba történik, az Azure Cosmos DB modul hibát jelez.   

A fenti példában a visszahívás hibát jelez, ha a művelet sikertelen volt. Ellenkező esetben azt állítja be a létrehozott dokumentum Azonosítóját az ügyfélnek a válasz törzse. Itt van ez a tárolt eljárás végrehajtásának módját a bemeneti paramétereknek.

```javascript
// register the stored procedure
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
    .then(function (response) {
        var createdStoredProcedure = response.resource;

        // run stored procedure to create a document
        var docToCreate = {
            id: "DocFromSproc",
            book: "The Hitchhiker’s Guide to the Galaxy",
            author: "Douglas Adams"
        };

        return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
              docToCreate);
    }, function (error) {
        console.log("Error", error);
    })
.then(function (response) {
    console.log(response); // "DocFromSproc"
}, function (error) {
    console.log("Error", error);
});
```

Ez a tárolt eljárás bemeneti dokumentum szervek tömbjét és hozza létre az összes a azonos tárolt eljárás végrehajtása egyenként hozza létre mindegyik kéréseket helyett módosíthatók. Ez a tárolt eljárás használható egy hatékony tömeges programu Pro import megvalósításához a Cosmos dB (az oktatóanyag későbbi részében részletezett).   

A bemutatott példában bemutatott tárolt eljárások használata. Ezután ismertetjük eseményindítók és felhasználó által definiált függvények (UDF-EK) az oktatóanyag későbbi részében.

### <a name="known-issues"></a>Ismert problémák

Egy tárolt eljárást az Azure portal használatával meghatározásakor bemeneti paraméterek mindig érkeznek karakterláncként a tárolt eljárást. Akkor is, ha a felhasználó egy karakterláncokból álló tömbre bemenetként, a tömb karakterlánccá és a tárolt eljárás küldött. Megkerülő megoldás a probléma definiálhat függvény belül tömbként a karakterláncot elemezni a tárolt eljárás. A következő kódot a következő elemezni a karakterlánc egy tömb, például: 

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);
    
    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

## <a name="database-program-transactions"></a>Adatbázis-program tranzakciók
Egy tipikus adatbázisban lévő tranzakció munka egyetlen logikai egységként végrehajtott műveletek sorozataként lehet definiálni. Minden egyes tranzakciót biztosít **ACID megvalósulásának**. ACID egy jól ismert rövidítése négy tulajdonságai – atomitást, konzisztencia, elkülönítési és tartóssági jelző.  

Röviden, atomitást garantálja, hogy az összes tranzakción belül végzett munka egyetlen egységként kezelik, vagy minden adatot elkötelezett vagy sem. Konzisztencia gondoskodik arról, hogy az adatok mindig állapotban van egy jó belső tranzakció között. Elkülönítés garantálja, hogy két tranzakciók egymással – általában elvégzésében, a legtöbb kereskedelmi rendszerekben több elkülönítési szintet, amely használható alkalmazás igényeinek megfelelően. Tartósságot biztosítja, hogy mindig lesz jelen elkötelezte magát az adatbázis módosítása.   

A Cosmos dB-ben üzemeltetett JavaScript a azonos memória-adatbázisként. Ezért kérések száma belül tárolt eljárások és eseményindítók ugyanabban a hatókörben, egy adatbázis-munkamenet végrehajtásához. Ez a funkció lehetővé teszi, hogy a Cosmos DB garantálja az ACID egyetlen tárolt eljárás vagy eseményindító részét képező összes művelethez. Vegye figyelembe a következő tárolt eljárás-definíciót:

```javascript
// JavaScript source code
var exchangeItemsSproc = {
    id: "exchangeItems",
    serverScript: function (playerId1, playerId2) {
        var context = getContext();
        var collection = context.getCollection();
        var response = context.getResponse();

        var player1Document, player2Document;

        // query for players
        var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
            function (err, documents, responseOptions) {
                if (err) throw new Error("Error" + err.message);

                if (documents.length != 1) throw "Unable to find both names";
                player1Document = documents[0];

                var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                    function (err2, documents2, responseOptions2) {
                        if (err2) throw new Error("Error" + err2.message);
                        if (documents2.length != 1) throw "Unable to find both names";
                        player2Document = documents2[0];
                        swapItems(player1Document, player2Document);
                        return;
                    });
                if (!accept2) throw "Unable to read player details, abort ";
            });

        if (!accept) throw "Unable to read player details, abort ";

        // swap the two players’ items
        function swapItems(player1, player2) {
            var player1ItemSave = player1.item;
            player1.item = player2.item;
            player2.item = player1ItemSave;

            var accept = collection.replaceDocument(player1._self, player1,
                function (err, docReplaced) {
                    if (err) throw "Unable to update player 1, abort ";

                    var accept2 = collection.replaceDocument(player2._self, player2,
                        function (err2, docReplaced2) {
                            if (err) throw "Unable to update player 2, abort"
                        });

                    if (!accept2) throw "Unable to update player 2, abort";
                });

            if (!accept) throw "Unable to update player 1, abort";
        }
    }
}

// register the stored procedure in Node.js client
client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
    .then(function (response) {
        var createdStoredProcedure = response.resource;
    }
);
```

Ez a tárolt eljárás belül egy kereskedelmi elemek között két játékos egyetlen művelettel játékalkalmazás tranzakciókat alkalmaz. A tárolt eljárás próbál meg olvasni argumentumként átadott minden megfelelő player azonosítókra két dokumentumot. Ha mindkét player dokumentumok találhatók, majd a tárolt eljárás frissíti a dokumentumok az elemek felcserélésével. Ha bármilyen hiba történik a vizualizáción, azt, amely implicit módon megszakítja a tranzakciót a JavaScript kivételt okoz.

Ha a gyűjtemény a tárolt eljárás regisztrálása elleni egypartíciós gyűjtemény, akkor a tranzakció hatókörét a gyűjteményben lévő összes dokumentumot. Ha a gyűjtemény particionált, majd a tárolt eljárások a tranzakció-hatókörben, amelyik egyetlen partíciókulcsot lesznek végrehajtva. Minden egyes tárolt eljárás végrehajtása majd tartalmaznia kell partíciókulcs-értékkel megfelelő a hatókört a tranzakció kell futnia. További információkért lásd: [Azure Cosmos DB particionálási](partition-data.md).

### <a name="commit-and-rollback"></a>Érvényesítés és visszaállítás
Tranzakciók mélyen és natív módon integrálva vannak Cosmos DB a JavaScript-programozási modellt. JavaScript-függvény, belül minden művelet automatikusan burkolja egyetlen tranzakció alatt. Ha a JavaScript összes kivétel nélkül befejeződött, a műveletek az adatbázishoz elkötelezettek. Érvényben a "BEGIN TRANSACTION" és "COMMIT TRANSACTION" utasításokat a relációs adatbázisok járnak Cosmos DB-ben.  

Ha bármely kivétellel, hogy a parancsfájlból propagálja, Cosmos DB a JavaScript futásidejű állítja vissza a teljes tranzakciót. A korábbi példa szerint hívása kivétel hatékonyan egyenértékű egy "ROLLBACK TRANSACTION" Cosmos DB-ben.

### <a name="data-consistency"></a>Adatkonzisztencia
Tárolt eljárások és eseményindítók mindig futtatja az Azure Cosmos DB-tároló az elsődleges replikán. Ez biztosítja, hogy az olvasások belül tárolt eljárások ajánlat erős konzisztencia. Lekérdezések, felhasználó által definiált függvények használatával hajtható végre az elsődleges vagy bármely másodlagos replikája, de biztosítania felel meg a kért konzisztenciaszint válassza ki a megfelelő replika.

## <a name="bounded-execution"></a>Korlátozott végrehajtása
Cosmos DB minden művelet a megadott kiszolgálón belül végezze kérelmek időtúllépési időtartama. Ezt a korlátozást a JavaScript-függvények (tárolt eljárások, eseményindítók és felhasználó által definiált függvények) is vonatkozik. Ha egy művelet nem fejeződik az adott határidő, a tranzakció vissza lesz állítva. JavaScript-függvények kell befejezni az időkorláton belül, vagy egy folytatási-alapú modell, batch és folytathat végrehajtási megvalósítására.  

Leegyszerűsítheti a fejlesztést, a tárolt eljárások és eseményindítók határidőket, a gyűjtemény objektumra vonatkozóan (létrehozása, olvasása, cserélje le és törölje a dokumentumok és mellékletek) alatt a függvények kezelése érdekében a visszaadandó egy logikai értéket képviselő e művelet fog befejeződni. Ha az értéke FALSE (hamis), azt jelzi, hogy az időkorlát lejár, és, hogy az eljárás végrehajtása kell belefoglalni.  Az első elfogadhatatlan store művelet műveletek aszinkron előtt garantáltan elvégezni, ha a tárolt eljárás ideje alatt befejeződik, és nem várólistára további kérések eredményéről.  

JavaScript-függvények vannak is körülhatárolt az erőforrás-használat. A cosmos DB gyűjteményenként vagy tárolók készletének teljesítmény fenntart. Átviteli sebesség a CPU, memória és i/o-felhasználás néven kérelemegység vagy fenntartott egységek normalizált egységben van kifejezve. JavaScript-függvények is használhat fel rövid időn belül nagy számú kérelemegység, és előfordulhat, hogy első sebessége korlátozott, ha a gyűjtemény korlátot. Erőforrás-igényes tárolt eljárásokat is előfordulhat, hogy karanténba primitív adatbázis-művelet rendelkezésre állásának biztosításához.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Példa: Tömeges adatok importálása egy adatbázis-programba
Alább, amelyek egy tárolt eljárás írt tömeges importálással dokumentumok egy gyűjteménybe. Vegye figyelembe, hogyan a tárolt eljárás kezeli a logikai ellenőrzésével körülhatárolt végrehajtási visszatérési érték a Documentclient, és ezután használja a száma, a tárolt eljárás egyes elindításaihoz beszúrt dokumentumok nyomon követését és a folyamat folytatásához kötegekre lebontva.

```javascript
function bulkImport(docs) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();

    // The count of imported docs, also used as current doc index.
    var count = 0;

    // Validate input.
    if (!docs) throw new Error("The array is undefined or null.");

    var docsLength = docs.length;
    if (docsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create a document.
    tryCreate(docs[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted. 
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called docs.length times.
    //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(doc, callback) {
        var isAccepted = collection.createDocument(collectionLink, doc, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client, 
        // which will call the script again with remaining set of docs.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when collection.createDocument is done in order to process the result.
    function callback(err, doc, options) {
        if (err) throw err;

        // One more document has been inserted, increment the count.
        count++;

        if (count >= docsLength) {
            // If we created all documents, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(docs[count], callback);
        }
    }
}
```

## <a id="trigger"></a> Adatbázis-eseményindítók
### <a name="database-pre-triggers"></a>Adatbázis előtti eseményindítók
A cosmos DB biztosít eseményindítókat, amelyek végrehajtása vagy a dokumentum egy olyan műveletet váltott. Megadhatja például előtti eseményindító, ha egy dokumentum létrehozásakor – ez előtti trigger futni fog a dokumentum létrehozása előtt. Az alábbi példa bemutatja, hogyan előtti eseményindítók használható egy dokumentumot létrehozott tulajdonságainak ellenőrzése:

```javascript
var validateDocumentContentsTrigger = {
    id: "validateDocumentContents",
    serverScript: function validate() {
        var context = getContext();
        var request = context.getRequest();

        // document to be created in the current operation
        var documentToCreate = request.getBody();

        // validate properties
        if (!("timestamp" in documentToCreate)) {
            var ts = new Date();
            documentToCreate["my timestamp"] = ts.getTime();
        }

        // update the document that will be created
        request.setBody(documentToCreate);
    },
    triggerType: TriggerType.Pre,
    triggerOperation: TriggerOperation.Create
}
```

És az eseményindító megfelelő Node.js ügyféloldali regisztrációs kódját:

```javascript
// register pre-trigger
client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
    .then(function (response) {
        console.log("Created", response.resource);
        var docToCreate = {
            id: "DocWithTrigger",
            event: "Error",
            source: "Network outage"
        };

        // run trigger while creating above document 
        var options = { preTriggerInclude: "validateDocumentContents" };

        return client.createDocumentAsync(collection.self,
              docToCreate, options);
    }, function (error) {
        console.log("Error", error);
    })
.then(function (response) {
    console.log(response.resource); // document with timestamp property added
}, function (error) {
    console.log("Error", error);
});
```

Üzem előtti eseményindítók nem lehet bemeneti paramétereket. A kérelem objektum a kérelemüzenet a művelethez társított kezelésére használható. Itt az üzem előtti eseményindító fut egy dokumentumot létrehozni, és a kérés törzse tartalmazza az JSON-formátumban kell létrehozni a dokumentumot.   

Eseményindítók regisztrálásakor a felhasználók megadhatják a műveletek, amelyek való futtatás. Ez az eseményindító TriggerOperation.Create, ami azt jelenti, hogy az eseményindító használatával az alábbi kódban látható módon csere művelet nem engedélyezett jött létre.

```javascript
var options = { preTriggerInclude: "validateDocumentContents" };

client.replaceDocumentAsync(docToReplace.self,
              newDocBody, options)
.then(function (response) {
    console.log(response.resource);
}, function (error) {
    console.log("Error", error);
});

// Fails, can’t use a create trigger in a replace operation
```
### <a name="database-post-triggers"></a>Adatbázis utáni eseményindítók
Utáni eseményindítók előtti eseményindítók, például egy dokumentum egy olyan műveletet társított, és nem használ a bemeneti paramétereket. Ezek futtatása **után** a művelet befejeződött, és hozzáférhetnek a az ügyfélnek küldött válaszüzenet.   

Az alábbi példa bemutatja a utáni eseményindítók működés közben:
```javascript
var updateMetadataTrigger = {
    id: "updateMetadata",
    serverScript: function updateMetadata() {
        var context = getContext();
        var collection = context.getCollection();
        var response = context.getResponse();

        // document that was created
        var createdDocument = response.getBody();

        // query for metadata document
        var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
        var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
            updateMetadataCallback);
        if(!accept) throw "Unable to update metadata, abort";

        function updateMetadataCallback(err, documents, responseOptions) {
            if(err) throw new Error("Error" + err.message);
                     if(documents.length != 1) throw 'Unable to find metadata document';

                     var metadataDocument = documents[0];

                     // update metadata
                     metadataDocument.createdDocuments += 1;
                     metadataDocument.createdNames += " " + createdDocument.id;
                     var accept = collection.replaceDocument(metadataDocument._self,
                           metadataDocument, function(err, docReplaced) {
                                  if(err) throw "Unable to update metadata, abort";
                           });
                     if(!accept) throw "Unable to update metadata, abort";
                     return;                    
        }                                                                                            
    },
    triggerType: TriggerType.Post,
    triggerOperation: TriggerOperation.All
}

```
Az eseményindító az alábbi mintában látható módon lehet regisztrálni.
```javascript
// register post-trigger
client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
    .then(function(createdTrigger) { 
        var docToCreate = { 
            name: "artist_profile_1023",
            artist: "The Band",
            albums: ["Hellujah", "Rotators", "Spinning Top"]
        };

        // run trigger while creating above document 
        var options = { postTriggerInclude: "updateMetadata" };

        return client.createDocumentAsync(collection.self,
              docToCreate, options);
    }, function(error) {
        console.log("Error" , error);
    })
.then(function(response) {
    console.log(response.resource); 
}, function(error) {
    console.log("Error" , error);
});
```

Ez az eseményindító lekérdezi a metaadat-dokumentum, és frissíti, az újonnan létrehozott dokumentum részleteit.  

Egyik dolog, vegye figyelembe a **tranzakciós** végrehajtásának eseményindítók Cosmos DB-ben. Ez utáni eseményindító ugyanabban a tranzakcióban, mint az eredeti dokumentum létrehozásának részeként futtatja. Ezért ha Ön kivételt a utáni eseményindítóból (például ha nem tudja frissíteni a metaadat-dokumentum), a teljes tranzakció sikertelen lesz, és vissza lesz állítva. Egyetlen dokumentumot sem jön létre, és kivételt adja vissza.  

## <a id="udf"></a>Felhasználó által definiált függvények
Felhasználó által definiált függvények (UDF) kiterjesztése az Azure Cosmos DB SQL-lekérdezési nyelvi szintaxis és egyéni üzleti logika végrehajtására szolgálnak. Ezek lze volat pouze a lekérdezések belül. Ezek nem rendelkeznek hozzáféréssel a context objektumot, és van kialakítva, hogy csak számítási JavaScript használható. Ezért UDF-EK futtatható a Cosmos DB szolgáltatás másodlagos replikákon.  

Az alábbi minta létrehoz egy UDF különféle bevételi zárójelben díjszabása alapján adó kiszámításához, és azt használja a lekérdezésbe, aki több mint 20 000 fizetett adók összes személyek keresése.

```javascript
var taxUdf = {
    id: "tax",
    serverScript: function tax(income) {

        if(income == undefined) 
            throw 'no input';

        if (income < 1000) 
            return income * 0.1;
        else if (income < 10000) 
            return income * 0.2;
        else
            return income * 0.4;
    }
}
```

Az UDF ezt követően az alábbi minta például a lekérdezések használhatók:

```javascript
// register UDF
client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
    .then(function(response) { 
        console.log("Created", response.resource);

        var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
        return client.queryDocuments('dbs/testdb/colls/testColl',
               query).toArrayAsync();
    }, function(error) {
        console.log("Error" , error);
    })
.then(function(response) {
    var documents = response.feed;
    console.log(response.resource); 
}, function(error) {
    console.log("Error" , error);
});
```

## <a name="javascript-language-integrated-query-api"></a>A JavaScript nyelvintegrált lekérdezési API
Dokumentumkeresési lekérdezések az Azure Cosmos DB SQL-szintaxis használata mellett a [kiszolgálóoldali SDK](https://azure.github.io/azure-cosmosdb-js-server/) optimalizált lekérdezések SQL ismerete egy fluent JavaScript-felület használatával végezhető el. Az API lehetővé teszi a programozott módon hozhat létre lekérdezéseket átadásával a predikátum függvény chainable függvény JavaScript lekérdezés hív meg, egy ECMAScript5 a tömb built-ins és a népszerű JavaScript-kódtárak Lodash hasonló szintaxissal. Lekérdezések által futtatandó hatékonyan navigálhat az Azure Cosmos DB-indexek a JavaScript futásidejű elemzi.

> [!NOTE]
> `__` (double-aláhúzásjelet) szerepel aliasként a `getContext().getCollection()`.
> <br/>
> Más szóval használhatja `__` vagy `getContext().getCollection()` a JavaScript-lekérdezés API eléréséhez.
> 
> 

Támogatott funkciók a következők:

<ul>
<li>
<b>CHAIN().... érték ([visszahívási] [, beállítások])</b>
<ul>
<li>
Value() –, be kell fejezni láncolt hívás kezdődik.
</li>
</ul>
</li>
<li>
<b>Filter (predicateFunction [, beállítások] [, visszahívási])</b>
<ul>
<li>
A predikátum függvény, amely ad vissza igaz/hamis érték, ha a bejövő és kimenő bemeneti dokumentumok az eredményül kapott csoportba használatával bemeneti szűri. Ez a függvény működését hasonló WHERE záradék az SQL-ben.
</li>
</ul>
</li>
<li>
<b>térkép (transformationFunction [, beállítások] [, visszahívási])</b>
<ul>
<li>
Egy leképezési megadott átalakító függvény, amely minden egyes bemeneti elem képez le egy JavaScript-objektumot vagy értéket vonatkozik. Ez a függvény működését a SELECT záradékban az SQL hasonló.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Ez a funkció akkor látható, hogy egyetlen tulajdonság értéke kigyűjti az egyes bemeneti elem parancsikont.
</li>
</ul>
</li>
<li>
<b>egybesimítás ([isShallow] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Egyesíti, és a egy egyetlen olyan tömböt az egyes bemeneti elemeiről-tömbök simítja egybe. Ez a függvény működését a LINQ SelectMany hasonló.
</li>
</ul>
</li>
<li>
<b>rendezési szempontjainak helyi ([predikátum] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Dokumentumok új készletét termék szerint rendezi a dokumentumokat a bemeneti dokumentum Stream növekvő sorrendben az adott predikátum használatával. Ez a függvény működését egy ORDER BY záradékban az SQL hasonló.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predikátum] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Dokumentumok új készletét termék szerint rendezi a dokumentumokat a bemeneti dokumentum Stream csökkenő sorrendben az adott predikátum használatával. Ez a függvény működését egy x DESC ORDER BY záradékban az SQL hasonló.
</li>
</ul>
</li>
</ul>


Belefoglalja predikátum és/vagy választó funkciók belül, a következő JavaScript-szerkezetek automatikusan optimalizálja a közvetlenül az Azure Cosmos DB-indexek futtatásához:

* Egyszerű operátorok: = + – * / % |} ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literálok, beleértve a objektumliterálhoz: {}
* var, visszatérési

A következő JavaScript-szerkezetek nem optimalizálja az Azure Cosmos DB-indexek:

* Átvitelvezérlés (például ha, miközben)
* Függvényhívások

További információkért lásd: a [kiszolgálóoldali JSDocs](https://azure.github.io/azure-cosmosdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Példa: Írhat egy tárolt eljárást a JavaScript query API használatával
Az alábbi kódmintában egyik példája a lekérdezési JavaScript API hogyan használható a tárolt eljárás keretében. A tárolt eljárás szúr be egy dokumentumot, adja meg a bemeneti paraméterek, és a egy metaadatainak dokumentálja, használja a `__.filter()` metódus: minSize, a maxSize és a bemeneti dokumentum size tulajdonság alapján totalSize.

```javascript
/**
 * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
 */
function insertDocumentAndUpdateMetadata(doc) {
  // HTTP error codes sent to our callback funciton by DocDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
    if (err) throw err;

    // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
    if (!doc.isMetadata && doc.size > 0) {
      // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata doc was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

        // The metadata document.
        var metaDoc = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
        else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

        // Update metaDoc.maxSize.
        metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

        // Update metaDoc.totalSize.
        metaDoc.totalSize += doc.size;

        // Update/replace the metadata document in the store.
        var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
}
```

## <a name="sql-to-javascript-cheat-sheet"></a>SQL-Javascript – Adatlap
Az alábbi táblázat bemutatja a különböző SQL-lekérdezések és a kapcsolódó JavaScript-lekérdezéseket.

Az SQL-lekérdezésekhez, dokumentum-tulajdonság kulcsokat (például `doc.id`) kis-és nagybetűket.

|SQL| JavaScript-lekérdezési API|Az alábbi leírása|
|---|---|---|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;doc; vissza<br>});|1|
|Válassza ki docs.id, mint docs.message msg, docs.actions <br>DOKUMENTÁCIÓ|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ<br>WHERE docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc.id === "X998_Y998";<br>});|3|
|VÁLASSZA KI *<br>DOKUMENTÁCIÓ<br>HOL ARRAY_CONTAINS (docs. Címkék, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;vissza a x.Tags & & x.Tags.indexOf(123) > -1;<br>});|4|
|Válassza ki docs.id, docs.message, msg<br>DOKUMENTÁCIÓ<br>WHERE docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|5|
|SELECT VALUE címke<br>DOKUMENTÁCIÓ<br>CSATLAKOZZON a docs címke. A címkék<br>Az ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;vissza a dokumentumot. A címkék & & Array.isArray (doc. A címkék);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc._ts; vissza<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

Az alábbi leírásokat a fenti táblázatban szereplő minden egyes lekérdezés ismertetik.
1. Az összes dokumentum (többoldalas a folytatási token), az eredményeket.
2. Projektek, a azonosítója, az üzenet (aliassal való msg) és a művelet az összes dokumentumot.
3. A predikátum a dokumentumok lekérdezések: azonosító = "X998_Y998".
4. Lekérdezések, amelyek a Tags tulajdonságnak és címkék dokumentumok je Pole obsahující a az 123 érték.
5. A dokumentumok, amelyek egy predikátum lekérdezések id = "X998_Y998", és ezután-projektek, az azonosítót és az üzenet (aliassal való msg).
6. Vlastnost typu Pole, címkék, rendelkeznie dokumentumoknál szűri és az eredményül kapott dokumentumokat a _ts időbélyeg rendszertulajdonság, majd a projektek + simítja egybe a címkék tömb.


## <a name="runtime-support"></a>Podpora modulu Runtime
Az Azure Cosmos DB [JavaScript kiszolgáló ügyféloldali API](https://azure.github.io/azure-cosmosdb-js-server/) támogatást nyújt a legtöbb funkcióját a alapvető technikai JavaScript nyelven, a szabványos [ECMA-262](https://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Biztonság
A JavaScript tárolt eljárások és eseményindítók vendégünk, hogy egy szkript hatásait nem nyilvánosságra kerüljenek a másik a pillanatkép-tranzakció elkülönítés az adatbázis szintjén áthaladás nélkül. A futtatókörnyezetet készletezett, de a környezet minden egyes futás után törlődik. Ezért ezek garantáltan biztonságos, minden olyan nem kívánt mellékhatásokkal egymástól.

### <a name="pre-compilation"></a>Üzem előtti fordítás
Tárolt eljárások, eseményindítók és felhasználói függvények minden szkripthívás időpontjában fordítási költségek elkerülése érdekében olyan implicit módon lefordított kód bájt formátumra. Üzem előtti fordítási biztosítja a tárolt eljárások hívása gyors és a egy alacsony erőforrás-igényű rendelkezik.

## <a name="client-sdk-support"></a>Ügyfél SDK-támogatás
Az Azure Cosmos DB mellett [Node.js](sql-api-sdk-node.md) API, Azure Cosmos DB rendelkezik [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript ](sql-api-sdk-node.md), és [Python SDK-k](sql-api-sdk-python.md) , valamint az SQL API-hoz. Tárolt eljárások, eseményindítók és felhasználói függvények hozhatók létre, és ezek SDK-k, valamint valamelyik használatával végrehajtott. Az alábbi példa bemutatja, hogyan hozhat létre, és a .NET-kliens használata a tárolt eljárás végrehajtására. Megjegyzés: a .NET-típusokat JSON-fájlként tárolt eljárásnak átadott és olvassa el a biztonsági hogyan.

```javascript
var markAntiquesSproc = new StoredProcedure
{
    Id = "ValidateDocumentAge",
    Body = @"
            function(docToCreate, antiqueYear) {
                var collection = getContext().getCollection();    
                var response = getContext().getResponse();    

                if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                    docToCreate.antique = true;
                }

                collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                    function(err, docCreated, options) { 
                        if(err) throw new Error('Error while creating document: ' + err.message);                              
                        if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                        response.setBody(docCreated);
                });
         }"
};

// register stored procedure
StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
dynamic document = new Document() { Id = "Borges_112" };
document.Title = "Aleph";
document.Year = 1949;

// execute stored procedure
Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);
```

Ez a példa bemutatja, hogyan használhatja a [SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) előtti eseményindító létrehozása, és hozzon létre egy dokumentumot a trigger engedélyezve van. 

```javascript
Trigger preTrigger = new Trigger()
{
    Id = "CapitalizeName",
    Body = @"function() {
        var item = getContext().getRequest().getBody();
        item.id = item.id.toUpperCase();
        getContext().getRequest().setBody(item);
    }",
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};

Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
    new RequestOptions
    {
        PreTriggerInclude = new List<string> { "CapitalizeName" },
    });
```

A következő példa bemutatja, hogyan hozhat létre egy felhasználói függvény (UDF), és használhatja, és egy [SQL-lekérdezés](how-to-sql-query.md).

```javascript
UserDefinedFunction function = new UserDefinedFunction()
{
    Id = "LOWER",
    Body = @"function(input) 
    {
        return input.toLowerCase();
    }"
};

foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
    "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
{
    Console.WriteLine("Read {0} from query", book);
}
```

## <a name="rest-api"></a>REST API
Azure Cosmos DB minden művelet egy RESTful módon hajtható végre. Tárolt eljárások, eseményindítók és felhasználó által definiált függvények regisztrálhatók a gyűjtemény HTTP POST használatával. Az alábbi példa bemutatja, hogyan regisztrálhat egy tárolt eljárást:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


A tárolt eljárás végrehajtásával: egy POST-kérelmet a URI adatbázisok/testdb/colls/testColl/sprocs ellen a törzs tartalmazza a tárolt eljárás létrehozása az regisztrálva van. Eseményindítók és felhasználói függvények hasonlóan egy HOZZÁSZÓLÁST/eseményindítók és /udfs rendre kiállításával regisztrálható.
Ez a tárolt eljárást is, majd hajtható végre, szemben az erőforrás-hivatkozás egy POST-kérés kiadásával:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


A tárolt eljárás bemeneti átadott itt, a kérelem törzsében. A bemeneti bemeneti paramétereket JSON-tömbként lett átadva. A tárolt eljárást az első bemenet, amely a válasz törzséhez dokumentumként vesz igénybe. A választ kap a következőképpen történik:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: 'Autumn of the Patriarch',
      id: 'V7tQANV3rAkDAAAAAAAAAA==',
      ts: 1407830727,
      self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
      etag: '6c006596-0000-0000-0000-53e9cac70000',
      attachments: 'attachments/',
      Price: 200
    }


Ellentétben a tárolt eljárások, eseményindítók közvetlenül nem hajtható végre. Ehelyett azok végrehajtása egy dokumentum egy művelet részeként. Megadhatja, hogy az eseményindítók a kérést a HTTP-fejlécek futtatásához. A következő kód bemutatja egy dokumentum létrehozására vonatkozó kérelem.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger

    {
       "name": "newDocument",
       "title": "The Wizard of Oz",
       "author": "Frank Baum",
       "pages": 92
    }


Itt a kérelemmel előtti eseményindítót a x-ms-documentdb-pre-trigger-include fejlécben megadott. Ennek megfelelően a utáni eseményindítókat a x-ms-documentdb-post-trigger-include fejléc van megadva. Megvalósítás előtti és utáni eseményindítók egy adott kérés esetében adható meg.

## <a name="sample-code"></a>Mintakód
Kiszolgálóoldali kód további példákat talál (beleértve a [tömeges törlés](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/bulkDelete.js), és [frissítése](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/update.js)) a a [GitHub-adattár](https://github.com/Azure/azure-cosmosdb-js-server/tree/master/samples).

Szeretné megosztani a Soft tárolt eljárás? a tárház hozzájárul, és hozzon létre egy pull-kérelem! 

## <a name="next-steps"></a>További lépések
Ha már rendelkezik egy vagy több tárolt eljárások, eseményindítók és felhasználó által definiált függvények létrehozása, importálná őket, és megtekintheti őket az adatkezelő segítségével, az Azure Portalon.

Azt is tapasztalhatja a következő hivatkozások és erőforrások hasznos, ha az elérési út tudhat meg többet az Azure Cosmos dB kiszolgálóoldali programozása:

* [Az Azure Cosmos DB a JavaScript kiszolgálóoldali API-referencia](https://azure.github.io/azure-cosmosdb-js-server/)
* [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JavaScript ECMA-262](https://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Biztonságos és hordozható adatbázis bővíthetőség](https://dl.acm.org/citation.cfm?id=276339) 
* [Szolgáltatás-orientált adatbázis-architektúra](https://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [A .NET-futtatórendszer a Microsoft SQL server üzemeltetése](https://dl.acm.org/citation.cfm?id=1007669)
