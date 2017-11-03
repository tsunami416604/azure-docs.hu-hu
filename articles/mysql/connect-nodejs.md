---
title: "Csatlakozás a MySQL-hez készült Azure-adatbázishoz a Node.js-sel | Microsoft Docs"
description: "Ez a rövid útmutató számos Node.js-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 2f18016614b229273aa4d661991149be949ce238
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-nodejs-to-connect-and-query-data"></a>A MySQL-hez készült Azure-adatbázis: Csatlakozás és adatlekérdezés a Node.js használatával
Ez a rövid útmutató ismerteti, hogyan használható a [Node.js](https://nodejs.org/) a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz Windows, Ubuntu Linux és Mac platformról. Azt is bemutatja, hogyan lehet SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. Ez a témakör azt feltételezi, hogy ismeri a Node.js használatával történő fejlesztéséhez, és, hogy még nem ismeri a MySQL az Azure-adatbázis használata.

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

Emellett a következőket kell elvégezni:
- Telepítse a [Node.js](https://nodejs.org)-futtatókörnyezetet.
- Telepítse a [mysql2](https://www.npmjs.com/package/mysql2)-csomagot a MySQL-hez való csatlakozáshoz a Node.js-alkalmazásból. 

## <a name="install-nodejs-and-the-mysql-connector"></a>A Node.js és a MySQL-összekötő telepítése
Attól függően, hogy a platform hajtsa végre a megfelelő részben található útmutatást követve telepítse a Node.js. Az npm használatával telepítse a mysql2 csomagot és annak függőségeit a projektmappába.

### <a name="windows"></a>**Windows**
1. Látogasson el a [Node.js letölti lap](https://nodejs.org/en/download/), és válassza ki a kívánt Windows installer lehetőséget.
2. Hozzon létre egy helyi projektmappát, például: `nodejsmysql`. 
3. A parancssor megnyitása, majd módosítsa a könyvtárat a projekthez mappába, például a`cd c:\nodejsmysql\`
4. A mysql2 könyvár a projektmappába telepítéséhez futtassa az NPM eszközt.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql2
   "C:\Program Files\nodejs\npm" list
   ```

5. Ellenőrizze a telepítést. Ehhez ellenőrizze az `npm list` kimeneti szövegét az `mysql2@1.3.5` esetében.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. A **Node.js** és az **npm**, a Node.js csomagkezelőjének telepítéséhez futtassa a következő parancsokat.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. A következő parancsokat a projekt mappa létrehozásához `mysqlnodejs` és a mysql2 telepítéséhez ebbe a mappába.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql2
   npm list
   ```
3. Ellenőrizze a telepítést. Ehhez ellenőrizze az npm list kimeneti szövegét az `mysql2@1.3.5` esetében.

### <a name="mac-os"></a>**Mac OS**
1. A **brew**, egy Mac OS X-szel és a **Node.js**-vel kompatibilis, egyszerűen használható csomagkezelő telepítéséhez adja meg a következő parancsokat.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. A következő parancsokat a projekt mappa létrehozásához `mysqlnodejs` és a mysql2 telepítéséhez ebbe a mappába.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql2
   npm list
   ```

3. Ellenőrizze a telepítést. Ehhez ellenőrizze az `npm list` kimeneti szövegét az `mysql2@1.3.6` esetében. Új javítások kiadásakor a verziószám változhat.

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A bal oldali ablaktáblán kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például: **myserver4demo**).
3. Kattintson a **myserver4demo** kiszolgálónévre.
4. Válassza ki a kiszolgálót **tulajdonságok** lapon, majd jegyezze fel a **kiszolgálónév** és **kiszolgálói rendszergazda bejelentkezési név**.
 ![MySQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-nodejs/1_server-properties-name-login.png)
5. Ha elfelejti a kiszolgálói bejelentkezési adatok, navigáljon a **áttekintése** lapon megtekintéséhez a rendszergazdai bejelentkezési nevet, és ha szükséges a jelszó alaphelyzetbe állítása.

## <a name="running-the-javascript-code-in-nodejs"></a>A JavaScript-kód futtatása a Node.js-ben
1. A JavaScript-kód beillesztése szövegfájlok, és mentse a fájl kiterjesztése .js (például C:\nodejsmysql\createtable.js vagy /home/username/nodejsmysql/createtable.js) egy projekt mappába.
2. A parancssor megnyitása vagy bash rendszerhéjat, majd módosítsa a könyvtárat a projekthez mappába `cd nodejsmysql`.
3. Az alkalmazás futtatásához ezután írja be a csomópontparancsot, majd a fájlnevet (például: `node createtable.js`).
4. Ha Windows rendszeren nem található a Node.js-alkalmazás a path környezeti változóban, előfordulhat, hogy a teljes elérési utat kell használnia a Node.js-alkalmazás elindításához, például: `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
Az alábbi kód segítségével csatlakozzon, és az adatok betöltése a **CREATE TABLE** és **INSERT INTO** SQL-utasításokat.

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) függvény a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) függvény az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **válasszon** SQL-utasításban. 

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) metódus a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) metódus az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. Az eredménytömb a lekérdezés eredményeinek tárolására használható.

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Adatok frissítése
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **frissítés** SQL-utasításban. 

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) metódus a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) metódus az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Adat törlése
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **törlése** SQL-utasításban. 

A [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) metódus a MySQL-kiszolgálóhoz való csatlakozásra szolgál. A [connect()](https://github.com/mysqljs/mysql#establishing-connections) metódus a kiszolgálóval való kapcsolat létesítésére szolgál. A [query()](https://github.com/mysqljs/mysql#performing-queries) metódus az SQL-lekérdezés MySQL-adatbázison való végrehajtására szolgál. 

Cserélje le a `host`, `user`, `password` és `database` paramétert azokkal az értékekkel, amelyeket a kiszolgáló és az adatbázis létrehozásakor adott meg.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
