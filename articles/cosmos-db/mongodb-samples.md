---
title: Egy Azure Cosmos DB-alkalmazás létrehozása a MongoDB API-k használatával
description: Oktatóanyag, amelynek során egy online adatbázist hoz létre a MongoDB-hez készült Azure Cosmos DB API-k használatával.
keywords: mongodb példák
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: e24c64f0042d65cc499e02dff7b53f3ca812f356
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864077"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Azure Cosmos DB: API készítése a MongoDB-hez a Node.js használatával
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js MongoDB-hez](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Ez a példa azt mutatja be, hogyan készíthető Azure Cosmos DB: API MongoDB konzolalkalmazáshoz a Node.js használatával.

A példa használatához a következőket kell tennie:

* MongoDB-hez készült Azure Cosmos DB API-fiók [létrehozása](create-mongodb-dotnet.md#create-account).
* A MongoDB [kapcsolati karakterlánc](connect-mongodb-account.md) információk beszerzése.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

1. Egy *app.js* fájl létrehozása és az alábbi információ bemásolása.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Más lehetőség**: A **MongoDB Node.js 2.2 illesztőprogram** használata esetén cserélje ki a következő kódrészletet:

    Eredeti:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Amire le kell cserélnie:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Módosítsa a következő változókat az *app.js* fájlban a fiókbeállításainak megfelelően (Hogyan található meg a [kapcsolati karakterlánc](connect-mongodb-account.md)?):

    > [!IMPORTANT]
    > A **MongoDB Node.js 3.0 illesztőprogram** megköveteli a Cosmos DB-jelszóban lévő speciális karakterek kódolását. Az „=” karakter %3D formában kódolandó.
    >
    > Példa: A *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* jelszó kódolva *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D* lesz
    >
    > A **MongoDB Node.js 2.2 illesztőprogram** nem követeli meg a Cosmos DB-jelszóban lévő speciális karakterek kódolását.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Nyissa meg kedvenc terminálját, futtassa az **npm install mongodb --save** parancsot, majd futtassa alkalmazását a **node app.js** paranccsal.

## <a name="next-steps"></a>További lépések
* Ismerje meg a [MongoChef használatát](mongodb-mongochef.md) a MongoDB-hez készült Azure Cosmos DB API-fiókkal.
