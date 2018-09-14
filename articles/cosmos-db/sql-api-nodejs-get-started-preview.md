---
title: Node.js-oktatóanyag az Azure Cosmos DB-hez készült SQL API-hoz | Microsoft Docs
description: Node.js oktatóanyag, amely az Azure Cosmos DB SQL API-val való csatlakoztatását és lekérdezését írja le
keywords: node.js-oktatóanyag, node-adatbázis
services: cosmos-db
author: deborahc
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: bb7c5c48a137c559cd0276de69cc2964bb6ed574
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698230"
---
# <a name="nodejs-tutorial-create-a-nodejs-console-application-with-azure-cosmos-db-sql-api-and-javascript-sdk-20-preview"></a>Node.js-oktatóanyag: Node.js-konzolalkalmazás létrehozása az Azure Cosmos DB SQL API-jával és a JavaScript SDK 2.0 (előzetes) verzióval

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Aszinkron Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Node.js – v2](sql-api-nodejs-get-started-preview.md) 
> 


Üdvözöljük az Azure Cosmos DB JavaScript SDK-hoz készült Node.js-oktatóanyagban! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazást készít, amely Azure Cosmos DB-erőforrásokat hoz létre és kérdez le.

Az oktatóanyag a következőket ismerteti:

* Azure Cosmos DB-fiók létrehozása és csatlakoztatása
* Az alkalmazás beállítása
* Adatbázis létrehozása
* Tároló létrehozása
* JSON-elemek hozzáadása a tárolóhoz
* A tároló lekérdezése
* Elemek cseréje
* Elemek törlése
* Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). Gyors útmutatásért tekintse meg [A teljes megoldás beszerzése](#GetSolution) című szakaszt.

A Node.js-oktatóanyag befejezése után a lap tetején vagy alján található szavazógomb használatával küldhet visszajelzést. Ha szeretne közvetlenül kapcsolatba lépni velünk, a hozzászólásaiban tüntesse fel az e-mail-címét.

Most pedig lássunk neki!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>A Node.js-oktatóanyag előfeltételei

Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* A [Node.js](https://nodejs.org/) 6.0.0-s vagy újabb verziója.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1. lépés: Azure Cosmos DB-fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A Node.js-alkalmazás beállítása](#SetupNode) című lépéssel. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon előre [A Node.js-alkalmazás beállítása](#SetupNode) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>2. lépés: A Node.js-alkalmazás beállítása

1. Nyissa meg kedvenc terminálját.
2. Keresse meg azt a mappát vagy könyvtárat, ahova a Node.js-alkalmazást menteni szeretné.
3. Hozzon létre két üres JavaScript-fájlt az alábbi parancsokkal:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Telepítse a DocumentDB modult az npm segítségével Használja az alábbi parancsot:
   * ```npm install @azure/cosmos --save```

Remek! A beállítás befejeztével nekiláthat a kód írásának.

## <a id="Config"></a>3. lépés: Az alkalmazás konfigurációnak megadása

Nyissa meg a ```config.js``` fájlt egy tetszőleges szövegszerkesztőben.

Ezután másolja és illessze be az alábbi kódrészletet, majd állítsa be a ```config.endpoint``` és ```config.primaryKey``` tulajdonságot a saját Azure Cosmos DB-végpontjának URI és elsődleges kulcs értékeire. Mindkettő konfiguráció megtalálható az [Azure Portalon](https://portal.azure.com).

![Node.js-oktatóanyag – Képernyőfelvétel az Azure Portalról, amely egy Azure Cosmos DB-fiókot jelenít meg az ACTIVE (AKTÍV) központ, az Azure Cosmos DB-fiók panelén lévő KEYS (KULCSOK) gomb, valamint a Kulcsok panelen lévő URI, PRIMARY KEY (ELSŐDLEGES KULCS) és SECONDARY KEY (MÁSODLAGOS KULCS) értékek kiemelésével – Node-adatbázis][keys]

```nodejs
// ADD THIS PART TO YOUR CODE
var config = {}

config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
config.primaryKey = "~your primary key here~";
``` 

Másolja és illessze be a ```database```, ```container``` és ```items``` adatokat az alábbi ```config```-objektumba oda, ahol megadta a ```config.endpoint``` és ```config.primaryKey``` tulajdonságot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, használhatja az Azure Cosmos DB [adatmigrálási eszközét](import-data.md) is ahelyett, hogy itt definiálná az adatokat.

```nodejs
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
Megjegyzés: Ha már ismeri a JavaScript SDK korábbi verzióját, ismerősek lehetnek a „gyűjtemény” és a „dokumentum” kifejezések. Mivel az Azure Cosmos DB [több API-modellt](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities) támogat, a JavaScript SDK 2.0-s vagy újabb verziói az általános „tároló” és „elem” kifejezéseket használják. A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. 

Végül exportálja a ```config```-objektumot, hogy hivatkozhasson rá az ```app.js``` fájlban.
```nodejs
        },
        "isRegistered": false
    }
};

