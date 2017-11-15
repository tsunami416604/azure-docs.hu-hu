---
title: "A kiszolgálóoldali JavaScript programozás Azure Cosmos DB |} Microsoft Docs"
description: "Útmutató: Azure Cosmos-Adatbázist kíván használni a tárolt eljárások, eseményindítók adatbázis és a felhasználó által megadott funkciókat (UDF) írását a JavaScript. Adatbázis programing tippeket és több kapják meg."
keywords: "Adatbázis-eseményindítók, tárolt eljárás, tárolt eljárás, adatbázis program, sproc, a documentdb, azure, a Microsoft azure"
services: cosmos-db
documentationcenter: 
author: aliuy
manager: jhubbard
editor: mimig
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: andrl
ms.openlocfilehash: ef191c3c8d85afa389859956d30b5ac0275053d2
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Az Azure Cosmos DB kiszolgálóoldali programozása: tárolt eljárások, eseményindítók adatbázis és a felhasználó által megadott függvények
Ismerje meg, hogy Azure Cosmos DB nyelvi integrálva, a tranzakciós végrehajtását a JavaScript lehetővé teszi, hogy a fejlesztők írási **tárolt eljárások**, **eseményindítók** és **felhasználó által definiált funkciókat (UDF)** a natív módon egy [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript. Ez lehetővé teszi az alkalmazáslogikák adatbázis program szállított és végre közvetlenül az adatbázis adattárolási partíciókat. 

Azt javasoljuk, hogy Kezdésként tekintse meg az alábbi videót, amelyben Andrew Liu Cosmos DB kiszolgálóoldali adatbázis programozási modell rövid áttekintést nyújt. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Demo-A-Quick-Intro-to-Azure-DocumentDBs-Server-Side-Javascript/player]
> 
> 

Ezt követően térjen vissza a cikkhez, ahol megtudhatja, a következő kérdések megválaszolásához:  

* Hogyan írni egy tárolt eljárás, eseményindító vagy használatával JavaScript UDF?
* Hogyan Cosmos DB sav garantálni?
* Hogyan működik a Cosmos DB tranzakciók?
* Mik azok a előre elindítja és utáni indítása, és hogyan hajtsa végre írási egy?
* Hogyan regisztrálja és futtatni egy tárolt eljárás, eseményindító vagy UDF RESTful módon HTTP-n keresztül?
* Mi Cosmos DB SDK-k érhetők el hozhatnak létre és futtathatnak tárolt eljárások, eseményindítók és felhasználó által megadott függvények?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Bevezetés a tárolt eljárás és UDF programozás
Ez a megközelítés a *"JavaScript egy T-SQL modern napot"* felszabadítja a rendszer típuseltérést és objektum-relációs leképezés technológiák bonyolultságára alkalmazásfejlesztők. Azt is számos belső előnyt, sokoldalú alkalmazásokat hozhatnak létre állítható be:  

* **Eljárási logika:** JavaScript egy magas szintű programozási nyelv, üzleti logika express gazdag és ismerős felületet biztosít. Az adatok műveletek közelebb összetett sorozatát végezheti el.
* **Az atomi tranzakciók:** Cosmos DB biztosítja, hogy az adatbázis-műveletet hajtott végre egy tárolt eljárás vagy eseményindító belül atomi. Ez lehetővé teszi, hogy egy alkalmazás egyesítése egyetlen kötegben kapcsolódó műveleteket, hogy az összes sikeres legyen, vagy egyiket sem sikerült. 
* **Teljesítmény:** , hogy a JSON alapvetően a Javascript nyelv típusrendszernek van leképezve, és egyben a alapvető egysége a Cosmos DB tárolási lehetővé teszi, hogy optimalizálásokat Lusta materialization pufferkészletben JSON-dokumentumok például számos és minősítené igény végrehajtás alatt álló kódot. Nincsenek további teljesítménybeli előnyökben szállítási üzleti logika az adatbázishoz tartozó:
  
  * Kötegelés – fejlesztők csoport műveletek tartoznak, mint a beszúrások, és a tömeges küldheti el ezeket. A hálózati forgalom késés költségeket, és a tároló terhelése hozzon létre külön tranzakciók jelentősen csökkent. 
  * A fordítás előtti – Cosmos DB precompiles tárolt eljárások, eseményindítók és felhasználó által megadott funkciókat (UDF) JavaScript fordítási költség egyes elindításaihoz elkerülése érdekében. A terhelés létrehozásának eljárási logika bájt kódjának van amortized minimális értéket.
  * Alkalmazás-előkészítés – sok műveleteket kell egy mellékhatása ("eseményindító"), amely potenciálisan magában foglalja a egy vagy több másodlagos tároló műveleteket. Vezérelt atomicity Ez a további performant, amikor a kiszolgáló kerül. 
