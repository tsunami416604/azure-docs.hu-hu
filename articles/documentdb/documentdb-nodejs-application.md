---
title: A Node.js megismerése – DocumentDB Node.js oktatóanyag | Microsoft Docs
description: A Node.js megismerése Ez az oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Node.js Express-webalkalmazások adatait a Microsoft Azure DocumentDB segítségével.
keywords: Alkalmazásfejlesztés, adatbázis-oktatóanyag, a node.js megismerése, node.js-oktatóanyag, documentdb, azure, Microsoft Azure
services: documentdb
documentationcenter: nodejs
author: syamkmsft
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 08/25/2016
ms.author: syamk

---
# <a name="_Toc395783175"></a>Node.js-webalkalmazás létrehozása a DocumentDB használatával
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Ez a Node.js-webalkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Node.js Express-alkalmazás adatait az Azure DocumentDB segítségével.

Azt javasoljuk, hogy kezdésként tekintse meg az alábbi videót, amelyből megtudhatja, hogyan oszthat ki egy Azure DocumentDB-adatbázisfiókot, valamint tárolhat JSON-dokumentumokat Node.js-alkalmazásában. 

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Demo-Getting-started-with-Azure-DocumentDB-on-Nodejs-in-Linux/player]
> 
> 

Ezt követően térjen vissza a jelen Node.js-oktatóanyaghoz, amelyben az alábbi kérdésekre kaphat választ:

* Hogyan használhatom a documentdb npm modult a DocumentDB szolgáltatással folytatott munkához?
* Hogyan telepíthetem a webalkalmazást az Azure Websitesra?

A jelen adatbázis-oktatóanyagban leírtak követésével egy olyan egyszerű, webalapú feladatkezelő alkalmazást hoz majd létre, amellyel feladatokat hozhat létre, kérhet le és végezhet el. A feladatok JSON-dokumentumokként lesznek tárolva az Azure DocumentDB-ben.

![Képernyőfelvétel a jelen Node.js oktatóanyag során készített My Todo List (Saját teendőlista) alkalmazásról](./media/documentdb-nodejs-application/image1.png)

Nincs ideje elvégezni az oktatóanyagot, és csak hozzá szeretne jutni a teljes megoldáshoz? A teljes megoldást beszerezheti a [GitHub][GitHub].

## <a name="_Toc395783176"></a>Előfeltételek
> [!TIP]
> Ez a Node.js-oktatóanyag feltételezi, hogy rendelkezik némi tapasztalattal a Node.js és az Azure Websites használatát illetően.
> 
> 