// ADD THIS PART TO YOUR CODE
module.exports = config;
```
## <a id="Connect"></a> 4. lépés: Csatlakozás Azure Cosmos DB-fiókhoz

Nyissa meg az üres ```app.js``` fájlt a szövegszerkesztőben. Másolja és illessze be az alábbi kódot a ```@azure/cosmos```, valamint az újonnan létrehozott ```config``` modul importálásához.

```nodejs
// ADD THIS PART TO YOUR CODE
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');
```

Másolja és illessze be a kódot, ha az előzőleg mentett ```config.endpoint``` és ```config.primaryKey``` használatával szeretne létrehozni egy új CosmosClient-ügyfelet.

```nodejs
const url = require('url');

// ADD THIS PART TO YOUR CODE
const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
```

Most, hogy rendelkezik az Azure Cosmos DB-ügyfél elindításához szükséges kóddal, vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.

## <a name="step-5-create-a-database"></a>5. lépés: Adatbázis létrehozása

Másolja és illessze be az alábbi kódot a Not Found (Nem található) HTTP-állapot, az adatbázis azonosítója, valamint a tároló azonosítója megadásához. Az Azure Cosmos DB-ügyfél ezen azonosítók alapján találja meg a megfelelő adatbázist és tárolót.

```nodejs
const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

// ADD THIS PART TO YOUR CODE
const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;
```

Az [adatbázis](sql-api-resources.md#databases) a **Databases** osztály [createIfNotExists](/javascript/api/%40azure/cosmos/databases) vagy [create](/javascript/api/%40azure/cosmos/databases) függvényének használatával hozható létre. Az adatbázis a tárolók között particionált elemek logikai tárolója. 

Másolja és illessze be a **createDatabase** és a **readDatabase** függvényeket a ```databaseId``` és a ```containerId``` definíciója alá az app.js fájlban. Ha még nem létezne, a **createDatabase** függvény létrehoz egy új adatbázist a ```FamilyDatabase``` azonosítóval, amelyet a ```config``` objektum határoz meg. A **readDatabase** függvény beolvassa az adatbázis definícióját, hogy ellenőrizze, hogy az adatbázis létezik-e.

```nodejs
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

Másolja és illessze be az alábbi kódot oda, ahol megadta a **createDatabase** és **readDatabase** függvényeket. Ezzel hozzáadhatja az **exit** (kilépés) segédfüggvényt, amely megjeleníti a kilépési üzenetet. 

```nodejs
// ADD THIS PART TO YOUR CODE
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
};
```
Másolja és illessze be az alábbi kódot oda, ahol megadta az **exit** függvényt a **createDatabase** és a **readDatabase** függvények meghívására.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
```

Ekkor az ```app.js``` fájlban a kódnak így kell kinéznie:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

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
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 
```bash 
node app.js
```

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-adatbázist.

## <a id="CreateContainer"></a>6. lépés: Tároló létrehozása

> [!WARNING]
> A **createContainer** függvény meghívása létrehoz egy új tárolót, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/).

A tároló a **Containers** osztály [createIfNotExists](/javascript/api/%40azure/cosmos/containers) vagy [create](/javascript/api/%40azure/cosmos/containers) függvényének használatával hozható létre. 

A tároló elemeket (az SQL API esetében JSON-dokumentumokat) tartalmaz, valamint a kapcsolódó JavaScript-alkalmazáslogikát.

Másolja és illessze be a **createContainer** és a **readContainer** függvényt a **readDatabase** függvény alá az app.js fájlban. Ha még nem létezne, a **createContainer** függvény létrehoz egy új tárolót a ```containerId``` azonosítóval, amelyet a ```config``` objektum határoz meg. A **readContainer** függvény beolvassa a tároló definícióját, hogy ellenőrizze, hogy a tároló létezik-e.

```nodejs
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

Másolja és illessze be az alábbi kódot a **readDatabase** alá a **createContainer** és a **readContainer** függvény végrehajtásához.
```nodejs
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

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

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

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

```bash 
node app.js
```

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-tárolót.

## <a id="CreateItem"></a>7. lépés: Elem létrehozása

Elemet az **Items** osztály [create](/javascript/api/%40azure/cosmos/items) függvényével hozhat létre. Az SQL API használatával az elemek dokumentumokként vannak kivetítve, amelyek felhasználói (tetszőleges) JSON-tartalmak. Most már beszúrhat egy elemet az Azure Cosmos DB-be.

Másolja és illessze be a **createFamilyItem** függvényt a **readContainer** függvény alá. A **createFamilyItem** függvény hozza létre a ```config``` objektumban mentett JSON-adatokat tartalmazó elemeket. Az egyes elemek létrehozása előtt a rendszer ellenőrzi, hogy létezik-e már elem ugyanazzal az azonosítóval.

```nodejs
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

