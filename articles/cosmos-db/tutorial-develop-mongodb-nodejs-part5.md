---
title: Csatlakoztassa az Angular alkalmazást az Azure Cosmos DB MongoDB-hoz való API-jához a Mongúz használatával
description: Ez az oktatóanyag ismerteti, hogyan hozhat létre egy Node.js alkalmazást az Angular és az Express használatával a Cosmos DB-ben tárolt adatok kezeléséhez. Ebben a részben a Mongúz használatával csatlakozhat az Azure Cosmos DB-hez.
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
ms.openlocfilehash: ba893eeb8c2560397f3524d1042566dbafee7d1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75444706"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Hozzon létre egy Szögletes alkalmazást az Azure Cosmos DB MongoDB-hoz való API-jával – a Mongoose használatával csatlakozzon a Cosmos DB-hez

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre node.js alkalmazást Express és Angular alkalmazással, és hogyan csatlakoztathatja azt a [Cosmos DB MongoDB API-j api-j-j-j-fiókjához.](mongodb-introduction.md) Ez a cikk ismerteti az [5.](tutorial-develop-mongodb-nodejs-part4.md)

Ebben a részben a bemutató, akkor:

> [!div class="checklist"]
> * A Cosmos DB-hez való csatlakozáshoz használja a Mongúzt.
> * A Cosmos DB kapcsolati karakterláncának beszereznie kell.
> * Hozza létre a Hős modellt.
> * Hozza létre a Hero szolgáltatást a hős adatok leéséhez.
> * Futtassa helyben az alkalmazást.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag megkezdése előtt hajtsa végre a [4.](tutorial-develop-mongodb-nodejs-part4.md)

* Ez az oktatóanyag megköveteli, hogy az Azure CLI helyileg fusson. Az Azure CLI 2.0-s vagy újabb verzióját kell telepíteni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

* Ez az oktatóanyag végigvezeti az alkalmazás lépésről lépésre történő létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="use-mongoose-to-connect"></a>A mongúz ok használata

A Mongoose a MongoDB és a Node.js objektumadat-modellezési (ODM) könyvtára. A Mongúz segítségével csatlakozhat az Azure Cosmos DB-fiókjához. A Mongúz telepítéséhez és az Azure Cosmos DB-hez való csatlakozáshoz az alábbi lépésekkel:

1. Telepítse a mongúz npm modult, amely egy API, amely a MongoDB-val való beszélgetéshez használatos.

    ```bash
    npm i mongoose --save
    ```

1. A **kiszolgáló** mappában hozzon létre egy **mongo.js**nevű fájlt. Ehhez a fájlhoz hozzáadja az Azure Cosmos DB-fiók kapcsolatának részleteit.

1. Másolja a következő kódot a **mongo.js** fájlba. A kód a következő funkciókat biztosítja:

   * A Mongoose használatát igényli.
   * Felülbírálja a Mongo ígéretét, hogy az ES6/ES2015 és újabb verziókba beépített alapvető ígéretet használja.
   * Olyan env fájlt hív meg, amely lehetővé teszi bizonyos dolgok beállítását az alapján, hogy átmeneti, éles vagy fejlesztési területen van-e. Ezt a fájlt a következő szakaszban hozza létre.
   * Tartalmazza a MongoDB kapcsolati karakterláncot, amely az env fájlban van beállítva.
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
    
1. Az Intéző ablaktáblán a **kiszolgáló**csoportban hozzon létre egy mappa nevű **környezetet.** A **környezet** mappában hozzon létre egy **environment.js**nevű fájlt.

1. A mongo.js fájlból meg kell adnunk a `dbName` `cosmosPort` , a és a `key`paraméterek et. Másolja a következő kódot a **environment.js** fájlba:

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

Az alkalmazás csatlakoztatásához az Azure Cosmos DB, frissítenie kell az alkalmazás konfigurációs beállításait. A beállítások frissítéséhez kövesse az alábbi lépéseket: 

1. Az Azure Portalon lekéri a port számát, az Azure Cosmos DB-fiók nevét és az Azure Cosmos DB-fiók elsődleges kulcsértékeit.

1. A **environment.js** fájlban módosítsa `port` az 10255 értéket. 

    ```javascript
    const port = 10255;
    ```

1. A **environment.js** fájlban módosítsa `accountName` az azure Cosmos DB-fiók, amely az [oktatóanyag 4.](tutorial-develop-mongodb-nodejs-part4.md) 

1. Kérje le az Azure Cosmos DB-fiók elsődleges kulcsát az alábbi parancssori paranccsal a terminálablakban: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> az oktatóanyag [4.](tutorial-develop-mongodb-nodejs-part4.md)

1. Másolja az elsődleges kulcsot a **environment.js** fájlba `key` értékként.

Most az alkalmazás rendelkezik az Azure Cosmos DB-hez való csatlakozáshoz szükséges összes információval. 

## <a name="create-a-hero-model"></a>Főképmodell létrehozása

Ezután meg kell határoznia az Azure Cosmos DB-ben tárolandó adatok sémáját egy modellfájl definiálásával. Az alábbi lépésekkel hozzon létre egy _olyan hősmodellt,_ amely meghatározza az adatok sémáját:

