---
title: "Csatlakozás a PostgreSQL-hez készült Azure Database-hez a Node.js segítségével | Microsoft Docs"
description: "Ebben a rövid útmutatóban biztosítjuk a PostgreSQL-hez készült Azure Database-ről csatlakozásra és adatlekérdezésre használható Node.js-kódmintát."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: hu-hu
ms.lasthandoff: 06/26/2017

---

<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# A PostgreSQL-hez készült Azure Database: Csatlakozás és adatlekérdezés a Node.js használatával
Ebben a rövid útmutatóban azt szemléltetjük, hogy miként lehet [Node.js](https://nodejs.org/) használatával csatlakozni a PostgreSQL-hez készült Azure Database-hez Windows, Ubuntu Linux és Mac platformról. Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. A cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Node.js használatával kapcsolatosan, a PostgreSQL-hez készült Azure Database használatában pedig még járatlan.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – CLI](quickstart-create-server-database-azure-cli.md)

Emellett a következőket kell elvégezni:
- [Node.js](https://nodejs.org) telepítése
- [pg](https://www.npmjs.com/package/pg) csomag telepítése. 

<a id="install-nodejs" class="xliff"></a>

## Node.js telepítése 
Platformtól függően a Node.js telepítéséhez:

<a id="mac-os" class="xliff"></a>

### **Mac OS**
A **brew**, a Mac OS X-hez és a **Node.js**-hez készült egyszerűen használható csomagkezelő telepítéséhez adja meg a következő parancsokat.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
A **Node.js** és az **npm**, a Node.js-hez készült csomagkezelő telepítéséhez írja be a következő parancsokat.

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
Keresse fel a [Node.js letöltési oldalát](https://nodejs.org/en/download/), majd válassza ki a kívánt windowsos telepítési lehetőséget.

<a id="install-pg-client" class="xliff"></a>

## pg-ügyfél telepítése
Telepítse a [pg](https://www.npmjs.com/package/pg)-t, amely a node.js-hez készült, a PostgreSQL-hez való csatlakozáskor és annak lekérdezésekor hasznos tisztán JavaScript alapú nem blokkoló ügyfél.

A pg-ügyfél telepítéséhez futtassa a node package managert (npm) a parancssorban.
```bash
npm install pg
```

A telepítést a telepített csomagok listázásával ellenőrizheti.
```bash
npm list
```
A listában található parancskimenet megerősíti az egyes összetevők verzióját. 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. A teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra van szükség.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal baloldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá az újonnan létrehozott kiszolgálóra **mypgserver-20170401**.
3. Kattintson a **mypgserver-20170401** kiszolgálónévre.
4. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
 ![A PostgreSQL-hez készült Azure Database – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-nodejs/1-connection-string.png)
5. Amennyiben elfelejtette a kiszolgáló bejelentkezési adatait, lépjen az **Overview** (Áttekintés) oldalra, és itt megtudhatja a kiszolgáló rendszergazdájának bejelentkezési nevét, valamint szükség esetén visszaállíthatja a jelszót.

<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>

## A JavaScript-kód futtatása a Node.js-ben
A Node.js-t a `node` beírásával is elindíthatja a Bash-felületről vagy a Windows-parancssorból. Ezt követően futtassa interaktív módon a példa JavaScript-kódot úgy, hogy kimásolja és beilleszti a parancssorba. Másik megoldásként el is mentheti a JavaScript-kódot az adott szövegfájlba, majd a futtatáshoz végezze el a `node filename.js` indítását a paraméterként megadott fájlnévvel.

<a id="connect-create-table-and-insert-data" class="xliff"></a>

## Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat és töltheti be az adatokat a **CREATE TABLE** és az **INSERT INTO** SQL-utasítások segítségével.
A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

<a id="read-data" class="xliff"></a>

## Adatok olvasása
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **SELECT** SQL-utasítás segítségével. A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="update-data" class="xliff"></a>

## Adatok frissítése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **UPDATE** SQL-utasítás segítségével. A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="delete-data" class="xliff"></a>

## Adat törlése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **DELETE** SQL-utasítás segítségével. A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

<a id="next-steps" class="xliff"></a>

## Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása az Exportálás és importálás lehetőség használatával](./howto-migrate-using-export-and-import.md)