* **Beágyazás:** a tárolt eljárások üzleti logika egy helyen csoportosításához használható. Ez a két előnnyel rendelkezik:
  * Hozzáadja a nyers adatokat, amely lehetővé teszi az adatok fejlesztők azt fejleszteni az alkalmazásaikat, függetlenül az adatokból fölött egy absztrakciós réteget. Ez különösen hasznos, amikor az adatok séma nélküli, miatt a rideg feltételek, amelyek esetleg bővíthetőség az alkalmazásba, ha közvetlenül az adatok kezelésére.  
  * Ez az absztrakció lehetővé teszi, hogy a vállalatok számára az adatokat a hozzáférést a parancsfájlok a egyszerűsítése biztonsága.  

A létrehozási és az adatbázis eseményindítók, tárolt eljárás és egyéni lekérdezési operátorok végrehajtási keresztül támogatja a [REST API](/rest/api/documentdb/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), és [ügyfél SDK-k](documentdb-sdk-dotnet.md)például .NET, Node.js és JavaScript számos platformon.

Ez az oktatóanyag használja a [Node.js SDK-val Q tett](http://azure.github.io/azure-documentdb-node-q/) szintaxis és a tárolt eljárások, eseményindítók és felhasználó által megadott függvények használatát mutatja be.   

## <a name="stored-procedures"></a>Tárolt eljárások
### <a name="example-write-a-simple-stored-procedure"></a>Példa: Egy egyszerű tárolt eljárás írása
Kezdjük egy egyszerű tárolt eljárás, amely a "Hello, World" választ ad vissza.

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Tárolt eljárások gyűjteményenként regisztrálva van, és a dokumentum és a mellékletek megtalálható a gyűjteményben is működik. Az alábbi kódrészletben láthatja, hogyan kell regisztrálni a helloWorld tárolt eljárás gyűjtemény tartozik. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


Regisztrálva van a következő tárolt eljárást, ha azt végre gyűjteményre, és olvassa el az eredmények vissza az ügyfélen. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


A környezeti objektumot biztosít az összes művelet végrehajtható Cosmos DB tároló elérésére, valamint a hozzáférési kérelem-válasz objektumokhoz. A válasz objektum ebben az esetben állítsa be a választörzs az ügyfélnek küldött használtuk. További részletekért tekintse meg a [Azure Cosmos DB JavaScript server SDK-dokumentáció](http://azure.github.io/azure-documentdb-js-server/).  

Ossza meg velünk bontsa ki az ebben a példában, és adja hozzá a további adatbázis-funkciók a tárolt eljárás. Tárolt eljárások létrehozása, frissítése, olvassa el, lekérdezheti és dokumentumok és mellékletek belül a gyűjtemény törlése.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Példa: Hozzon létre egy dokumentumot egy tárolt eljárás írása
A következő kódrészletet bemutatja, hogyan használhatja a környezeti objektumot Cosmos DB erőforrásokat.

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


Ez a tárolt eljárás bemeneti documentToCreate, az aktuális gyűjtemény létrehozni egy dokumentum, időt vesz igénybe. Ilyen műveletek aszinkron jellegűek, és a JavaScript függvény visszahívások függenek. A visszahívási függvénynek két paraméterrel, egy, a hiba objektum esetében a művelet sikertelen lesz, és egy a létrehozott objektum. A visszahívási belül felhasználók kezelte a kivételt, vagy hiba throw. Egy visszahívás nem áll rendelkezésre, és nem sikerül, az Azure Cosmos DB futásidejű hibát jelez.   

A fenti példában a visszahívás hibát jelez, ha a művelet sikertelen volt. Ellenkező esetben azt állítja be a létrehozott dokumentum azonosítója a választörzs az ügyfélnek. Ez a tárolt eljárás bemeneti paraméterekkel végrehajtásának módját.

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


Vegye figyelembe, hogy ez a tárolt eljárás bemeneti dokumentum szervek tömbje eltarthat, és hozza létre a címzetteket a azonos tárolt eljárás végrehajtása több hálózati kérelmek, mindegyikhez külön-külön létrehozása helyett az összes kell módosítani. Ez egy hatékony tömeges importáló végrehajtása Cosmos DB (Ez az oktatóanyag későbbi részében bemutatása) is használható.   

A bemutatott példában bemutatott tárolt eljárások használatával. Bemutatjuk, eseményindítók és felhasználó által megadott funkciókat (UDF) az oktatóanyag későbbi részében.

## <a name="database-program-transactions"></a>Adatbázis-program tranzakciók
Egy tipikus adatbázisban tranzakció munka egyetlen logikai egységként végrehajtott műveletek sorozata adható meg. Minden tranzakció biztosít **ACID garanciák**. SAV egy jól ismert mozaikszó négy tulajdonságai – Atomicity, konzisztencia, elkülönítési és tartósságot jelző.  

Röviden atomicity garantálja, hogy egyetlen egységként kezelt-e a minden dolgozott tranzakción belül hol vagy az összes véglegesítve vagy "none". Konzisztencia lehetővé teszi, hogy az adatok mindig a megfelelő belső állapotban tranzakciók között. Elkülönítési garantálja, hogy két tranzakciók zavarják – általában, a legtöbb kereskedelmi rendszerek adjon meg több elkülönítési szinten használható az alkalmazás igények alapján. Tartósságot biztosítja, hogy bármi is módosul az adatbázis előjegyzett mindig szerepel.   

A Cosmos DB JavaScript tárolva az adatbázisban memóriaterületén. Emiatt kérések belül tárolt eljárások és eseményindítók végrehajtani ugyanabban a hatókörben egy adatbázis-munkamenet. Ez lehetővé teszi a Cosmos DB sav garantálja az egyetlen tárolt eljárás vagy eseményindító részét képező minden műveletnél. Vegye figyelembe a következő tárolt eljárás definíciója:

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

Ez a tárolt eljárás kereskedelmi elemek között két játékosok egyetlen művelettel játék alkalmazásokban tranzakciók használja. A tárolt eljárás megkísérli argumentumként átadott a player azonosítók megfelelő két dokumentumok olvasását. Ha mindkét player dokumentum található, majd a tárolt eljárás frissíti a dokumentumok csere az elemeket. Ha a hibák elérhesse ezt a hibát, azt, amely implicit módon megszakítja a tranzakciót JavaScript kivételt okoz.

Ha a gyűjteményhez a következő tárolt eljárás regisztrálva van-e elleni egypartíciós gyűjtemény, akkor a tranzakció hatókörét a gyűjteményben lévő összes dokumentumot. Ha a gyűjtemény particionálva van, majd tárolt eljárások a tranzakció hatókörében egypartíciós kulcs lesznek végrehajtva. Minden egyes tárolt eljárás végrehajtása majd tartalmaznia kell egy megfelelő kell futnia a tranzakció hatókörében a partíciós kulcs értéke. További részletekért lásd: [Azure Cosmos DB particionálás](partition-data.md).

### <a name="commit-and-rollback"></a>Érvényesítés és visszaállítás
Tranzakciók mélyen és natív módon integrálva vannak Cosmos DB JavaScript programozási modellt. A JavaScript függvényen belül minden műveletet vannak automatikusan végett az egyetlen tranzakció alatt. Ha a JavaScript kivételen nélkül befejeződött, a műveleteket, így az adatbázis lépnek. Érvényben van a "BEGIN TRANSACTION" és "COMMIT TRANSACTION" utasítások a relációs adatbázisok implicit Cosmos DB-ben.  

Ha minden kivételt, amely a parancsfájlból propagálja, Cosmos DB a JavaScript futásidejű állítja vissza a teljes tranzakciót. A korábbi példa szerint, egy kivétel kiváltása megegyezik hatékonyan egy "ROLLBACK TRANSACTION" Cosmos DB-ben.

### <a name="data-consistency"></a>Adatkonzisztencia
Tárolt eljárások és eseményindítók mindig végrehajtása az Azure Cosmos DB tároló az elsődleges replikán. Ez biztosítja, hogy az olvasások belül tárolt eljárások ajánlat az erős konzisztencia. Lekérdezések felhasználó által definiált függvények használatával hajtható végre az elsődleges vagy a másodlagos másodpéldány, de gondoskodunk róla, hogy megfeleljen a kért konzisztenciaszint válassza ki a megfelelő replika.

## <a name="bounded-execution"></a>A kötött végrehajtása
Minden Cosmos DB műveletet kell végeznie a megadott kiszolgálón belül kérelmek időtúllépési időtartama. Ez a korlátozás vonatkozik JavaScript-funkcióként (tárolt eljárások, eseményindítók és felhasználó által definiált függvények) is. Ha egy művelet nem fejeződött be az ezt az időkorlátot, a tranzakció vissza lesz állítva. JavaScript-funkcióként kell befejezni az időkorláton belül vagy egy kötegelt/Folytatás végrehajtási alapú folytatási modellt.  

Tárolt eljárások és eseményindítók időkorlátokat, a gyűjtemény objektumra (létrehozása, olvasása, cserélje le és törölje a dokumentumok és mellékletek) alatti összes funkciójának kezeléséhez egyszerűsítése érdekében visszatérési egy logikai értéket képviselő e művelet befejeződik. Ha ez az érték hamis, akkor arra utal, hogy az időkorlát lejár, és, hogy az eljárás végrehajtása be burkolnia kell.  Az első elfogadhatatlan adattárolási művelet műveletek aszinkron előtti garantáltan befejeződik, ha a tárolt eljárás idő alatt fejeződik be, és nem várólistára további kérelmeket.  

JavaScript-funkcióként is kötve van a hálózatierőforrás-fogyasztás. Cosmos DB fenntart egy adatbázis-fiók kiosztott mérete alapján gyűjteményenként átviteli sebesség. Átviteli sebesség a CPU, a memória és a kérelemegység vagy RUs IO fogyasztás normalizált egységben van kifejezve. JavaScript-funkcióként is használhat egy nagy számú RUs rövid időn belül, és előfordulhat, hogy olvasson sebessége korlátozott, ha a gyűjtemény határértékét. Erőforrás intenzív tárolt eljárások is előfordulhat, hogy karanténba egyszerű adatbázis-művelet rendelkezésre állásának biztosításához.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Példa: Tömeges adatok importálása egy adatbázis-program
Alább példája tömeges importálással dokumentumok gyűjteménybe írt tárolt eljárást. Vegye figyelembe a következő tárolt eljárás kezelésének kötött végrehajtási logikai ellenőrzésével visszatérési érték a Documentclient, és egyes elindításaihoz a tárolt eljárás a dokumentumok számát a nyomon követése és a folyamat folytatásához kötegek között.

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

## <a id="trigger"></a>Adatbázis-eseményindítók
### <a name="database-pre-triggers"></a>Adatbázis előtti eseményindítók
Cosmos DB biztosít, amelyek végrehajtása, vagy egy műveletet a dokumentum által indított eseményindítók. Például lehetőségeiről előtti eseményindítót hoz létre egy dokumentumot – az előzetes eseményindító fog futni, a dokumentum létrehozása előtt. A következő egy példa hogyan előtti eseményindítók kerül létrehozásra dokumentumok tulajdonságainak érvényesítéséhez használható:

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


És a hozzá kapcsolódó a Node.js ügyféloldali regisztrációja a eseményindító:

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


Előtti eseményindítók nem tartozhat bemeneti paraméter. A request objektumon segítségével kezelheti a társított kérelemüzenethez tartozó a műveletet. Itt a dokumentum létrehozása előtti eseményindító futtatják, és a kérelem üzenettörzs tartalmazza a dokumentum hozható létre JSON formátumban.   

Eseményindítók regisztrált, amikor a felhasználók megadhatják való futtatás műveletek. TriggerOperation.Create, ami azt jelenti, hogy a következő nem engedélyezett ehhez az eseményindítóhoz hozták létre.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Adatbázis utáni eseményindítók
Utáni eseményindítók előtti eseményindítók, például egy műveletet a dokumentum tartoznak, és nem helyez el a bemeneti paramétereket. Futnak **után** a művelet befejeződött, és hozzáférhetnek a válaszüzenetet küldött az ügyfélnek.   

A következő példa bemutatja a utáni eseményindítók működés közben:

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


Az eseményindító regisztrálható, a következő mintában látható módon.

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


Ehhez az eseményindítóhoz lekérdezi a metaadat-dokumentum, és frissíti azt az újonnan létrehozott dokumentum adatait.  

Az egyik dolog, ami fontos megjegyezni a **tranzakciós** Cosmos DB eseményindítók végrehajtását. Ez utáni eseményindító futtatja ugyanabban a tranzakcióban, mint az eredeti dokumentumhoz létrehozása. Ezért a utáni eseményindítóval (például ha nem sikerült frissíteni a metaadat-dokumentum) azt kivételt jelez, ha a teljes tranzakció sikertelen lesz, és vissza lesz vonva. Nincs dokumentum jön létre, és kivételt adja vissza.  

## <a id="udf"></a>Felhasználó által definiált függvények
Felhasználói függvény (UDF) segítségével bővítheti a DocumentDB API SQL-lekérdezési nyelv szintaxis, valamint valósítja meg az üzleti logika. Ezek csak a hívható lekérdezéseken belül. Ezeket a nincs hozzáférése a context objektumot, és célja, hogy csak számítási JavaScript használható. Ezért a felhasználó által megadott függvények a másodlagos replikákon a Cosmos DB szolgáltatás futtatható.  

Az alábbi minta létrehoz egy UDF kiszámításához adó különböző bevétel zárójeleket sebességet alapján, és azt használja a lekérdezésben található összes olyan személyek, akik több mint 20 000 $ fizetett adók.

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


Az UDF ezt követően használható lekérdezésekben például a következő mintában:

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

## <a name="javascript-language-integrated-query-api"></a>A JavaScript nyelv integrált lekérdezés API
Mellett a DocumentDB SQL-szintaxis használatával lekérdezések kiállító, a kiszolgálóoldali SDK teszi lehetővé az SQL ismeretek nélkül Folyékonyan beszél JavaScript-illesztő segítségével optimalizált lekérdezések végrehajtásához. A JavaScript-lekérdezés API lehetővé teszi programozott módon hozhatók létre olyan lekérdezések úgy, hogy a predikátum függvény chainable függvénynek hívások, a megszokott ECMAScript5 tartozó tömb built-ins és népszerű JavaScript szalagtárak például lodash szintaxissal. Lekérdezések hajthatnak végre hatékonyan Azure Cosmos DB indexet a JavaScript futásidejű rendszer elemzi.

> [!NOTE]
> `__`(kettős-aláhúzásjel) aliasként `getContext().getCollection()`.
> <br/>
> Más szóval használhatja `__` vagy `getContext().getCollection()` a JavaScript lekérdezés API eléréséhez.
> 
> 

Támogatott funkciók a következők:

<ul>
<li>
<b>CHAIN().... érték ([visszahívási] [, beállítások])</b>
<ul>
<li>
Value() – amely kell lezárni láncolt hívás kezdődik.
</li>
</ul>
</li>
<li>
<b>szűrő (predicateFunction [, beállítások] [, visszahívási])</b>
<ul>
<li>
Szűrők használata a predikátum függvény, amely ahhoz, hogy a kimeneti/bemeneti dokumentumok szűrése az eredő igaz vagy hamis értéket ad vissza a bemeneti. Ez úgy viselkedik, hasonló SQL WHERE záradék.
</li>
</ul>
</li>
<li>
<b>térkép (transformationFunction [, beállítások] [, visszahívási])</b>
<ul>
<li>
Vonatkozik a leképezés egy átalakítási függvénynek, amely minden egyes bemeneti elem van leképezve a JavaScript vagy értéket kap. Ez úgy viselkedik, a SELECT záradékban az SQL hasonló.
</li>
</ul>
</li>
<li>
<b>pluck ([propertyName] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Ez egy olyan térképet, amely egyetlen tulajdonság értékének összes beviteli elemet a parancsikont.
</li>
</ul>
</li>
<li>
<b>egybesimítására ([isShallow] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Egyesíti, és egyetlen tömb minden egyes bemeneti eleménél-tömbök simítja. Ez úgy viselkedik, hasonló LINQ a selectmany metódus.
</li>
</ul>
</li>
<li>
<b>a sortBy ([predicate] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Létrehoznak egy új dokumentumot a bemeneti dokumentum-adatfolyamra növekvő sorrendben az adott predikátum használata a dokumentumok rendezésével. Ez úgy viselkedik, egy ORDER BY záradékban az SQL hasonló.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicate] [, beállítások] [, visszahívási])</b>
<ul>
<li>
Létrehoznak egy új dokumentumot a bemeneti dokumentum-adatfolyamra csökkenő sorrendben az adott predikátum használata a dokumentumok rendezésével. Ez úgy viselkedik, egy x DESC ORDER BY záradékban az SQL hasonló.
</li>
</ul>
</li>
</ul>


Predikátum és/vagy választó funkciók bekerüljön, amikor a következő JavaScript-szerkezet automatikusan beolvasása optimalizálták, hogy közvetlenül a Azure Cosmos DB indexek:

* Egyszerű operátorok: = + - * / % |} ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Szövegkonstans, beleértve az objektum szöveges: {}
* var, visszatérési

A következő JavaScript-szerkezet nem Azure Cosmos DB indexet az beszerzése optimalizált:

* Szabályozhatja a folyamat (pl. Ha, közben)
* Függvényhívások

További információkért lásd: a [kiszolgálóoldali JSDocs](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Példa: Írni a JavaScript lekérdezés API használatával tárolt eljárás
A következő példakód példája a JavaScript lekérdezés API hogyan használható egy tárolt eljárás keretében. A tárolt eljárás szúr be egy dokumentum, egy bemeneti paraméter által megadott és a metaadatok dokumentálása, használja a `__.filter()` minSize maxSize és a bemeneti dokumentum size tulajdonság alapján totalSize metódust.

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

## <a name="sql-to-javascript-cheat-sheet"></a>A Javascript-Adatlap SQL
Az alábbi táblázat mutatja be a különböző SQL-lekérdezések és a kapcsolódó JavaScript-lekérdezéseket.

Az SQL-lekérdezések, a dokumentum tulajdonság kulcsok, (pl. `doc.id`)-és nagybetűk.

|SQL| JavaScript lekérdezés API|Az alábbi leírása|
|---|---|---|
|VÁLASSZA KI *<br>A dokumentumok| __.Map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;térjen vissza a doc;<br>});|1|
|Jelölje be docs.id, mint docs.message adatköltségek, docs.actions <br>A dokumentumok|__.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;azonosító: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;üzenet: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|VÁLASSZA KI *<br>A dokumentumok<br>HOL docs.id="X998_Y998"|__.Filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;térjen vissza a doc.id === "X998_Y998";<br>});|3|
|VÁLASSZA KI *<br>A dokumentumok<br>HOL ARRAY_CONTAINS (dokumentumok. Címkék, 123)|__.Filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;térjen vissza a x.Tags & & x.Tags.indexOf(123) > -1;<br>});|4|
|Jelölje be docs.id, docs.message adatköltségek,<br>A dokumentumok<br>HOL docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;térjen vissza a doc.id === "X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{visszaadása<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;azonosító: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;üzenet: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|A SELECT VALUE címke<br>A dokumentumok<br>CSATLAKOZTASSA a docs címke. Címkék<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;térjen vissza a dokumentumot. Címkék & & Array.isArray (doc. Címkék);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;térjen vissza a doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|6|