1. Az Intéző ablaktáblájának **kiszolgálómappája** alatt hozzon létre egy **hero.model.js**nevű fájlt.

1. Másolja a következő kódot a **hero.model.js** fájlba. A kód a következő funkciókat biztosítja:

   * A Mongoose használatát igényli.
   * Egy új sémát hoz létre egy azonosítóval, egy névvel és egy üzenettel.
   * Modellt hoz létre a séma használatával.
   * Exportálja a modellt. 
   * A **Gyűjtemény Heroes** **(Heros**helyett , amely a mongúz többes elnevezési szabályokon alapuló gyűjtemény alapértelmezett neve).

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

A hősmodell létrehozása után meg kell határoznia egy szolgáltatást az adatok olvasásához, valamint listavégrehajtásához, létrehozása, törléséhez és frissítéséhez. Az alábbi lépésekkel hozzon létre egy _Hero szolgáltatást,_ amely lekérdezi az adatokat az Azure Cosmos DB-ből:

1. Az Intéző ablaktáblájának **kiszolgálómappája** alatt hozzon létre egy **hero.service.js**nevű fájlt.

1. Másolja a következő kódot a **hero.service.js** fájlba. A kód a következő funkciókat biztosítja:

   * Leveszi a létrehozott modellt.
   * Csatlakozik az adatbázishoz.
   * Létrehoz `docquery` egy változót, amely a `hero.find` metódus segítségével definiál ja az összes hőst visszaadó lekérdezést.
   * Futtat egy lekérdezést a funkcióval, azzal az `docquery.exec` ígérettel, hogy megkapja az összes hős listáját, ahol a válasz állapota 200. 
   * A hibaüzenet visszaküldése, ha az állapot 500.
   * Elkapja a hősöket, mert modulokat használunk. 

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

## <a name="configure-routes"></a>Útvonalak konfigurálása

Ezután be kell állítania az útvonalakat a kérelmek lekéréséhez, létrehozásához, olvasásához és törléséhez szükséges URL-címek kezeléséhez. Az útválasztási módszerek visszahívási függvényeket (más néven _kezelőfüggvényeket)_ határoznak meg. Ezeket a függvényeket akkor hívják meg, amikor az alkalmazás kérést kap a megadott végpontra és HTTP metódusra. A Hero szolgáltatás hozzáadásához és az útvonalak meghatározásához kövesse az alábbi lépéseket:

1. A Visual Studio-kód ban a **routes.js** fájlban fűzze hozzá a `res.send` mintahős adatait küldő függvényt. Adjon hozzá egy `heroService.getHeroes` vonalat a függvény hívásához.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. A **routes.js** fájlban `require` a hős szolgáltatás:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. A **hero.service.js** fájlban `getHeroes` frissítse a `req` függvényt úgy, hogy a paraméterek és `res` a paraméterek a következők:

    ```javascript
    function getHeroes(req, res) {
    ```

Vegyünk egy percet, hogy vizsgálja felül, és séta az előző kódot. Először is belépünk az index.js fájlba, amely beállítja a csomópontkiszolgálót. Figyelje meg, hogy beállítja és meghatározza az útvonalakat. Ezután a routes.js fájl beszél a hős szolgáltatást, és azt mondja, hogy kap a funkciók, mint **a getHeroes**, és adja át a kérelmet és választ. A hero.service.js fájl megkapja a modellt, és csatlakozik a Mongo-hoz. Ezután végrehajtja **a getHeroes-t,** amikor hívjuk, és 200-as választ ad vissza. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezután futtassa az alkalmazást az alábbi lépésekkel:

1. A Visual Studio-kódban mentse az összes módosítást. A bal oldalon kattintson a **Debug** ![gomb](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)Hibakeresési ikonjára a Visual Studio Kód ban](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png), majd kattintson a **Hibakeresés indítása** gomb ![Hibakeresés ikonra a Visual Studio Kód jában .

1. Most váltson a böngészőre. Nyissa meg a **Fejlesztőeszközök és** a **Hálózat lapot**. Menj `http://localhost:3000`a , és ott látod a kérelmet.

    ![Új Azure Cosmos DB-fiók az Azure Portalon](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Az alkalmazásban még nincsenek hősök. Az oktatóanyag következő részében felteszed, leküldéses és törlési funkciókat adunk hozzá. Ezután hozzáadhatjuk, frissíthetjük és törölhetjük a hősöket a felhasználói felületről a Mongoose-kapcsolatok használatával az Azure Cosmos adatbázisunkhoz. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokra, törölheti az erőforráscsoportot, az Azure Cosmos DB-fiókot és az összes kapcsolódó erőforrást. Az erőforráscsoport törléséhez kövesse az alábbi lépéseket:

 1. Nyissa meg azt az erőforráscsoportot, amelyhez létrehozta az Azure Cosmos DB-fiókot.
 1. Válassza az **Erőforráscsoport törlése** elemet.
 1. Erősítse meg a törölni kívánt erőforráscsoport nevét, és válassza a **Törlés**gombot.

## <a name="next-steps"></a>További lépések

Folytassa az oktatóanyag 6.

> [!div class="nextstepaction"]
> [6. rész: Post, Put és Delete függvények hozzáadása az alkalmazáshoz](tutorial-develop-mongodb-nodejs-part6.md)
