---
title: Egy Node.js-webalkalmazás létrehozása Azure Cosmos DB SQL API-adatok kezelése a JavaScript SDK segítségével
description: Ez a Node.js-oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Node.js Express-webalkalmazások adatait a Microsoft Azure Cosmos DB segítségével.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: 04f634406eacd05e772d2b672cdfb2af6fb42054
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874481"
---
# <a name="tutorial-build-a-nodejs-web-app-using-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Oktatóanyag: Node.js-webalkalmazás létrehozása a JavaScript SDK használatával az Azure Cosmos DB SQL API-adatok kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Ez a Node.js-oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Cosmos DB SQL API-fiók adatait az Azure Websitesban tárolt Node.js Express-alkalmazás segítségével. Ebben az oktatóanyagban egy egyszerű webalapú alkalmazást (Teendők alkalmazás) fogunk létrehozni, amelyben feladatokat hozhatunk létre, kérhetünk le és fejezhetünk be. A feladatokat JSON-dokumentumok formájában tárolja az Azure Cosmos DB. 

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot az Azure Portal segítségével. Ezután létrehoz és futtat egy Node.js SDK-alapú webalkalmazást, amellyel létrehozza az adatbázist és a tárolót, majd elemeket ad a tárolóhoz. Ez az oktatóanyag a JavaScript SDK 2.0-s verzióját használja.

A teljes mintát beszerezheti a [GitHubról][GitHub] is, és megtekintheti az [információs](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) fájlt az alkalmazás futtatásával kapcsolatos útmutatásért.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása
> * Új Node.js-alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB-hez
> * Az alkalmazás futtatása és üzembe helyezése az Azure-ban

## <a name="_Toc395783176"></a>Előfeltételek