Az alábbi leírásokat ismertetik a fenti táblázatban minden egyes lekérdezés.
1. Az összes dokumentumok (a folytatási kód paginated) eredményez.
2. Projektek a azonosítója, az üzenet (aliassal való adatköltségek) és a művelet az összes dokumentumot.
3. A predikátum dokumentumok ideális: azonosító = "X998_Y998".
4. A lekérdezések, amelyek a Tags tulajdonság és címkék dokumentumok 123 értéket tartalmazó tömb.
5. Lekérdezések dokumentumok predikátumával, azonosító = "X998_Y998", és ezután projektek azonosítója és üzenet (aliassal való adatköltségek).
6. A dokumentumok, amely egy tömb tulajdonságát, a címkéket, és az így kapott dokumentumokat rendezése _ts időbélyeg rendszer tulajdonság, majd projektek + a címkék tömb simítja szűrők.


## <a name="runtime-support"></a>Futásidejű támogatása
[A DocumentDB a JavaScript kiszolgáló oldalán API](http://azure.github.io/azure-documentdb-js-server/) támogatást nyújt a legtöbb funkcióját a alapvető technikai JavaScript nyelv által szabványosított, [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Biztonság
JavaScript tárolt eljárások és eseményindítók elkülönített, hogy egy parancsfájl eredő nem nyilvánosságra kerüljenek a másik a pillanatkép-tranzakció elkülönítés az adatbázis szintjén áthaladás nélkül. A futásidejű környezetek készletezett, de tisztítani a környezet után minden egyes futtatásához. Ezért ezek garantáltan biztonságos az oldal nem várt hatások egymástól.

### <a name="pre-compilation"></a>A fordítás előtti
Tárolt eljárások, eseményindítók és felhasználó által megadott függvények minden parancsfájl hívás idején fordítási költségek elkerülése érdekében olyan implicit módon lefordított bájt kód formátumba. Ez biztosítja a tárolt eljárások indítások gyors, és egy kis erőforrásigényét.

## <a name="client-sdk-support"></a>Ügyfél SDK-támogatás
A DocumentDB API-t a mellett [Node.js](documentdb-sdk-node.md) ügyfél, Azure Cosmos DB rendelkezik [.NET](documentdb-sdk-dotnet.md), [.NET Core](documentdb-sdk-dotnet-core.md), [Java](documentdb-sdk-java.md), [ JavaScript](http://azure.github.io/azure-documentdb-js/), és [Python SDK-k](documentdb-sdk-python.md) a documentdb API. Tárolt eljárások, eseményindítók és felhasználó által megadott függvények hozhatók létre, és végre bármely, valamint a SDK használatával. A következő példa bemutatja, hogyan hozhat létre, és a .NET-ügyfélprogrammal tárolt eljárás végrehajtása. Megjegyzés: a .NET-típusok lett átadva a következő tárolt eljárás JSON-ként és olvasási vissza hogyan.

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


Ez a példa bemutatja, hogyan használható a [DocumentDB .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) előtti eseményindító létrehozása, és hozzon létre egy dokumentumot az eseményindító engedélyezve van. 

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


A következő példa bemutatja, hogyan hozzon létre egy felhasználói függvény (UDF), és ezért és egy [DocumentDB API SQL-lekérdezés](documentdb-sql-query.md).

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

## <a name="rest-api"></a>REST API
Minden Azure Cosmos DB műveletet RESTful módon végezheti el. Tárolt eljárások, eseményindítók és felhasználó által definiált függvények regisztrálhatók egy gyűjteményt a HTTP POST használatával. A következő egy példa bemutatja, hogyan regisztrálja a tárolt eljárás:

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


A tárolt eljárás regisztrálva van egy POST kérést az URI adatbázisok/testdb/colls/testColl/sprocs szemben a szervezethez tartalmazó létrehozni a tárolt eljárás végrehajtásával. Eseményindítók és felhasználó által megadott függvények hasonlóan a FELADÁS egy vagy több/eseményindítók és /udfs rendre kiállításával regisztrálható.
Ez tárolt eljárást is, majd a POST kérelmet az erőforrás-hivatkozás kiállításával hajtható végre:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Itt a tárolt eljárás bemeneti lett átadva a kérés törzsében. Vegye figyelembe, hogy a bemeneti JSON tömb a bemeneti paraméterek átadása. A következő tárolt eljárást az első bemenetből fogad adatokat, amely egy adott válasz törzsének-dokumentumként. A érkező válasz a következőképpen történik:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Ellentétben a tárolt eljárások, eseményindítók közvetlenül nem hajtható végre. Ehelyett végrehajtás egy dokumentumot egy művelet részeként. A kérelem HTTP-fejlécek használatával futtatásához eseményindítók adható meg. A dokumentum létrehozására vonatkozó kérelem a következő:

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


A kérelem futtatásához előtti eseményindító Itt a x-ms-documentdb-pre-trigger-include fejlécben megadott. Ennek megfelelően a utáni eseményindítókat kap a x-ms-documentdb-post-trigger-include fejlécben. Vegye figyelembe, hogy mindkét előtti és utáni eseményindítók adható meg egy adott kérés esetében.

## <a name="sample-code"></a>Mintakód
További példákat kiszolgálóoldali található (beleértve a [tömeges törlési](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js), és [frissítése](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) a a [GitHub-tárházban](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Meg szeretné osztani a Soft tárolt eljárás? Kérjük küldjön egy lekérést! 

## <a name="next-steps"></a>Következő lépések
Miután egy vagy több tárolt eljárások, eseményindítók és felhasználó által definiált függvények létrehozott, betöltve helyezheti, és megtekintheti az Azure-portálon az Intéző segítségével.

Előfordulhat, hogy is megtalálhatja a következő hivatkozások és erőforrások hasznos az elérési úthoz Azure Cosmos dB kiszolgálóoldali programozása tájékozódhat:

* [Az Azure Cosmos DB SDK-k](documentdb-sdk-dotnet.md)
* [A DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Biztonságos és hordozható adatbázis bővíthetőség](http://dl.acm.org/citation.cfm?id=276339) 
* [Szolgáltatás készült adatbázis-architektúra](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [A .NET-futtatókörnyezet, a Microsoft SQL server üzemeltetéséhez](http://dl.acm.org/citation.cfm?id=1007669)

