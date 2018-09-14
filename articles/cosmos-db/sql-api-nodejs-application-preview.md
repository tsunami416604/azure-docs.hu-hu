---
title: Node.js-webalkalmazás létrehozása az Azure Cosmos DB-hez | Microsoft Docs
description: Ez a Node.js-oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Node.js Express-webalkalmazások adatait a Microsoft Azure Cosmos DB segítségével.
keywords: Alkalmazásfejlesztés, adatbázis-oktatóanyag, a Node.js megismerése, Node.js-oktatóanyag
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 8e788207e99a87e9635fbf668ad99c21ca101ecf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697115"
---
# <a name="_Toc395783175"></a>Node.js-webalkalmazás létrehozása az Azure Cosmos DB és a Node.js SDK (előzetes verzió) használatával

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js – v2](sql-api-nodejs-application-preview.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Ez a Node.js-oktatóanyag bemutatja, miként tárolhatja és érheti el az Azure Websitesban tárolt Node.js Express-alkalmazás adatait az Azure Cosmos DB SQL API-fiók segítségével. Ebben az oktatóanyagban egy egyszerű webalapú alkalmazást (Teendők alkalmazás) fogunk létrehozni, amelyben feladatokat hozhatunk létre, kérhetünk le és fejezhetünk be. A feladatokat JSON-dokumentumok formájában tárolja az Azure Cosmos DB. Az alábbi képen a Teendők alkalmazás képernyőképe látható:

![Képernyőfelvétel a jelen Node.js oktatóanyag során készített My Todo List (Saját teendőlista) alkalmazásról](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Cosmos DB SQL API-fiókot az Azure Portal segítségével. Ezután létrehoz és futtat egy Node.js SDK-alapú webalkalmazást, amellyel létrehozza az adatbázist és a tárolót, majd elemeket ad a tárolóhoz. Az oktatóanyag a Node.js SDK 2.0-s verzióját használja, amely jelenleg előzetes verzióban érhető el.

A teljes mintát beszerezheti a [GitHubról][GitHub] is. Az alkalmazás futtatásához szükséges útmutatást az [Olvass el](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) fájlban találja.

## <a name="_Toc395783176"></a>Előfeltételek

A jelen cikkben lévő utasítások követése előtt rendelkeznie kell a következőkkel:

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* A [Node.js][Node.js] 6.10-es vagy újabb verziója.
* [Express generátor](http://www.expressjs.com/starter/generator.html) (az `npm install express-generator -g` segítségével telepítheti)
* [Git][Git].

## <a name="_Toc395637761"></a>1. lépés: Azure Cosmos DB-adatbázisfiók létrehozása
Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik fiókkal, vagy az oktatóanyagban az Azure Cosmos DB Emulatort használja, továbbléphet a [2. lépés: Új Node.js-alkalmazás létrehozása](#_Toc395783178) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>2. lépés: Új Node.js-alkalmazás létrehozása
Most megtanulhatja, hogyan hozhat létre egy alapszintű Hello World Node.js-projektet az [Express](http://expressjs.com/)-keretrendszer használatával.

1. Nyissa meg kedvenc terminálját, például a Node.js parancssort.
2. Keresse meg azt a könyvtárat, amelyben tárolni szeretné az új alkalmazást.
3. Az Express generátor használatával hozzon létre egy új alkalmazást **todo** (teendők) néven.

   ```bash
   express todo
   ```
4. Nyissa meg az új **todo** könyvtárat, és telepítse a függőségeket.

   ```bash
   cd todo
   npm install
   ```
5. Futtassa az új alkalmazást.

   ```bash
   npm start
   ```

6. Az új alkalmazás megtekintéséhez navigáljon a böngészőben a következő címre: [http://localhost:3000](http://localhost:3000).
   
    ![A Node.js megismerése – Képernyőfelvétel a Hello World alkalmazásról egy böngészőablakban](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Az alkalmazás leállításához nyomja le a CTRL+C billentyűkombinációt a terminálablakban, a kötegelt feladat leállításához pedig kattintson az **y** elemre.

## <a name="_Toc395783179"></a>3. lépés: A szükséges modulok telepítése

A **package.json** fájl egyike azon fájloknak, amelyek a projekt gyökérmappájában létrejönnek. Ez a fájl tartalmazza a Node.js-alkalmazáshoz szükséges további modulok listáját. Később, amikor az Azure Websitesra telepíti az alkalmazást, a rendszer ennek a fájlnak a segítségével határozza meg, hogy melyik modulokat kell az Azure-ban telepíteni ahhoz, hogy működjön az alkalmazás. A jelen oktatóanyaghoz még két csomag telepítése szükséges.

1. Nyissa meg a terminált, és telepítse az **async** modult az npm segítségével.

   ```bash
   npm install async --save
   ```

2. Telepítse a **@azure/cosmos** modult az npm segítségével. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>4. lépés: Az Azure Cosmos DB szolgáltatás használata Node.js-alkalmazásokban
Miután most végrehajtottuk a kezdeti telepítést és konfigurálást, megírjuk a kódot, amelyre a Teendők alkalmazásnak szüksége van az Azure Cosmos DB-vel való kommunikációhoz.

### <a name="create-the-model"></a>A modell létrehozása
1. A projektkönyvtár gyökerében hozzon létre egy új könyvtárat **models** (modellek) néven.  

2. A **models** könyvtárban hozzon létre egy új fájlt **taskDao.js** néven. Ez a fájl tartalmazza az adatbázis és a tároló létrehozásához szükséges kódot, és definiálja a feladatok az Azure Cosmos DB-ben való olvasásához, frissítéséhez, létrehozásához és kereséséhez használt metódusokat. 

3. Másolja be az alábbi kódot a **taskDao.js** fájlba.

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

2. Adja hozzá a következő kódot a **tasklist.js** fájlhoz. Ez betölti a **tasklist.js** fájl által használt CosmosClient és async modult. Emellett a **TaskList** (Feladatlista) osztályt is meghatározza, amelyet a rendszer a korábban definiált **TaskDao** objektum példányába továbbít:
   
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

2. Adja hozzá a következő kódot az **app.js** fájlhoz. Ez a kód fogja meghatározni a használni kívánt konfigurációs fájlt, és kiolvasni belőle az értékeket néhány változóhoz, amelyekre hamarosan szükség lesz. 
   
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

3. Végül mentse és zárja be az **app.js** fájlt. És már majdnem készen is van.

## <a name="_Toc395783181"></a>5. lépés: Felhasználói felület létrehozása
Most térjünk át a felhasználói felület létrehozására, hogy a felhasználók ténylegesen használatba vehessék az alkalmazást. A létrehozott Express-alkalmazás a **Jade** megjelenítési motort használja. A Jade motorral kapcsolatos további információkért lásd: [http://jade-lang.com/](http://jade-lang.com/).

1. A rendszer a **views** (nézetek) könyvtárban található **layout.jade** fájlt használja a többi **.jade** fájl globális sablonjaként. Ebben a lépésben ezt a sablont a [Twitter Bootstrap](https://github.com/twbs/bootstrap) eszközkészletre módosítja majd, amellyel könnyen tervezhet tetszetős webhelyeket.  

2. Nyissa meg a **views** (nézetek) mappában található **layout.jade** fájlt, és cserélje ki annak tartalmát a következőre:

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

    Ez gyakorlatilag megmondja a **Jade** motornak, hogy rendereljen HTML-kódot az alkalmazás számára, és létrehoz egy **content** (tartalom) nevű **blokkot**, ahol megadhatja a tartalomoldalak elrendezését.

    Mentse és zárja be a **layout.jade** fájlt.

3. Most nyissa meg az **index.jade** fájlt, az alkalmazás által használt nézetet, és cserélje ki a fájl tartalmát az alábbira:

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

Ez kibővíti az elrendezést, és tartalmat biztosít a **layout.jade** fájlban az imént látott **content** (tartalom) helyőrző számára.
   
Ebben az elrendezésben két HTML-űrlapot hoztunk létre.

Az első űrlap az adatok táblázatát, valamint egy gombot tartalmaz, amely lehetővé teszi az elemek frissítését úgy, hogy elküldi őket a vezérlő **/completetask** metódusának.
    
A második űrlap két beviteli mezőt és egy gombot tartalmaz, amely lehetővé teszi új elemek létrehozását úgy, hogy elküldi azokat a vezérlő **/addtask** metódusának.

Az alkalmazás működéséhez csak ennyire van szükség.

## <a name="_Toc395783181"></a>6. lépés: Az alkalmazás helyileg történő futtatása
1. Ha a helyi gépén szeretné tesztelni az alkalmazást, futtassa az `npm start` parancsot a terminálon az alkalmazás elindításához, majd frissítse a [http://localhost:3000](http://localhost:3000) böngészőoldalt. Az oldalnak most úgy kell kinéznie, ahogy az alábbi képen látható:
   
    ![Képernyőfelvétel a My Todo List (Saját teendőlista) alkalmazásról egy böngészőablakban](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Ha olyan hibaüzenetet kap, amely a layout.jade fájlban vagy az index.jade fájlban lévő behúzásra vonatkozik, győződjön meg arról, hogy az első két sor mindkét fájlban balra zárt, és nem tartalmaz szóközt. Ha szóközök kerültek az első két sor elé, távolítsa el őket, mentse mindkét fájlt, és frissítse a böngészőablakot. 

2. Adjon meg egy új feladatot az Item (Elem), az Item Name (Elem neve) és a Category (Kategória) mezőkben, majd kattintson az **Add Item** (Elem hozzáadása) lehetőségre. Ez egy új dokumentumot hoz létre az Azure Cosmos DB-ben a megadott tulajdonságokkal. 
3. Az oldal ekkor frissül, és megjeleníti az újonnan létrehozott elemet a teendőlistában.
   
    ![Képernyőfelvétel az alkalmazásról és a teendőlista új eleméről](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. A feladatok elvégzéséhez egyszerűen jelölje be a jelölőnégyzetet a Complete (Elvégezve) oszlopban, majd kattintson az **Update tasks** (Feladatok frissítése) lehetőségre. Ez frissíti a már létrehozott dokumentumot, és eltávolítja azt a nézetből.

5. Az alkalmazás leállításához nyomja le a CTRL+C billentyűkombinációt a terminálablakban, majd a kötegelt feladat leállításához kattintson az **Y** elemre.

## <a name="_Toc395783182"></a>7. lépés: Az alkalmazásfejlesztési projekt üzembe helyezése az Azure Websites-ban
1. Ha még nem tette meg, engedélyezzen egy Git-tárházat az Azure Websites számára. Ehhez a következő témakörben találhat útmutatót: [Local Git Deployment to Azure App Service](../app-service/app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben).
2. Adja hozzá Azure-webhelyét távoli Git-elemként.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Helyezze üzembe a tárházat a távoli mappához küldéssel.
   
        git push azure master
4. Néhány másodpercen belül a Git befejezi a webalkalmazás közzétételét, és elindít egy böngészőt, ahol láthatja az Azure-on futó munkáját!

    Gratulálunk! Létrehozta az első Node.js Express-webalkalmazását az Azure Cosmos DB használatával, és közzétette azt az Azure Websitesban.

    Az oktatóanyaghoz a teljes referenciaalkalmazás letölthető a [GitHubról][GitHub].

## <a name="_Toc395637775"></a>Következő lépések

* Méret- és teljesítménytesztelést szeretne végezni az Azure Cosmos DB használatával? Tekintse meg a következőt: [Teljesítmény- és mérettesztelés az Azure Cosmos DB használatával](performance-testing.md)
* Ismerje meg, hogyan [figyelhet egy Azure Cosmos DB-fiókot](monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* Tekintse át az [Azure Cosmos DB-dokumentációt](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

