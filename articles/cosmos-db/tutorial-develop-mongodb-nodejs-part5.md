---
title: Angular alkalmazás létrehozása az Azure Cosmos DB API a mongodb-hez – csatlakozás Cosmos DB-hez a Mongoose használata
titleSuffix: Azure Cosmos DB
description: Ez az oktatóanyag ismerteti a Node.js-alkalmazás létrehozása az Angular és az Express használatával az, Cosmos DB-ben tárolt adatok kezeléséhez. Ez a rész a Mongoose használhatja Azure Cosmos DB-hez való kapcsolódáshoz.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Node.js application, so that I can manage the data stored in Cosmos DB.
ms.openlocfilehash: 59ba69f8bf208ab4f38ae326cf4297947f0b844e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547112"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Angular alkalmazás létrehozása az Azure Cosmos DB API a mongodb-hez – csatlakozás Cosmos DB-hez a Mongoose használata

Ez a többrészes oktatóanyag bemutatja, hogyan hozzon létre egy Node.js-alkalmazást az Express és az Angular, és csatlakoztassa azt a [Cosmos DB API a mongodb-hez konfigurált Cosmos-fiók](mongodb-introduction.md). Ez a cikk azt ismerteti, az oktatóanyag 5. rész és épül [4. rész](tutorial-develop-mongodb-nodejs-part4.md).

Az oktatóanyag ezen részében tartalma:

> [!div class="checklist"]
> * Csatlakozás a Cosmos DB-hez a Mongoose használata.
> * A Cosmos DB kapcsolati sztring lekérése.
> * A főképmodell létrehozása.
> * A Főképszolgáltatás létrehozása főképadatok lekéréséhez.
> * Futtassa helyben az alkalmazást.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Ebben az oktatóanyagban a Kezdés előtt hajtsa végre a [4. rész](tutorial-develop-mongodb-nodejs-part4.md).

* Ehhez az oktatóanyaghoz az Azure CLI helyi futtatása. Az Azure CLI 2.0-s vagy újabb verzióját kell telepíteni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Ez az oktatóanyag végigvezeti a lépéseken, az alkalmazás lépésről lépésre. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="use-mongoose-to-connect"></a>Csatlakozás a Mongoose használata

A mongoose-objektum adatmodellezési (eszközgyártók általi) könyvtár MongoDB-hez és Node.js. A Mongoose használhatja az Azure Cosmos DB-fiókhoz való csatlakozáshoz. A következő lépések segítségével telepítse a Mongoose és csatlakozás az Azure Cosmos DB:

1. Telepítse a mongoose npm modult, amely egy felvenni a kapcsolatot a mongodb-hez használt API.

    ```bash
    npm i mongoose --save
    ```

1. Az a **kiszolgáló** mappában hozzon létre egy fájlt **mongo.js**. A kapcsolat részleteit az Azure Cosmos DB-fiókot fogja hozzáadni ehhez a fájlhoz.

1. Másolja az alábbi kódot a **mongo.js** fájlt. A kód a következő szolgáltatásokat nyújtja:

    * A Mongoose használatát igényli.
    * Felülbírálja a Mongo az ES6/ES2015 és újabb verziók épített alap ígéretet használja.
    * Meghív egy env fájlt, amely lehetővé teszi bizonyos dolgokat alapján van-e az átmeneti, éles vagy fejlesztői beállítása. Ezt a fájlt a következő szakaszban fog létrehozni.
    * A MongoDB kapcsolati karakterláncot, amely be van állítva az env fájlban tartalmazza.
    * Létrehoz egy connect függvényt, amely meghívja a Mongoose-t.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
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
    
1. Az Explorer panelen alatt **kiszolgáló**, hozza létre a **környezet**. Az a **környezet** mappában hozzon létre egy fájlt **environment.js**.

1. A mongo.js fájlból értékeinek tartalmaznia kell a `dbName`, a `key`, és a `cosmosPort` paramétereket. Másolja az alábbi kódot a **environment.js** fájlt:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alkalmazás Azure Cosmos DB-hez csatlakozni szeretne az alkalmazás-konfigurációs beállításainak frissítése. A következő lépések segítségével frissítse a beállításokat: 

1. Az Azure Portalon szerezze be a port számát, az Azure Cosmos DB-fiók nevét és elsődleges kulcs értékeit az Azure Cosmos DB-fiókot.

1. Az a **environment.js** fájl, módosítsa az értéket a `port` 10255-re. 

    ```javascript
    const port = 10255;
    ```

1. Az a **environment.js** fájl, módosítsa az értéket a `accountName` neve lesz az Azure Cosmos DB-fiókot, amelyet [4. rész](tutorial-develop-mongodb-nodejs-part4.md) az oktatóanyag. 

1. Kérje le az Azure Cosmos DB-fiók elsődleges kulcsát az alábbi parancssori paranccsal a terminálablakban: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name > a létrehozott Azure Cosmos DB-fiók neve [4. rész](tutorial-develop-mongodb-nodejs-part4.md) az oktatóanyag.

1. Másolja az elsődleges kulcs értékét a **environment.js** fájlt úgy a `key` értéket.

Az alkalmazás most már rendelkezik a csatlakozás az Azure Cosmos DB összes szükséges információt. 

## <a name="create-a-hero-model"></a>Főképmodell létrehozása

