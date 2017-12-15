---
title: "MongoDB API-k segítségével az Azure Cosmos DB alkalmazás létrehozása |} Microsoft Docs"
description: "Ez az oktatóanyag az Azure Cosmos DB API-k használatával mongodb egy online adatbázist hoz létre."
keywords: "mongodb-példák"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.openlocfilehash: aa545a9fbaac0686a0a29482c8dcd3379ee4db8f
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Egy Azure Cosmos DB létrehozása: API-t a MongoDB-alkalmazásokhoz Node.js használatával
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js MongoDB-hez](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

Ez a példa bemutatja, hogyan hozhat létre egy Azure Cosmos DB: API-t a MongoDB-Konzolalkalmazás Node.js segítségével.

Ebben a példában használatához az alábbiak szükségesek:

* [Hozzon létre](create-mongodb-dotnet.md#create-account) egy Azure Cosmos DB: API-t a MongoDB-fiók.
* A MongoDB beolvasása [kapcsolati karakterlánc](connect-mongodb-account.md) információkat.

## <a name="create-the-app"></a>Az alkalmazás létrehozása

1. Hozzon létre egy *app.js* fájl és másolja és illessze be az alábbi kódot.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

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

2. A következő változók módosítása a *app.js* fiókbeállításokban / fájl (keresése a [kapcsolati karakterlánc](connect-mongodb-account.md)):
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Nyissa meg kedvenc terminálját, és futtassa **npm mongodb--telepítése Mentés**, majd futtassa az alkalmazás a **csomópont app.js**

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [MongoChef használja](mongodb-mongochef.md) együtt az Azure Cosmos DB: API-t a MongoDB-fiók.
