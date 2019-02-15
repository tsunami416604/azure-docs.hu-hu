---
title: Az Azure Cosmos DB SQL API a node.js-oktatóanyag
description: Node.js oktatóanyag, amely az Azure Cosmos DB SQL API-val való csatlakoztatását és lekérdezését írja le
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 4441797eb41dc909a98be3c42931140e71e36f80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270351"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Oktatóanyag: Node.js-Konzolalkalmazás létrehozása az Azure Cosmos DB SQL API-adatok kezelése a JavaScript SDK-val

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (előzetes verzió)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [A .NET core (előzetes verzió)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

A fejlesztők lehetséges, hogy NoSQL dokumentum-adatokat használó alkalmazások. Segítségével egy SQL API-fiókot az Azure Cosmos DB-ben Ez a dokumentum adatok tárolása és elérése. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node.js-konzolalkalmazást hozhat létre Azure Cosmos DB-erőforrásokat, és kérdezheti le őket.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre, és csatlakozzon az Azure Cosmos DB-fiók.
> * Állítsa be az alkalmazást.
> * Hozzon létre egy adatbázist.
> * Hozzon létre egy tárolót.
> * Elemek hozzáadása a tárolóhoz.
> * Az elemek, a tároló és az adatbázis az alapszintű műveletek végrehajtása.

## <a name="prerequisites"></a>Előfeltételek 

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NODE.js](https://nodejs.org/) v6.0.0 vagy újabb verziója.

## <a name="create-azure-cosmos-db-account"></a>Az Azure Cosmos DB-fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A Node.js-alkalmazás beállítása](#SetupNode) című lépéssel. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon előre [A Node.js-alkalmazás beállítása](#SetupNode) című lépésre. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>A Node.js-alkalmazás beállítása

Az alkalmazás kódírás megkezdése előtt a keretrendszer hozhat létre az alkalmazáshoz. Futtassa az alábbi lépéseket a Node.js-alkalmazás, amely rendelkezik a framework-kód beállítása:

1. Nyissa meg kedvenc terminálját.
2. Keresse meg azt a mappát vagy könyvtárat, ahova a Node.js-alkalmazást menteni szeretné.
3. Hozzon létre két üres JavaScript-fájlt az alábbi parancsokkal:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Létrehozni és inicializálni egy `package.json` fájlt. Használja az alábbi parancsot:
   * ```npm init -y```

5. Telepítse az @azure/cosmos modult az npm segítségével. Használja az alábbi parancsot:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Az alkalmazás konfigurációnak megadása

Most, hogy az alkalmazás már létezik, győződjön meg arról, hogy kommunikálni tudjon az Azure Cosmos DB szeretne. Néhány konfigurációs beállítások módosításával, ahogyan az alábbi lépésekkel, állíthatja be az alkalmazást, hogy kommunikáljon az Azure Cosmos DB:

1. Nyissa meg a ```config.js``` fájlt egy tetszőleges szövegszerkesztőben.

1. Másolja és illessze be az alábbi kódrészletet, majd állítsa be a ```config.endpoint``` és ```config.primaryKey``` tulajdonságot a saját Azure Cosmos DB-végpontjának URI- és elsődleges kulcs értékeire. Mindkettő konfiguráció megtalálható az [Azure Portalon](https://portal.azure.com).

   ![Kulcsok lekérése az Azure Portalról képernyőkép][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Másolja és illessze be a ```database```, ```container``` és ```items``` adatokat az alábbi ```config```-objektumba oda, ahol megadta a ```config.endpoint``` és ```config.primaryKey``` tulajdonságot. Ha már rendelkezik adat, amelyet szeretne az adatbázisban tárolni, használhatja a meghatározásához, itt az adatok helyett az Azure Cosmos DB az adatok áttelepítési eszköz.

   ```javascript
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   A JavaScript SDK-t használ az általános feltételek *tároló* és *elem*. A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. 

1. Végül exportálja a ```config```-objektumot, hogy hivatkozhasson rá az ```app.js``` fájlban.

   ```javascript
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Csatlakozás az Azure Cosmos DB-fiók

1. Nyissa meg az üres ```app.js``` fájlt a szövegszerkesztőben. Másolja és illessze be az alábbi kódot a ```@azure/cosmos```, valamint az újonnan létrehozott ```config``` modul importálásához.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Másolja és illessze be a kódot, ha az előzőleg mentett ```config.endpoint``` és ```config.primaryKey``` használatával szeretne létrehozni egy új CosmosClient-ügyfelet.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> Ha csatlakozik a **Cosmos DB Emulator**, tiltsa le az SSL-ellenőrzést kapcsolat egyéni szabályzat létrehozásával.
>   ```
>   const connectionPolicy = new cosmos.ConnectionPolicy ()
>   connectionPolicy.DisableSSLVerification = true
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Most, hogy rendelkezik az Azure Cosmos DB-ügyfél elindításához szükséges kóddal, vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.

## <a name="create-a-database"></a>Adatbázis létrehozása

1. Másolja és illessze be az alábbi kód megadásával beállíthatja az adatbázis-azonosító és a tároló azonosítója. Azonosítóit a részletekben, hogyan az Azure Cosmos DB-ügyfél megtalálja a megfelelő adatbázist és a tároló.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   Egy adatbázis használatával hozható létre a `createIfNotExists` , vagy hozzon létre funkcióját a **adatbázisok** osztály. Az adatbázis a tárolók között particionált elemek logikai tárolója. 

2. Másolja és illessze be a **createDatabase** és a **readDatabase** metódusokat a ```databaseId``` és a ```containerId``` definíciója alá az app.js fájlban. Ha még nem létezne, a **createDatabase** függvény létrehoz egy új adatbázist a ```FamilyDatabase``` azonosítóval, amelyet a ```config``` objektum határoz meg. A **readDatabase** függvény beolvassa az adatbázis definícióját, hogy ellenőrizze, hogy az adatbázis létezik-e.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Másolja és illessze be az alábbi kódot oda, ahol megadta a **createDatabase** és **readDatabase** függvényeket. Ezzel hozzáadhatja az **exit** (kilépés) segédfüggvényt, amely megjeleníti a kilépési üzenetet. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Másolja és illessze be az alábbi kódot oda, ahol megadta az **exit** függvényt a **createDatabase** és a **readDatabase** függvények meghívására.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   Ekkor az ```app.js``` fájlban a kódnak úgy kell kinéznie, mint az alábbi kód:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Tároló létrehozása

Ezután hozzon létre egy tárolót az Azure Cosmos DB-fiókon belül, így a tárolhatja, és lekérdezheti az adatokat. 

> [!WARNING]
Tároló létrehozása – következmények díjszabás rendelkezik. Látogasson el a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) , hogy tudja, hogy milyen hatással vannak.

Egy tároló használatával hozható létre a `createIfNotExists` , vagy hozzon létre a függvénynek a **tárolók** osztály.  A tároló elemeket (az SQL API esetében JSON-dokumentumokat) tartalmaz, valamint a kapcsolódó JavaScript-alkalmazáslogikát.

1. Másolja és illessze be a **createContainer** és a **readContainer** függvényt a **readDatabase** függvény alá az app.js fájlban. Ha még nem létezne, a **createContainer** függvény létrehoz egy új tárolót a ```containerId``` azonosítóval, amelyet a ```config``` objektum határoz meg. A **readContainer** függvény beolvassa a tároló definícióját, hogy ellenőrizze, hogy a tároló létezik-e.

   ```javascript
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Másolja és illessze be az alábbi kódot a **readDatabase** alá a **createContainer** és a **readContainer** függvény végrehajtásához.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   Ekkor az ```app.js``` fájlban a kódnak így kell kinéznie:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Elem létrehozása

Egy elem a létrehozás funkciójának használatával hozható létre a **elemek** osztály. Az SQL API-t használja, amikor elemek leképezi a rendszer, amely felhasználó által megadott (tetszőleges) JSON tartalmak-dokumentumok formájában. Most már beszúrhat egy elemet az Azure Cosmos DB-be.

1. Másolja és illessze be a **createFamilyItem** függvényt a **readContainer** függvény alá. A **createFamilyItem** függvény hozza létre a ```config``` objektumban mentett JSON-adatokat tartalmazó elemeket. Az egyes elemek létrehozása előtt a rendszer ellenőrzi, hogy létezik-e már elem ugyanazzal az azonosítóval.

   ```javascript
   /**
   * Create family item if it does not exist
   */
  async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. Másolja és illessze be az alábbi kódot a **readContainer** függvény meghívása alá a **createFamilyItem** függvény végrehajtásához.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. Az alábbi mintakód egy olyan lekérdezést mutat be, amelyet a tárolóban található dokumentumokra vonatkozóan futtathat le.

1. Másolja és illessze be a **queryContainer** függvényt a **createFamilyItem** függvény alá az app.js fájlban. Az Azure Cosmos DB támogatja az SQL-szerű lekérdezéseket, ahogyan azt az alábbi példa is mutatja.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Másolja és illessze be az alábbi kódot a **createFamilyItem** függvény meghívásai alá a **queryContainer** függvény végrehajtásához.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Cserélje le egy elem
Az Azure Cosmos DB támogatja az elemek tartalmának cseréjét.

1. Másolja és illessze be a **replaceFamilyItem** függvényt a **queryContainer** függvény alá az app.js fájlban. Vegye figyelembe, hogy a gyermekek „szint” tulajdonságát 6-os értékre módosítottuk a korábbi 5-ös értékről.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. Másolja és illessze be az alábbi kódot a **queryContainer** függvény meghívása alá a **replaceFamilyItem** függvény végrehajtásához. Továbbá adja hozzá a **queryContainer** függvényt meghívó kódot annak megerősítése érdekében, hogy az elem módosítása sikeres volt.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Egy elem törlése

Az Azure Cosmos DB támogatja a JSON-elemek törlését.

1. Másolja és illessze be a **deleteFamilyItem** függvényt a **replaceFamilyItem** függvény alá.

   ```javascript
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Másolja és illessze be az alábbi kódot a második **queryContainer** függvény meghívása alá a **deleteFamilyItem** függvény végrehajtásához.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Az adatbázis törlése

A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (tárolók, elemek stb.) is törlődik.

1. Másolja és illessze be a **cleanup** függvényt a **deleteFamilyItem** függvény alá az adatbázis, valamint annak minden gyermekerőforrása törléséhez.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Másolja és illessze be az alábbi kódot a **deleteFamilyItem** függvény meghívása alá a **cleanup** függvény végrehajtásához.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>A Node.js-alkalmazás futtatása

Összességében a kódnak így kell kinéznie:

```javascript
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

```bash 
node app.js
```

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenetnek meg kell egyeznie az alábbi példaszöveggel.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>A Node. js-oktatóanyagban szereplő teljes megoldás beszerzése 

Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a kódot, a [GitHubon](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) beszerezheti azt. 

A kezdeti lépéseket ismertető elindítva megoldás, amely tartalmazza az ebben a cikkben minden a kód futtatásához szüksége lesz: 

* Egy [Azure Cosmos DB-fiók][create-account]. 
* A GitHubon elérhető [Kezdeti lépések](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) megoldás. 

A projekt függőségeihez az npm telepítése. Használja az alábbi parancsot: 

* ```npm install``` 

Ezután a ```config.js``` fájlt, frissítse a config.endpoint és config.primaryKey értékek leírtak szerint [3. lépés: Az alkalmazás konfigurációnak megadása](#Config).  

Majd a terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség ezeket az erőforrásokat, törölheti az erőforráscsoportot, az Azure Cosmos DB-fiók és minden kapcsolódó erőforrás. Ehhez válassza ki az erőforráscsoportot, amelyet az Azure Cosmos DB-fiók, jelölje be a használt **törlése**, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Cosmos DB-fiók figyelése](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