A jelen cikkben lévő utasítások követése előtt rendelkeznie kell a következőkkel:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* [Node.js][Node.js]-verzió: 0.10.29-s vagy újabb.
* [Express generátor](http://www.expressjs.com/starter/generator.html) (az `npm install express-generator -g` segítségével telepítheti)
* [Git][Git].

## <a name="_Toc395637761"></a>1. lépés: DocumentDB-adatbázisfiók létrehozása
Először hozzon létre egy DocumentDB-fiókot. Ha már rendelkezik fiókkal, továbbléphet a [2. lépés: Új Node.js-alkalmazás létrehozása](#_Toc395783178) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[!INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>2. lépés: Új Node.js-alkalmazás létrehozása
Most megtanulhatja, hogyan hozhat létre egy alapszintű Hello World Node.js-projektet az [Express](http://expressjs.com/)-keretrendszer használatával.

1. Nyissa meg kedvenc terminálját.
2. Az Express generátor használatával hozzon létre egy új alkalmazást **todo** (teendők) néven.
   
        express todo
3. Nyissa meg az új **todo** könyvtárat, és telepítse a függőségeket.
   
        cd todo
        npm install
4. Futtassa az új alkalmazást.
   
        npm start
5. Az új alkalmazás megtekintéséhez navigáljon a böngészőben a következő címre: [http://localhost:3000](http://localhost:3000).
   
    ![A Node.js megismerése – Képernyőfelvétel a Hello World alkalmazásról egy böngészőablakban](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>3. lépés: További modulok telepítése
A **package.json** fájl egyike azon fájloknak, amelyek a projekt gyökérmappájában létrejönnek. Ez a fájl tartalmazza a Node.js-alkalmazáshoz szükséges további modulok listáját. Később, amikor egy Azure Websitesra telepíti az alkalmazást, a rendszer ennek a fájlnak a segítségével határozza meg, hogy melyik modulokat kell az Azure-ban telepíteni ahhoz, hogy működjön az alkalmazás. A jelen oktatóanyag befejezéséhez még két csomag telepítésére van szükség.

1. A terminálban telepítse az **async** modult az npm segítségével.
   
        npm install async --save
2. Telepítse a **DocumentDB** modult az npm segítségével. Ez az a modul, amelyben a DocumentDB-vel kapcsolatos csodák történnek.
   
        npm install documentdb --save
3. Ha gyorsan megtekinti a **package.json** fájl tartalmát, láthatja is a további modulokat. Ez a fájl utasítja az Azure-t az alkalmazás futtatásakor szükséges csomagok letöltésére és telepítésére. Ennek az alábbi példához hasonlóan kell kinéznie.
   
    ![Képernyőfelvétel a package.json lapról](./media/documentdb-nodejs-application/image17.png)
   
    Ez értesíti a Node-ot (majd később az Azure-t) arról, hogy az alkalmazás ezektől a további moduloktól függ.

## <a name="_Toc395783180"></a>4. lépés: A DocumentDB szolgáltatás használata Node.js-alkalmazásokban
Ezzel a kezdeti beállítás és konfiguráció készen is van. Ideje elkezdeni a kódírást az Azure DocumentDB használatával.

### <a name="create-the-model"></a>A modell létrehozása
1. A projektkönyvtárban hozzon létre egy új könyvtárat **models** (modellek) néven.
2. A **models** könyvtárban hozzon létre egy új fájlt **taskDao.js** néven. Ez a fájl tartalmazza majd a modellt az alkalmazás által létrehozott feladatok számára.
3. Ugyanabban a **models** könyvtárban hozzon létre egy másik új fájlt **docdbUtils.js** néven. Ez a fájl néhány hasznos, újrafelhasználható, az alkalmazás minden területén használt kódot tartalmaz majd.. 
4. Másolja be az alábbi kódot a **docdbUtils.js** fájlba
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
   
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
   
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
   
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };             
   
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
   
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
   
        module.exports = DocDBUtils;
   
   > [!TIP]
   > A createCollection metódus tartalmazhat egy opcionális requestOptions paramétert is, amellyel megadható a gyűjtemény ajánlattípusa. Ha nem ad meg értéket a requestOptions.offerType paraméter esetében, a gyűjtemény az alapértelmezett ajánlattípus használatával jön létre.
   > 
   > A DocumentDB ajánlattípusaival kapcsolatos további információkért lásd: [Performance levels in DocumentDB](documentdb-performance-levels.md) (A DocumentDB teljesítményszintjei) 
   > 
   > 
5. Mentse és zárja be a **docdbUtils.js** fájlt.
6. A **taskDao.js** fájl elejéhez adja hozzá a következő kódot a **DocumentDBClient**-ügyfélre és a fentiekben létrehozott **docdbUtils.js** fájlra való hivatkozáshoz:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. Ezután adja hozzá a feladatobjektum meghatározására és exportálására használt kódot. Ez felelős a feladatobjektum elindításáért, valamint a használni kívánt adatbázis és dokumentumgyűjtemény beállításáért.
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. Ezután adja hozzá a következő kódot a feladatobjektumokhoz további metódusok meghatározásához, amelyek lehetővé teszik majd a DocumentDB-ben tárolt adatokkal folytatott interakciót.
   
        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
   
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
   
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
   
            find: function (querySpec, callback) {
                var self = this;
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results);
                    }
                });
            },
   
            addItem: function (item, callback) {
                var self = this;
   
                item.date = Date.now();
                item.completed = false;
   
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, doc);
                    }
                });
            },
   
            updateItem: function (itemId, callback) {
                var self = this;
   
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        doc.completed = true;
   
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
   
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
   
            getItem: function (itemId, callback) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };
9. Mentse és zárja be a **taskDao.js** fájlt. 

### <a name="create-the-controller"></a>A vezérlő létrehozása
1. A projekt **routes** könyvtárában hozzon létre egy új fájlt **tasklist.js** néven. 
2. Adja hozzá a következő kódot a **tasklist.js** fájlhoz. Ez betölti a **tasklist.js** fájl által használt DocumentDBClient és async modult. Emellett a **TaskList** (Feladatlista) függvényt is meghatározta, amelyet a rendszer továbbad a **Task** (Feladat) objektum korábban meghatározott példányának:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
   
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
   
        module.exports = TaskList;
3. Folytassa a **tasklist.js** fájlhoz való hozzáadást a **showTasks, addTasks** és **completeTasks** által használt metódusok hozzáadásával.
   
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
   
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
   
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
   
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
   
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
   
                    res.redirect('/');
                });
            },
   
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
   
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };
4. Mentse és zárja be a **tasklist.js** fájlt.

### <a name="add-config.js"></a>A config.js fájl hozzáadása
1. A projektkönyvtárban hozzon létre egy új fájlt **config.js** néven.
2. Adja hozzá a következőket a **config.js** fájlhoz. Ez meghatározza az alkalmazáshoz szükséges konfigurációs beállításokat és értékeket.
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. A **config.js** fájlban frissítse a HOST és AUTH_KEY értékeket a [Microsoft Azure portálon](https://portal.azure.com), a DocumentDB-fiókjának Kulcsok panelén található értékekkel.
4. Mentse és zárja be a **config.js** fájlt.

### <a name="modify-app.js"></a>Az app.js fájl módosítása
1. A projekt könyvtárában nyissa meg az **app.js** fájlt. Ez a fájl korábban, az Express-webalkalmazás létrehozásakor jött létre.
2. Adja hozzá a következő kódot az **app.js** fájl elejéhez
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. Ez a kód határozza meg a használni kívánt konfigurációs fájlt, és olvassa ki az értékeket abból néhány változó számára, amelyeket hamarosan használni fog.
4. Cserélje ki az **app.js** fájl alábbi két sorát:
   
        app.use('/', routes);
        app.use('/users', users); 
   
      a következő kódtöredékre:
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. Ezek a sorok meghatározzák a **TaskDao** objektum egy új példányát, amely egy új (a **config.js** fájlból kiolvasott értékek felhasználásával létesített) kapcsolattal csatlakozik a DocumentDB-adatbázishoz. Továbbá ezek inicializálják a feladatobjektumot, majd társítanak űrlapműveleteket a metódusokhoz a **TaskList**-vezérlőn. 
6. Végül mentse és zárja be az **app.js** fájlt. És már majdnem készen is van.

## <a name="_Toc395783181"></a>5. lépés: Felhasználói felület létrehozása
Most térjünk át a felhasználói felület létrehozására, hogy a felhasználók ténylegesen használatba vehessék az alkalmazást. A létrehozott Express-alkalmazás a **Jade** megjelenítési motort használja. A Jade motorral kapcsolatos további információkért lásd: [http://jade-lang.com/](http://jade-lang.com/).

1. A rendszer a **views** (nézetek) könyvtárban található **layout.jade** fájlt használja a többi **.jade** fájl globális sablonjaként. Ebben a lépésben ezt a sablont a [Twitter Bootstrap](https://github.com/twbs/bootstrap) eszközkészletre módosítja majd, amellyel könnyen tervezhet tetszetős webhelyeket. 
2. Nyissa meg a **views** (nézetek) mappában található **layout.jade** fájlt, majd cserélje ki annak tartalmát a következőre:
   
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

    Ez gyakorlatilag megmondja a **Jade** motornak, hogy rendereljen HTML-kódot az alkalmazás számára, és létrehoz egy **content** (tartalom) nevű **blokkot**, ahol megadhatja a tartalomoldalak elrendezését.
    Mentse és zárja be a **layout.jade** fájlt.

1. Most nyissa meg az **index.jade** fájlt, az alkalmazás által használt nézetet, és cserélje ki a fájl tartalmát az alábbira:
   
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
                      input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
            button.btn(type="submit") Update tasks
          hr
          form.well(action="/addtask", method="post")
            label Item Name:
            input(name="name", type="textbox")
            label Item Category:
            input(name="category", type="textbox")
            br
            button.btn(type="submit") Add item
   
    Ez kibővíti az elrendezést, és tartalmat biztosít a **layout.jade** fájlban az imént látott **content** (tartalom) helyőrző számára.
   
    Ebben az elrendezésben két HTML-űrlapot hoztunk létre. 
    Az első űrlap az adatok táblázatát, valamint egy gombot tartalmaz, amely lehetővé teszi az elemek frissítését úgy, hogy elküldi azokat a vezérlő **/completetask** metódusának.
    A második űrlap két beviteli mezőt és egy gombot tartalmaz, amely lehetővé teszi új elemek létrehozását úgy, hogy elküldi azokat a vezérlő **/addtask** metódusának.
   
    Az alkalmazás működéséhez csak ennyire van szükség.
2. Nyissa meg a **public\stylesheets** könyvtárban található **style.css** fájlt, és cserélje ki a kódot a következőre:
   
        body {
          padding: 50px;
          font: 14px "Lucida Grande", Helvetica, Arial, sans-serif;
        }
        a {
          color: #00B7FF;
        }
        .well label {
          display: block;
        }
        .well input {
          margin-bottom: 5px;
        }
        .btn {
          margin-top: 5px;
          border: outset 1px #C8C8C8;
        }
   
    Mentse és zárja be a **style.css** fájlt.

## <a name="_Toc395783181"></a>6. lépés: Az alkalmazás helyileg történő futtatása
1. Ha tesztelni szeretné az alkalmazást a helyi gépén, futtassa az `npm start` parancsot egy terminálban az alkalmazás elindításához és egy böngésző megnyitásához az alábbi illusztráción láthatóhoz hasonló oldallal:
   
    ![Képernyőfelvétel a My Todo List (Saját teendőlista) alkalmazásról egy böngészőablakban](./media/documentdb-nodejs-application/image18.png)
2. Adja meg az adatokat az Item (Elem), Item Name (Elem neve) és Category (Kategória) mezőkben, majd kattintson az **Add Item** (Elem hozzáadása) lehetőségre.
3. Az oldal ekkor frissül, és megjeleníti az újonnan létrehozott elemet a teendőlistában.
   
    ![Képernyőfelvétel az alkalmazásról és a teendőlista új eleméről](./media/documentdb-nodejs-application/image19.png)
4. A feladatok elvégzéséhez egyszerűen jelölje be a jelölőnégyzetet a Complete (Elvégezve) oszlopban, majd kattintson az **Update tasks** (Feladatok frissítése) lehetőségre.

## <a name="_Toc395783182"></a>7. lépés: Az alkalmazásfejlesztési projekt üzembe helyezése az Azure Websites-ban
1. Ha még nem tette meg, engedélyezzen egy Git-tárházat az Azure Websites számára. Ehhez a következő témakörben találhat útmutatót: [Local Git Deployment to Azure App Service](../app-service-web/app-service-deploy-local-git.md) (Helyi Git-üzembehelyezés az Azure App Service-ben).
2. Adja hozzá Azure-webhelyét távoli Git-elemként.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Helyezze üzembe a tárházat a távoli mappához küldéssel.
   
        git push azure master
4. Néhány másodpercen belül a Git befejezi a webalkalmazás közzétételét, és elindít egy böngészőt, ahol láthatja az Azure-on futó munkáját.

## <a name="_Toc395637775"></a>Következő lépések
Gratulálunk! Létrehozta az első Node.js Express-webalkalmazását az Azure DocumentDB használatával, és közzétette azt az Azure Websiteson.

A teljes referenciaalkalmazás forráskódja letölthető a [Github][Github].

További információk: [Node.js fejlesztői központ](https://azure.microsoft.com/develop/nodejs/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[Github]: https://github.com/Azure-Samples/documentdb-node-todo-app




<!--HONumber=Oct16_HO3-->