Másolja és illessze be az alábbi kódot a **readContainer** függvény meghívása alá a **createFamilyItem** függvény végrehajtásához.
```nodejs
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

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

```bash 
node app.js
```

Gratulálunk! Sikeresen létrehozott egy Azure Cosmos DB-elemet.


## <a id="Query"></a>8. lépés: Az Azure Cosmos DB-erőforrások lekérdezése
Az Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](sql-api-sql-query.md). Az alábbi mintakód egy olyan lekérdezést mutat be, amelyet a tárolóban található dokumentumokra vonatkozóan futtathat le.

Másolja és illessze be a **queryContainer** függvényt a **createFamilyItem** függvény alá az app.js fájlban. Az Azure Cosmos DB támogatja az SQL-szerű lekérdezéseket, ahogyan azt az alábbi példa is mutatja. A bonyolult lekérdezések felépítésével kapcsolatos további információkért tekintse meg a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) szakaszt, valamint a [lekérdezésekre vonatkozó dokumentációt](sql-api-sql-query.md).

```nodejs
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

Másolja és illessze be az alábbi kódot a **createFamilyItem** függvény meghívásai alá a **queryContainer** függvény végrehajtásához.

```nodejs
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

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:

```bash 
node app.js
```

Gratulálunk! Sikeresen lekérdezte az Azure Cosmos DB-elemeket.

## <a id="ReplaceItem"></a>9. lépés: Elemek cseréje
Az Azure Cosmos DB támogatja az elemek tartalmának cseréjét.

Másolja és illessze be a **replaceFamilyItem** függvényt a **queryContainer** függvény alá az app.js fájlban. Vegye figyelembe, hogy a gyermekek „szint” tulajdonságát 6-os értékre módosítottuk a korábbi 5-ös értékről.

```nodejs
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
Másolja és illessze be az alábbi kódot a **queryContainer** függvény meghívása alá a **replaceFamilyItem** függvény végrehajtásához. Továbbá adja hozzá a **queryContainer** függvényt meghívó kódot annak megerősítése érdekében, hogy az elem módosítása sikeres volt.

```nodejs
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
A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:

```bash 
node app.js
```

Gratulálunk! Sikeresen lecserélt egy Azure Cosmos DB-elemet.

## <a id="DeleteItem"></a>10. lépés: Elemek törlése

Az Azure Cosmos DB támogatja a JSON-elemek törlését.

Másolja és illessze be a **deleteFamilyItem** függvényt a **replaceFamilyItem** függvény alá.

```nodejs
/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};
```

Másolja és illessze be az alábbi kódot a második **queryContainer** függvény meghívása alá a **deleteFamilyItem** függvény végrehajtásához.

```nodejs
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

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

```bash 
node app.js
```

Gratulálunk! Sikeresen törölt egy Azure Cosmos DB-elemet.

## <a id="DeleteDatabase"></a>11. lépés: Az adatbázis törlése

A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (tárolók, elemek stb.) is törlődik.

Másolja és illessze be a **cleanup** függvényt a **deleteFamilyItem** függvény alá az adatbázis, valamint annak minden gyermekerőforrása törléséhez.

```nodejs
/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}
```

Másolja és illessze be az alábbi kódot a **deleteFamilyItem** függvény meghívása alá a **cleanup** függvény végrehajtásához.

```nodejs
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

## <a id="Run"></a>12. lépés: A teljes Node.js-alkalmazás futtatása

Összességében a kódnak így kell kinéznie:
```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

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

Gratulálunk! Ezzel befejezte a Node.js-oktatóanyagot, és létrehozta első saját Azure Cosmos DB-konzolalkalmazását!

## <a id="GetSolution"></a>A Node. js-oktatóanyagban szereplő teljes megoldás beszerzése

Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a kódot, a [GitHubon](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) beszerezheti azt.

A cikkben szereplő teljes kódot tartalmazó kezdő megoldás futtatásához az alábbiakra lesz szüksége:

* Egy [Azure Cosmos DB-fiók][create-account].
* A GitHubon elérhető [Kezdeti lépések](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) megoldás.

Telepítse a **@azure/cosmos** modult az npm segítségével. Használja az alábbi parancsot:

* ```npm install @azure/cosmos --save```

Ezután a ```config.js``` fájlban frissítse a config.endpoint és config.primaryKey értékeket a [3. lépés: Az alkalmazás konfigurációnak megadásában](#Config) leírtak alapján. 

Majd a terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: 

```bash 
node app.js
```

Ennyi az egész, és máris jó úton jár! 

## <a name="next-steps"></a>További lépések
* Összetettebb Node.js-mintát szeretne használni? Lásd: [Node.js-webalkalmazás létrehozása az Azure Cosmos DB használatával](sql-api-nodejs-application-preview.md).
* Ismerje meg, hogyan [figyelhet egy Azure Cosmos DB-fiókot](monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png