A jelen cikkben lévő utasítások követése előtt győződjön meg arról, hogy rendelkezik az alábbi erőforrásokkal:

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* A [Node.js][Node.js] 6.10-es vagy újabb verziója.
* [Express generator](http://www.expressjs.com/starter/generator.html) (az Expresst az `npm install express-generator -g` segítségével telepítheti)
* Telepítse a [Git][Git] szoftvert a helyi munkaállomáson.

## <a name="_Toc395637761"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik fiókkal, vagy az oktatóanyagban az Azure Cosmos DB Emulatort használja, továbbléphet a [2. lépés: Új Node.js-alkalmazás létrehozása](#_Toc395783178) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>2. lépés: Új Node.js-alkalmazás létrehozása
Most megtanulhatja, hogyan hozhat létre egy alapszintű Hello World Node.js-projektet az [Express](http://expressjs.com/)-keretrendszer használatával.

1. Nyissa meg kedvenc terminálját, például a Node.js parancssort.

1. Keresse meg azt a könyvtárat, amelyben tárolni szeretné az új alkalmazást.

1. Az Express generátor használatával hozzon létre egy új alkalmazást **todo** (teendők) néven.

   ```bash
   express todo
   ```

1. Nyissa meg a **todo** könyvtárat, és telepítse a függőségeket.

   ```bash
   cd todo
   npm install
   ```

1. Futtassa az új alkalmazást.

   ```bash
   npm start
   ```

1. Az új alkalmazás megtekintéséhez navigáljon a böngészőben a következő címre: [http://localhost:3000](http://localhost:3000).
   
   ![A Node.js megismerése – Képernyőfelvétel a Hello World alkalmazásról egy böngészőablakban](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Az alkalmazás leállításához nyomja le a CTRL+C billentyűkombinációt a terminálablakban, a kötegelt feladat leállításához pedig válassza az **y** elemet.

## <a name="_Toc395783179"></a>3. lépés: A szükséges modulok telepítése

A **package.json** fájl egyike azon fájloknak, amelyek a projekt gyökérmappájában létrejönnek. Ez a fájl tartalmazza a Node.js-alkalmazáshoz szükséges további modulok listáját. Amikor az Azure-ba telepíti az alkalmazást, a rendszer ennek a fájlnak a segítségével határozza meg, hogy melyik modulokat kell az Azure-ban telepíteni ahhoz, hogy működjön az alkalmazás. A jelen oktatóanyagban telepítsen még két csomagot.

1. Nyissa meg a terminált, és telepítse az **async** modult az npm segítségével.

   ```bash
   npm install async --save
   ```

2. Telepítse a **@azure/cosmos** modult az npm segítségével. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>4. lépés: A Node.js-alkalmazás csatlakoztatása az Azure Cosmos DB-hez
Miután most végrehajtottuk a kezdeti telepítést és konfigurálást, megírjuk a kódot, amelyre a Teendők alkalmazásnak szüksége van az Azure Cosmos DB-vel való kommunikációhoz.

### <a name="create-the-model"></a>A modell létrehozása
1. A projektkönyvtár gyökerében hozzon létre egy új könyvtárat **models** (modellek) néven.  

2. A **models** könyvtárban hozzon létre egy új fájlt **taskDao.js** néven. Ez a fájl tartalmazza az adatbázis és a tároló létrehozásához szükséges kódot, és definiálja a feladatok az Azure Cosmos DB-ben való olvasásához, frissítéséhez, létrehozásához és kereséséhez használt metódusokat. 

3. Másolja be az alábbi kódot a **taskDao.js** fájlba

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Mentse és zárja be a **taskDao.js** fájlt.  

### <a name="create-the-controller"></a>A vezérlő létrehozása

1. A projekt **routes** könyvtárában hozzon létre egy új fájlt **tasklist.js** néven.  

2. Adja hozzá a következő kódot a **tasklist.js** fájlhoz. Ez a kód betölti a **tasklist.js** fájl által használt CosmosClient és async modult. A kód a **TaskList** (Feladatlista) osztályt is meghatározza, amelyet a rendszer a korábban definiált **TaskDao** objektum példányába továbbít:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Mentse és zárja be a **tasklist.js** fájlt.

### <a name="add-configjs"></a>A config.js fájl hozzáadása

1. A projektkönyvtár gyökerében hozzon létre egy új fájlt **config.js** néven. 

2. Adja hozzá a következő kódot a **config.js** fájlhoz. Ez a kód határozza meg az alkalmazáshoz szükséges konfigurációs beállításokat és értékeket.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. A **config.js** fájlban frissítse a HOST és az AUTH_KEY értékeket azokkal az értékekkel, amelyeket a [Microsoft Azure Portalon](https://portal.azure.com) lévő Azure Cosmos DB-fiókjának Kulcsok oldalán talál. 

4. Mentse és zárja be a **config.js** fájlt.

### <a name="modify-appjs"></a>Az app.js fájl módosítása

1. A projekt könyvtárában nyissa meg az **app.js** fájlt. Ez a fájl korábban, az Express-webalkalmazás létrehozásakor jött létre.  

2. Adja hozzá a következő kódot az **app.js** fájlhoz. Ez a kód fogja meghatározni a használni kívánt konfigurációs fájlt, és betölteni az értékeket néhány változóba, amelyekre a következő szakaszokban szüksége lesz. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Végül mentse és zárja be az **app.js** fájlt.

## <a name="_Toc395783181"></a>5. lépés: Felhasználói felület létrehozása

Most hozzuk létre a felhasználói felületet, hogy a felhasználók használatba vehessék az alkalmazást. Az előző szakaszokban létrehozott Express-alkalmazás a **Jade** megjelenítési motort használja. A Jade motorral kapcsolatos további információkért tekintse meg a [Jade language](http://jade-lang.com/) (Jade nyelv) oldalt.

1. A rendszer a **views** (nézetek) könyvtárban található **layout.jade** fájlt használja a többi **.jade** fájl globális sablonjaként. Ebben a lépésben ezt a sablont a [Twitter Bootstrap](https://github.com/twbs/bootstrap) eszközkészletre módosítja majd, amellyel webhelyeket tervezhet.  

2. Nyissa meg a **views** (nézetek) mappában található **layout.jade** fájlt, és cserélje ki annak tartalmát az alábbi kódra:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Ez megmondja a **Jade** motornak, hogy rendereljen HTML-kódot az alkalmazás számára, és létrehoz egy **content** (tartalom) nevű **blokkot**, ahol megadhatja a tartalomoldalak elrendezését. Mentse és zárja be a **layout.jade** fájlt.

3. Most nyissa meg az **index.jade** fájlt, az alkalmazás által használt nézetet, és cserélje ki a fájl tartalmát az alábbi kódra:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Ez a kód kibővíti az elrendezést, és tartalmat biztosít a **layout.jade** fájlban az imént látott **content** (tartalom) helyőrző számára. Ebben az elrendezésben két HTML-űrlapot hoztunk létre.

Az első űrlap az adatok táblázatát, valamint egy gombot tartalmaz, amely lehetővé teszi az elemek frissítését úgy, hogy elküldi őket a vezérlő **/completeTask** metódusának.
    
A második űrlap két beviteli mezőt és egy gombot tartalmaz, amely lehetővé teszi új elemek létrehozását úgy, hogy elküldi azokat a vezérlő **/addtask** metódusának. Az alkalmazás működéséhez csak ennyire van szükség.

## <a name="_Toc395783181"></a>6. lépés: Az alkalmazás helyileg történő futtatása

1. Ha a helyi gépén szeretné tesztelni az alkalmazást, futtassa az `npm start` parancsot a terminálon az alkalmazás elindításához, majd frissítse a [http://localhost:3000](http://localhost:3000) böngészőoldalt. Az oldalnak az alábbi képernyőképen látható módon kell kinéznie:
   
    ![Képernyőfelvétel a My Todo List (Saját teendőlista) alkalmazásról egy böngészőablakban](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Ha olyan hibaüzenetet kap, amely a layout.jade fájlban vagy az index.jade fájlban lévő behúzásra vonatkozik, győződjön meg arról, hogy az első két sor mindkét fájlban balra zárt, és nem tartalmaz szóközt. Ha szóközök kerültek az első két sor elé, távolítsa el őket, mentse mindkét fájlt, és frissítse a böngészőablakot. 

2. Adjon meg egy új feladatot az Item (Elem), az Item Name (Elem neve) és a Category (Kategória) mezőkben, majd válassza az **Add Item** (Elem hozzáadása) lehetőséget. Ez egy dokumentumot hoz létre az Azure Cosmos DB-ben a megadott tulajdonságokkal. 

3. Az oldal ekkor frissül, és megjeleníti az újonnan létrehozott elemet a teendőlistában.
   
    ![Képernyőfelvétel az alkalmazásról és a teendőlista új eleméről](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. A feladatok elvégzéséhez jelölje be a jelölőnégyzetet a Complete (Elvégezve) oszlopban, majd válassza az **Update tasks** (Feladatok frissítése) lehetőséget. Ez frissíti a már létrehozott dokumentumot, és eltávolítja a nézetből.

5. Az alkalmazás leállításához nyomja le a CTRL+C billentyűkombinációt a terminálablakban, majd a kötegelt feladat leállításához válassza az **Y** elemet.

## <a name="_Toc395783182"></a>7. lépés: Az alkalmazás telepítése az Azure Websitesra

1. Ha még nem tette meg, engedélyezzen egy Git-adattárat az Azure Websites számára. A Git-adattár engedélyezésével kapcsolatos útmutatót a [Local Git Deployment to Azure App Service](../app-service/app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben) című témakörben talál.

2. Adja hozzá Azure-webhelyét távoli Git-elemként.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Helyezze üzembe az alkalmazást a távoli mappához küldéssel.
   
   ```bash
   git push azure master
   ```

4. Néhány másodpercen belül az alkalmazás közzé lesz téve és el lesz indítva egy böngészőben.

Ha szeretné letölteni vagy megtekinteni az oktatóanyagban szereplő teljes referenciaalkalmazást, letöltheti a [GitHubról][GitHub].

## <a name="_Toc395637775"></a>Következő lépések

Ebben az oktatóanyagban megismerhette, hogyan hozhat létre Node.js-webalkalmazást a JavaScript SDK használatával az Azure Cosmos DB SQL API-adatok kezeléséhez. Továbbléphet a következő cikkre:

> [!div class="nextstepaction"]
> [Mobilalkalmazások létrehozása a Xamarin és az Azure Cosmos DB használatával](mobile-apps-with-xamarin.md)


[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

