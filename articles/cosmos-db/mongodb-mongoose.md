---
title: "A Mongoose keretrendszer használata Azure Cosmos DB |} Microsoft Docs"
description: "Megtudhatja, hogyan Node.js Mongoose alkalmazás csatlakoztatása az Azure Cosmos-Adatbázishoz"
services: cosmos-db
documentationcenter: 
author: romitgirdhar
manager: jhubbard
editor: 
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: 9878936b5dd76730633dec16b1c3a3eaac78e95a
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Az Azure Cosmos DB: A Mongoose keretrendszer használata Azure Cosmos DB

Ez az oktatóanyag bemutatja, hogyan kell használni a [Mongoose keretrendszer](http://mongoosejs.com/) adatokat az Adatbázisba az Azure Cosmos tárolásakor. A MongoDB API-t az Azure Cosmos DB, a forgatókönyv használjuk. Azok az Ön ismeri Mongoose node.js mongodb objektum modellezési keretrendszere, és az alkalmazás adatok egyszerű feladat, a séma-alapú megoldást kínál.

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Segítségével gyorsan létrehozhat és lekérdezhet dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike felhasználja az Azure Cosmos DB középpontjában álló globális elosztási és horizontális skálázhatósági képességeket.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/)-verzió: 0.10.29-s vagy újabb.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókot, ugorjon előre [a Node.js-alkalmazás beállítása](#SetupNode). Ha az Azure Cosmos DB Emulator használ, kövesse a [Azure Cosmos DB emulátor](local-emulator.md) az emulátor beállítását, és ugorjon előre [a Node.js-alkalmazás beállítása](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>A Node.js-alkalmazás beállítása

>[!Note]
> Ha szeretné a forgatókönyv csak a mintakódot helyett telepítő magának az alkalmazásnak, klónozza a [minta](https://github.com/Azure-Samples/Mongoose_CosmosDB) ebben az oktatóanyagban használt, és a Node.js Mongoose alkalmazás az Azure Cosmos-adatbázis létrehozása.

1. Node.js-alkalmazás létrehozása a kiválasztott mappában, a következő parancsot egy csomópont parancssorban.

    ```npm init```

    A kérdések megválaszolása és készen áll a projekthez.

1. Új fájl hozzáadása a mappát, és adjon neki nevet ```index.js```.
1. Telepítse a szükséges csomagokat egyikével a ```npm install``` beállítások:
    * Mongoose:```npm install mongoose --save```
    * Dotenv (Ha azt szeretné, a titkos kulcsok .env fájlból történő betöltése):```npm install dotenv --save```
    
    >[!Note]
    > A ```--save``` jelzőt a package.json fájlt ad hozzá a függőség.

1. Importálja a függőségeket a index.js fájlban.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. A Cosmos DB kapcsolati karakterláncot és a Cosmos adatbázis nevét a ```.env``` fájlt.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Csatlakozás Azure Cosmos DB index.js végén ad hozzá a következő kódot a Mongoose keretrendszer használatával.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > Itt a környezeti változók betöltött process.env használatával. {variableName} a "dotenv" npm csomag segítségével.

    Miután csatlakozott az Azure Cosmos Adatbázishoz, a Mongoose most elindíthatja a hálózatiobjektum-modelljei beállítása.
    
## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>A Mongoose segítségével Azure Cosmos DB figyelmeztetések

Minden modell hoz létre Mongoose alatt a magában foglalja az új MongoDB gyűjteményt hoz létre. Azonban megadott Azure Cosmos DB gyűjteményre számlázási modelljét, nem lehet lépjen, a leginkább költséghatékony módja Ha több objektum modellek, amelyek ritkán fel van töltve.

Ez a forgatókönyv mindkét modellre vonatkozik. Oktatóanyag a következőket először ismerteti a forgatókönyv a egy típusú gyűjteményenként adatok tárolására. Ez az a Mongoose defacto viselkedését.

Mongoose is rendelkezik egy fogalom nevű [Discriminators](http://mongoosejs.com/docs/discriminators.html). Discriminators egy séma öröklési mechanizmus. Ezek lehetővé teszik több modellek fölött ugyanaz a mögöttes MongoDB gyűjtemény átfedő sémával rendelkezik.

A különböző adatmodellekben ugyanaz a gyűjtemény tárolja, és segítségével egy szűrési záradékot időben lekérik a csak a szükséges adatokat.

### <a name="one-collection-per-object-model"></a>Egy gyűjtemény / hálózatiobjektum-modellje

A Mongoose alapértelmezés lesz MongoDB-gyűjtemény létrehozása minden alkalommal, amikor egy objektum modell létrehozása. Ez a szakasz ismerteti, hogyan Azure Cosmos DB a MongoDB-vel ennek érdekében. Ez az Azure Cosmos DB esetén ajánlott nagy mennyiségű adat objektum modellek rendelkezik. Ez az alapértelmezett modell működő mongoose bővítményt, úgy, akkor előfordulhat, hogy ismernie kell, ha jártas a mongoose bővítményt.

1. Nyissa meg a ```index.js``` újra.

1. A "Termékcsalád" séma-definíció létrehozása.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Az objektum létrehozása "Termékcsalád".

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Végezetül most mentse az objektumot az Azure Cosmos-Adatbázishoz. Ezzel létrehoz egy gyűjteményt a magában foglalja az alatt.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Most hozzon létre egy másik sémájának és objektumformátumának. Ez alkalommal, hozzon létre egyet a családok előfordulhat, hogy érdeklődik "szabadsága célhoz".
    1. Csakúgy, mint a legutóbbi hozzon létre a rendszer
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Hozzon létre egy minta-objektumot (adhat hozzá több objektumot a sémát), és mentse azt.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Most is be az Azure portálon, az Azure Cosmos Adatbázisba létrehozott két gyűjtemény észlel.

    ![NODE.js-oktatóanyag – képernyőfelvétel az Azure portálról, egy Azure Cosmos DB fiókot megjelenítő nevű gyűjtemény kiemelésével – Node-adatbázis][alldata]

1. Végezetül most beolvasni az adatokat az Azure Cosmos-Adatbázisból. Mivel az alapértelmezett Mongoose üzemi modell használunk, olvasási ugyanazok, mint bármely más Mongoose az olvasási műveletek.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Mongoose discriminators használatával egyetlen gyűjteményben található adatok tárolásához

Ezt a módszert használjuk [Mongoose Discriminators](http://mongoosejs.com/docs/discriminators.html) optimalizálásához Azure Cosmos DB gyűjteményt költségek. Discriminators engedélyezi annak meghatározását egy ezeknek "kulcs", így tárolásához, megkülönböztetéséhez, és különböző hálózatiobjektum-modelljei szűrni.

Itt létrehozhatunk egy alapobjektum modellhez tartozó, "Családhoz" és "VacationDestinations" Hozzáadás bővítményként a alap modell és ezeknek kulcs megadása.

1. Most az alapszintű konfigurációs beállítása, és adja meg a Diszkriminátor kulcshoz.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. A következő is határozza meg a közös hálózatiobjektum-modellje

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Most meghatároztuk a "Családhoz" modell. Itt láthatja, hogy használjuk ```commonModel.discriminator``` helyett ```mongoose.model```. Emellett azt is visszaigazolása az alapszintű konfigurációs mongoose séma. Igen, itt, a discriminatorKey van ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Hasonlóképpen adjuk hozzá egy másik séma, "VacationDestinations" most. Itt a DiscriminatorKey van ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Végezetül tegyük a modell-objektumokat hozzon létre, és mentse.
    1. Adjunk objektum(ok) "Családhoz" mintának.
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. A következő most objektumok hozzáadása a "VacationDestinations" modellhez, és mentse azt.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. Most, ha vissza az Azure-portálon, bizonyára észrevette, hogy rendelkezik-e hívása csak egy gyűjtemény ```alldata``` "Családhoz" és a "VacationDestinations" adatokkal.

    ![NODE.js-oktatóanyag – képernyőfelvétel az Azure portálról, egy Azure Cosmos DB fiókot megjelenítő nevű gyűjtemény kiemelésével – Node-adatbázis][mutiple-coll]

1. Továbbá figyelje meg, hogy minden objektum rendelkezik-e egy másik attribútum néven ```__type```, amely megkülönböztetni a két különböző objektum modell segítségével.

1. Végezetül most olvassa el az Azure Cosmos DB-ben tárolt adatok. Mongoose gondoskodik a modellen alapuló adatok szűréséhez. Úgy hogy nincs másik adatok olvasásakor. Csak adja meg a modell (ebben az esetben ```Family_common```) és a "DiscriminatorKey" szűrés Mongoose kezeli.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Ahogy látja, akkor egyszerűen dolgozhat Mongoose discriminators. Ezért, ha egy alkalmazás, amely a Mongoose keretrendszert használja, ez az oktatóanyag lehetővé teszik a az alkalmazás, és fut-e a MongoDB API-t az Azure Cosmos-adatbázis anélkül, hogy túl sok változás az.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

További tudnivalók a MongoDB műveletek, operátorok, fázisból áll, parancsokat és az Azure Cosmos DB MongoDB API támogatja a beállítások [MongoDB API támogatja a MongoDB-szolgáltatások és szintaxis](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png