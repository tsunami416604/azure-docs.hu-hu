---
title: Csatlakozás az Azure Database for PostgreSQL-hez a Node.js segítségével
description: Ebben a rövid útmutatóban biztosítjuk a PostgreSQL-hez készült Azure Database-ről csatlakozásra és adatlekérdezésre használható Node.js-kódmintát.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 4c7f2f71ff302cbaa0aab66f97b93cb1c5b9e5e4
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541115"
---
# <a name="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data"></a>Azure Database for postgresql-hez: Csatlakozás és adatlekérdezés a Node.js használatával
Ez a rövid útmutató azt ismerteti, hogyan lehet csatlakozni az Azure Database for PostgreSQL-hez egy [Node.js](https://nodejs.org/)-alkalmazással. Azt is bemutatja, hogyan lehet SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. A jelen cikkben ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Node.js használatával kapcsolatban, az Azure Database for PostgreSQL használatában pedig még járatlan.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – CLI](quickstart-create-server-database-azure-cli.md)

Emellett a következőket kell elvégezni:
- [Node.js](https://nodejs.org) telepítése

## <a name="install-pg-client"></a>pg-ügyfél telepítése
Telepítse a [pg](https://www.npmjs.com/package/pg)-t, amely egy PostgreSQL-ügyfél a Node.js-hez.

A pg-ügyfél telepítéséhez futtassa a node package managert (npm) a parancssorban.
```bash
npm install pg
```

A telepítést a telepített csomagok listázásával ellenőrizheti.
```bash
npm list
```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mydemoserver**).
3. Kattintson a kiszolgálónévre.
4. A kiszolgáló **Áttekintés** paneléről jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**. Ha elfelejti a jelszavát, ezen a panelen új jelszót is tud kérni.
 ![Azure Database for PostgreSQL-kiszolgáló neve](./media/connect-nodejs/1-connection-string.png)

## <a name="running-the-javascript-code-in-nodejs"></a>A JavaScript-kód futtatása a Node.js-ben
A Node.js-t elindíthatja a Bash felületről, a Terminal eszközből vagy a Windows parancssorból a `node` parancs beírásával. Ezután a JavaScript-kód interaktív futtatásához másolja és illessze be azt a promptba. Másik megoldásként el is mentheti a JavaScript-kódot az adott szövegfájlba, majd a futtatáshoz végezze el a `node filename.js` indítását a paraméterként megadott fájlnévvel.

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód használatával csatlakozhat és töltheti be az adatokat a **CREATE TABLE** és az **INSERT INTO** SQL-utasítások segítségével.
A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DROP TABLE IF EXISTS inventory;
        CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
        INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
        INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
        INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    `;

    client
        .query(query)
        .then(() => {
            console.log('Table created successfully!');
            client.end(console.log('Closed client connection'));
        })
        .catch(err => console.log(err))
        .then(() => {
            console.log('Finished execution, exiting now');
            process.exit();
        });
}
```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **SELECT** SQL-utasítás segítségével. A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else { queryDatabase(); }
});

function queryDatabase() {
  
    console.log(`Running query to PostgreSQL server: ${config.host}`);

    const query = 'SELECT * FROM inventory;';

    client.query(query)
        .then(res => {
            const rows = res.rows;

            rows.map(row => {
                console.log(`Read: ${JSON.stringify(row)}`);
            });

            process.exit();
        })
        .catch(err => {
            console.log(err);
        });
}
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **UPDATE** SQL-utasítás segítségével. A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) throw err;
    else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        UPDATE inventory 
        SET quantity= 1000 WHERE name='banana';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Update completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="delete-data"></a>Adat törlése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok olvasását **DELETE** SQL-utasítás segítségével. A [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) objektum a PostgreSQL-kiszolgálóval való használatra szolgál. A [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) függvény az SQL-lekérdezés PostgreSQL-adatbázison való végrehajtására szolgál. 

Cserélje le a gazdagép, az adatbázisnév, a felhasználó és a jelszó paramétereit azokra az értékekre, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg. 

```javascript
const pg = require('pg');

const config = {
    host: '<your-db-server-name>.postgres.database.azure.com',
    // Do not hard code your username and password.
    // Consider using Node environment variables.
    user: '<your-db-username>',     
    password: '<your-password>',
    database: '<name-of-database>',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(err => {
    if (err) {
        throw err;
    } else {
        queryDatabase();
    }
});

function queryDatabase() {
    const query = `
        DELETE FROM inventory 
        WHERE name = 'apple';
    `;

    client
        .query(query)
        .then(result => {
            console.log('Delete completed');
            console.log(`Rows affected: ${result.rowCount}`);
        })
        .catch(err => {
            console.log(err);
            throw err;
        });
}
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
