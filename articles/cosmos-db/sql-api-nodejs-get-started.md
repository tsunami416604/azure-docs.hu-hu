---
title: Node. js-oktatóanyag a Azure Cosmos DB SQL API-hoz
description: Node.js oktatóanyag, amely az Azure Cosmos DB SQL API-val való csatlakoztatását és lekérdezését írja le
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: d30016381740c6e1a881ba8fcdaa6a6a719e6275
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855653"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Oktatóanyag: Node. js-konzol-alkalmazás létrehozása a JavaScript SDK-val Azure Cosmos DB SQL API-adat kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Fejlesztőként rendelkezhet olyan alkalmazásokkal, amelyek NoSQL-dokumentumokat használnak. A dokumentumok tárolására és elérésére a Azure Cosmos DBban SQL API-fiók használható. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node. js-konzol alkalmazást Azure Cosmos DB erőforrások létrehozásához és lekérdezéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre és kapcsolódjon egy Azure Cosmos DB-fiókhoz.
> * Az alkalmazás beállítása.
> * Hozzon létre egy adatbázist.
> * Hozzon létre egy tárolót.
> * Elemek hozzáadása a tárolóhoz.
> * Alapszintű műveleteket hajthat végre az elemeken, a tárolón és az adatbázison.

## <a name="prerequisites"></a>Előfeltételek 

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node. js](https://nodejs.org/) v 6.0.0 vagy újabb.

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A Node.js-alkalmazás beállítása](#SetupNode) című lépéssel. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon előre [A Node.js-alkalmazás beállítása](#SetupNode) című lépésre. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>A Node. js-alkalmazás beállítása

Mielőtt elkezdi a kód írását az alkalmazás létrehozásához, létrehozhatja az alkalmazás keretrendszerét. A következő lépésekkel állíthatja be a Node. js-alkalmazást, amely a keretrendszer kódjával rendelkezik:

1. Nyissa meg kedvenc terminálját.
2. Keresse meg azt a mappát vagy könyvtárat, ahova a Node.js-alkalmazást menteni szeretné.
3. Hozzon létre két üres JavaScript-fájlt az alábbi parancsokkal:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Hozzon létre és inicializáljon egy `package.json` fájlt. Használja az alábbi parancsot:
   * ```npm init -y```

5. Telepítse az @azure/cosmos modult az npm segítségével. Használja az alábbi parancsot:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Az alkalmazás konfigurációjának beállítása

Most, hogy az alkalmazás már létezik, meg kell győződnie arról, hogy tud beszélni Azure Cosmos DB. Néhány konfigurációs beállítás frissítésével az alábbi lépésekben látható módon beállíthatja, hogy az alkalmazás beszéljen a Azure Cosmos DB:

1. Nyissa meg a ```config.js``` fájlt egy tetszőleges szövegszerkesztőben.

1. Másolja és illessze be az alábbi kódrészletet, majd állítsa be a ```config.endpoint``` és ```config.key``` tulajdonságot a saját Azure Cosmos DB-végpontjának URI- és elsődleges kulcs értékeire. Mindkettő konfiguráció megtalálható az [Azure Portalon](https://portal.azure.com).

   ![Kulcsok lekérése az Azure Portalról képernyőkép][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.key = "~your primary key here~";
   ``` 

1. Másolja és illessze be a ```database```, ```container``` és ```items``` adatokat az alábbi ```config```-objektumba oda, ahol megadta a ```config.endpoint``` és ```config.key``` tulajdonságot. Ha már rendelkezik olyan adattal, amelyet szeretne az adatbázisban tárolni, a Azure Cosmos DB adatáttelepítési eszközét az itt megadott módon nem definiálhatja. A config. js fájlnak a következő kóddal kell rendelkeznie:

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   A JavaScript SDK az általános feltételek *tárolóját* és az *elemeket*használja. A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. 
   
   `module.exports = config;`a kód az ```config``` objektum exportálására szolgál, hogy a ```app.js``` fájlon belül hivatkozhat rá.

## <a id="Connect"></a>Kapcsolódás Azure Cosmos DB fiókhoz

1. Nyissa meg az üres ```app.js``` fájlt a szövegszerkesztőben. Másolja és illessze be az alábbi kódot a ```@azure/cosmos```, valamint az újonnan létrehozott ```config``` modul importálásához.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Másolja és illessze be a kódot, ha az előzőleg mentett ```config.endpoint``` és ```config.key``` használatával szeretne létrehozni egy új CosmosClient-ügyfelet.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });
   ```
   
> [!Note]
> Ha csatlakozik a **Cosmos db emulátorhoz**, tiltsa le az SSL-ellenőrzést a csomóponti folyamathoz:
>   ```
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Most, hogy rendelkezik az Azure Cosmos DB-ügyfél elindításához szükséges kóddal, vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.

## <a name="create-a-database"></a>Adatbázis létrehozása

1. Másolja és illessze be az alábbi kódot az adatbázis-azonosító és a tároló AZONOSÍTÓjának megadásához. Ezek az azonosítók azt ismertetik, hogy a Azure Cosmos DB ügyfél hol találja a megfelelő adatbázist és tárolót.

   ```javascript
   const client = new CosmosClient({ endpoint, key } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   Egy adatbázis az `createIfNotExists` **adatbázisok** osztály vagy a Create függvény használatával hozható létre. Az adatbázis a tárolók között particionált elemek logikai tárolója. 

2. Másolja és illessze be a **createDatabase** és a **readDatabase** metódusokat a ```databaseId``` és a ```containerId``` definíciója alá az app.js fájlban. A **createDatabase** függvény létrehoz egy új, azonosítóval ```FamilyDatabase```rendelkező adatbázist, amely az ```config``` objektumból van megadva, ha még nem létezik. A **readDatabase** függvény beolvassa az adatbázis definícióját, hogy ellenőrizze, hogy az adatbázis létezik-e.

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
      const { resource: databaseDefinition } = await client.database(databaseId).read();
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
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

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
     const { resource: databaseDefinition } = await client.database(databaseId).read();
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

Ezután hozzon létre egy tárolót a Azure Cosmos DB fiókon belül, hogy tárolja és lekérdezheti az adattárat. 

> [!WARNING]
> A tárolók létrehozása díjszabási következményekkel jár. Látogasson el az [árképzési oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/) , és tudja, mire számíthat.

A tárolók a **tárolók** osztály vagy a `createIfNotExists` Create függvény használatával hozhatók létre.  A tároló elemeket (az SQL API esetében JSON-dokumentumokat) tartalmaz, valamint a kapcsolódó JavaScript-alkalmazáslogikát.

1. Másolja és illessze be a **createContainer** és a **readContainer** függvényt a **readDatabase** függvény alá az app.js fájlban. Ha még nem létezne, a **createContainer** függvény létrehoz egy új tárolót a ```containerId``` azonosítóval, amelyet a ```config``` objektum határoz meg. A **readContainer** függvény beolvassa a tároló definícióját, hogy ellenőrizze, hogy a tároló létezik-e.

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
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
   const key = config.key;

   const client = new CosmosClient({ endpoint, key });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

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

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { resource: containerDefinition } = await client.database(databaseId).container(containerId).read();
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

## <a id="CreateItem"></a>Elemek létrehozása

Egy elem az Items osztály Create függvényének használatával hozható létre . Az SQL API használatakor az elemek dokumentumokként vannak kiképezve, amelyek felhasználó által definiált (tetszőleges) JSON-tartalmak. Most már beszúrhat egy elemet az Azure Cosmos DB-be.

1. Másolja és illessze be a **createFamilyItem** függvényt a **readContainer** függvény alá. A **createFamilyItem** függvény hozza létre a ```config``` objektumban mentett JSON-adatokat tartalmazó elemeket. Ellenőrizzük, hogy a létrehozás előtt nem létezik-e már ugyanazzal az AZONOSÍTÓval rendelkező tétel.

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
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

Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-dokumentumokon végzett részletes lekérdezéseket. Az alábbi mintakód egy olyan lekérdezést mutat be, amelyet a tárolóban található dokumentumokra vonatkozóan futtathat le.

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

    const { resources } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).fetchAll();
    for (var queryResult of resources) {
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


## <a id="ReplaceItem"></a>Egy tétel cseréje
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
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
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


## <a id="DeleteItem"></a>Elemek törlése

Az Azure Cosmos DB támogatja a JSON-elemek törlését.

1. Másolja és illessze be a **deleteFamilyItem** függvényt a **replaceFamilyItem** függvény alá.

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
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

## <a id="Run"></a>A Node. js-alkalmazás futtatása

Összességében a kódnak így kell kinéznie:

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

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

A cikkben szereplő összes kódot tartalmazó első lépések megoldás futtatásához a következőkre lesz szüksége: 

* Egy [Azure Cosmos db-fiók][create-account]. 
* A GitHubon elérhető [Kezdeti lépések](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) megoldás. 

Telepítse a projekt függőségeit a NPM-on keresztül. Használja az alábbi parancsot: 

* ```npm install``` 

Ezután a ```config.js``` fájlban frissítse a config. Endpoint és a config. PrimaryKey értékeket a 3. [lépésben leírtak szerint. Adja meg az alkalmazás konfigurációit](#Config).  

Majd a terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szükség, törölheti az erőforráscsoportot, Azure Cosmos DB fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki a Azure Cosmos DB fiókhoz használt erőforráscsoportot, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Cosmos DB fiók figyelése](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
