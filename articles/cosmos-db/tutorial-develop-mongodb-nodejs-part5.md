---
title: "MongoDB, Angular és Node oktatóanyag az Azure-hoz – 5. rész | Microsoft Docs"
description: "A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 5. része."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.openlocfilehash: 697ea4aedb025f4bff4b88df3370ed7c12e7b0d7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>MongoDB-alkalmazás létrehozása az Angular és az Azure Cosmos DB használatával – 5. rész: A Mongoose használata az Azure Cosmos DB-hez való csatlakozáshoz

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy Node.js-ben írt új [MongoDB API](mongodb-introduction.md)-alkalmazást az Express, az Angular és az Azure Cosmos DB-adatbázis használatával.

Az oktatóanyag 5. része a [4. részre](tutorial-develop-mongodb-nodejs-part4.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * A Mongoose használata az Azure Cosmos DB-hez való csatlakozáshoz
> * Kapcsolati sztringek adatainak lekérése az Azure Cosmos DB-ből
> * A főképmodell létrehozása
> * A főképszolgáltatás létrehozása főképadatok lekéréséhez
> * Az alkalmazás futtatása helyben

## <a name="video-walkthrough"></a>Bemutató videó

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp végezze el az oktatóanyag [4. részében](tutorial-develop-mongodb-nodejs-part4.md) ismertetett lépéseket.

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>A Mongoose használata az Azure Cosmos DB-hez való csatlakozáshoz

1. Telepítse a mongoose npm modult, amely egy általában a MongoDB-vel való kommunikációhoz használt API.

    ```bash
    npm i mongoose --save
    ```

2. Most hozzon létre egy új fájlt a **server** mappában **mongo.js** néven. Ebbe a fájlba felveszi majd az Azure Cosmos DB adatbázis összes kapcsolati adatát.

3. Másolja az alábbi kódot a **mongo.js** fájlba. Ez a kód:
    * A Mongoose használatát igényli.
    * Felülbírálja a Mongo-ígéretet és az ES6/ES2015 és az azt követő verziókba épített alap ígéretet használja.
    * Meghív egy env fájlt, amelyben elvégezhet bizonyos beállításokat annak függvényében, hogy előkészítési, termelési vagy fejlesztői környezetet használ-e. Ezt a fájlt nemsokára létrehozzuk.
    * Tartalmazza a MongoDB kapcsolati sztringünket, amelyet az env fájlban állítunk majd be.
    * Létrehoz egy connect függvényt, amely meghívja a Mongoose-t.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. Az Explorer panelen hozzon létre egy mappát a **server** mappában **environment** néven, majd ebben az **environment** mappában egy új fájlt **environment.js** néven.

5. A mongo.js fájlból most a `dbName`, a `key` és a `cosmosPort` részekre van szükségünk, ezért másolja az alábbi kódot az **environment.js** fájlba.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>Kapcsolati sztring adatainak lekérése

1. Az **environment.js** fájlban módosítsa a `port` értékét 10255-re. (A Cosmos DB-portot az Azure Portalon találja meg.)

    ```javascript
    const port = 10255;
    ```

2. Az **environment.js** fájlban módosítsa a `accountName` értékét a [4. lépésben](tutorial-develop-mongodb-nodejs-part4.md) létrehozott Azure Cosmos DB-fiók nevére. 

3. Kérje le az Azure Cosmos DB-fiók elsődleges kulcsát az alábbi parancssori paranccsal a terminálablakban: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * A `<cosmosdb-name>` a [4. lépésben](tutorial-develop-mongodb-nodejs-part4.md) létrehozott Azure Cosmos DB-fiók neve.

4. Másolja az elsődleges kulcsot az environment.js fájlba a `key` értékeként.

    Az alkalmazás most már minden adattal rendelkezik, amelyekre szüksége van az Azure Cosmos DB-hez való csatlakozáshoz. Ezek az adatok a portálon keresztül is lekérhetők. További információ: [A MongoDB kapcsolati sztring lekérése testre szabáshoz](connect-mongodb-account.md#GetCustomConnection). A portálon használt Felhasználónév megegyezik az environments.js fájlban lévő dbName névvel. 

## <a name="create-a-hero-model"></a>Főképmodell létrehozása

1.  Az Explorer panelen hozza létre a **hero.model.js** fájlt a **server** mappában.

2. Másolja az alábbi kódot a **hero.model.js** fájlba. Ez a kód:
   * A Mongoose használatát igényli.
   * Egy új sémát hoz létre egy azonosítóval, egy névvel és egy üzenettel.
   * Létrehoz egy modellt a séma használatával.
   * Exportálja a modellt. 
   * A gyűjteménynek a Heroes nevet adja (a Heros név helyett, amely a Mongoose többes számú elnevezési szabályai alapján a gyűjtemény alapértelmezett neve lenne).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Főképszolgáltatás létrehozása

1.  Az Explorer panelen hozza létre a **hero.service.js** fájlt a **server** mappában.

2. Másolja az alábbi kódot a **hero.service.js** fájlba. Ez a kód:
   * Lekéri az imént létrehozott modellt.
   * Csatlakozik az adatbázishoz.
   * Létrehoz egy docquery változót, amely a hero.find metódus használatával meghatároz egy lekérdezést, amely visszaadja az összes fő képet.
   * Lefuttat egy lekérdezést a docquery.exec használatával azzal az ígérettel, hogy az összes fő kép listáját visszaadja, ahol a válasz állapota 200. 
   * Ha az állapot 500, hibaüzenetet ad vissza.
   * Mivel modulokat használunk, lekéri a fő képeket. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>A főképszolgáltatás hozzáadása a routes.js fájlhoz

1. A Visual Studio Code-ban, a **routes.js** fájlban tegye megjegyzésbe a `res.send` függvényt, amely a minta fő kép adatait küldi, és adjon hozzá egy sort, amely ehelyett a `heroService.getHeroes` függvényt hívja meg.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. A **routes.js** fájlban írja elő a főképszolgáltatás használatát:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. A **hero.service.js** fájlban frissítse a getHeroes függvényt, hogy felvegye a `req` és `res` paramétereket az alábbiak szerint:

    ```javascript
    function getHeroes(req, res) {
    ```

    Szánjunk néhány percet az itt leírt híváslánc áttekintésére és végigkövetésére. Nézzük először a csomópont-kiszolgálót létrehozó `index.js` fájlt, és láthatjuk, hogy ez állítja be és határozza meg az útvonalainkat. A routes.js fájl ezután a főképszolgáltatással kommunikál, és utasítja, hogy kérje le a függvényeinket (például a getHeroes függvényt), illetve továbbítsa a kérést és a választ. A hero.service.js most fogja a modellt, és csatlakozik a Mongóhoz, majd végrehajtja a getHeroes függvényt, amikor meghívjuk, és visszaadja a 200-as választ. Aztán ismét végiggyűrűzik a láncon. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. Most futtassuk újra az alkalmazást. A Visual Studio Code-ban mentse az összes módosítást, kattintson a **Hibakeresés**  gombra ![Hibakeresés ikon a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) a bal oldalon, majd a **Hibakeresés indítása** gombra ![Hibakeresés ikon a Visual Studio Code-ban](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Most váltson vissza a böngészőre, nyissa meg a Fejlesztői eszközöket és a Hálózat lapot, majd lépjen a http://localhost:3000 helyre, ahol megtalálja az alkalmazást.

    ![Új Azure Cosmos DB-fiók az Azure Portalon](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Az alkalmazás még nem tárol fő képeket, az oktatóanyag következő részében azonban majd hozzáadjuk a put, push és delete funkcionalitást, amelyekkel hozzáadhat, frissíthet és törölhet fő képeket a felhasználói felületen keresztül az Azure Cosmos DB-vel létesített Mongoose-kapcsolatokon keresztül. 

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * A Mongoose API-k használatával csatlakoztatta főképalkalmazását az Azure Cosmos DB-hez 
> * Hozzáadta a fő képek lekérését végző funkciót az alkalmazáshoz

Továbbléphet az oktatóanyag következő részére, amelyben Post, Put és Delete függvényeket adhat az alkalmazáshoz.

> [!div class="nextstepaction"]
> [Post, Put és Delete függvények hozzáadása az alkalmazáshoz](tutorial-develop-mongodb-nodejs-part6.md)