Ezután meg kell a sémájának meghatározása egy modellfájl megadásával az Azure Cosmos DB tárolja az adatokat. Az alábbi lépések segítségével hozzon létre egy _főképmodell_ , amely meghatározza, hogy az adatok a séma:

1. Az Explorer panelen alatt a **kiszolgáló** mappában hozzon létre egy fájlt **hero.model.js**.

1. Másolja az alábbi kódot a **hero.model.js** fájlt. A kód a következő szolgáltatásokat nyújtja:

   * A Mongoose használatát igényli.
   * Egy új sémát hoz létre egy azonosítóval, egy névvel és egy üzenettel.
   * Létrehoz egy modellt a séma használatával.
   * Exportálja a modellt. 
   * A gyűjtemény neve **fő képek** (helyett **heros név**, amely a Mongoose többes számú elnevezési szabályai alapján a gyűjtemény alapértelmezett neve).

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

Miután létrehozta a főképmodell, meg kell határozhat meg az adatokat, és hajtsa végre a listában, létrehozása, törlése és frissítési műveletek. Az alábbi lépések segítségével hozzon létre egy _főképszolgáltatás_ , amely lekéri az adatokat az Azure Cosmos DB:

1. Az Explorer panelen alatt a **kiszolgáló** mappában hozzon létre egy fájlt **hero.service.js**.

1. Másolja az alábbi kódot a **hero.service.js** fájlt. A kód a következő szolgáltatásokat nyújtja:

   * Lekéri a létrehozott modellt.
   * Csatlakozás az adatbázishoz.
   * Létrehoz egy `docquery` változót, amely a `hero.find` meghatároz egy lekérdezést, amely visszaadja az összes fő módszert.
   * A lekérdezést a `docquery.exec` egy beváltja az összes fő listájának beolvasása, ahol a válasz állapota 200 függvényt. 
   * Küld vissza a hibaüzenet, ha az állapot 500.
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

## <a name="configure-routes"></a>Útvonalak beállítása

Ezután kell kezelni az URL-címek a get, útvonalak beállítása létrehozni, olvasni, és törlési kérelmek. Az útválasztási metódusok adja meg a visszahívási függvényekben (más néven _eseménykezelési funkciókat_). Ezek a függvények nevezzük, amikor az alkalmazás a megadott végponton, és a HTTP-metódus kérést kap. A főképszolgáltatás hozzáadása és definiálni az útvonalakat, kövesse az alábbi lépéseket:

1. A Visual Studio Code-a a **routes.js** fájlt, tegye megjegyzésbe a `res.send` -függvény, amely a minta fő kép adatait küldi. Adjon hozzá egy sort hívja a `heroService.getHeroes` inkább működik.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. Az a **routes.js** fájl `require` a főképszolgáltatás:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. Az a **hero.service.js** fájlt, frissítse a `getHeroes` függvényt, hogy a `req` és `res` paramétereket az alábbiak szerint:

    ```javascript
    function getHeroes(req, res) {
    ```

Szánjunk néhány percet, és körbejárja az előző kód. Először nézzük az index.js fájlba, amely beállítja a csomópont-kiszolgálót. Figyelje meg, hogy állít be, és határozza meg az útvonalakat. Ezután a routes.js fájl a főképszolgáltatás műsorgazdája, és utasítja, hogy az első az funkciók, például **getheroes függvényt**, és továbbítsa a kérést és választ. A hero.service.js fájl lekérdezi a modellt, és csatlakozik a Mongo. Ezután végrehajtása **getheroes függvényt** amikor nevezik, és adja vissza biztonsági 200-as választ. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezután futtassa az alkalmazást az alábbi lépések segítségével:

1. A Visual Studio Code-ban mentse az összes módosítást. A bal oldalon válassza ki a **Debug** gomb ![hibakeresés ikon a Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png), majd válassza ki a **hibakeresés indítása** gomb ![hibakeresés ikon a Visual Studio Code-ban ](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

1. Most váltson a böngészőben. Nyissa meg a **fejlesztői eszközök** és a **hálózati lap**. Lépjen a `http://localhost:3000`, és nem látja az alkalmazást.

    ![Új Azure Cosmos DB-fiók az Azure Portalon](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Nincsenek nem tárol még az alkalmazás fő képeket. Ez az oktatóanyag következő részében, hozzáadjuk a put, push és törlési funkciót. Ezután azt is hozzáadása, frissítése és törölhet fő képeket a felhasználói felület az Azure Cosmos DB-adatbázishoz a Mongoose-kapcsolatokon keresztül. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokat, törölheti az erőforráscsoportot, az Azure Cosmos DB-fiók és minden kapcsolódó erőforrás. A következő lépések használatával törölje az erőforráscsoportot:

 1. Nyissa meg az erőforráscsoport, amelyben létrehozta az Azure Cosmos DB-fiókot.
 1. Válassza az **Erőforráscsoport törlése** elemet.
 1. Erősítse meg az erőforráscsoport törléséhez, és válassza ki a nevét **törlése**.

## <a name="next-steps"></a>További lépések

Folytassa a 6. rész az oktatóanyag hozzáadása a Post, Put és Delete függvényeket az alkalmazáshoz:

> [!div class="nextstepaction"]
> [6. rész: Adja hozzá a Post, Put és Delete függvényeket az alkalmazáshoz](tutorial-develop-mongodb-nodejs-part6.md)
