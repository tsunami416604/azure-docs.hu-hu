---
title: "A table storage (Node.js) webalkalmazásnál |} Microsoft Docs"
description: "Ez az oktatóanyag Azure Storage szolgáltatás és az Azure-modul hozzáadásával a webalkalmazás az Express oktatóanyag épül."
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: b802f880c1131abb7eb9ba00dd8f2e65017bc802
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="nodejs-web-application-using-storage"></a>Storage használata node.js-webalkalmazás
## <a name="overview"></a>Áttekintés
Ebben az oktatóanyagban az alkalmazás létrehozott a [Express használata Node.js-webalkalmazás] oktatóanyag ki van bővítve használata a Microsoft Azure Klienskódtárak segítségével a Node.js adatok szolgáltatások. Hozzon létre egy webalapú feladatlista alkalmazás, amely központilag telepíthető a Azure terjesztheti ki az alkalmazást. A feladatok lista lehetővé teszi a felhasználóknak feladatokat beolvasni, adja hozzá az új feladatok és feladatok megjelölése befejezettként.

A feladat tárolódnak az Azure Storage. Az Azure Storage biztosítja a hibatűrő és magas rendelkezésre állású strukturálatlan adatok tárhelyet. Az Azure Storage számos adatszerkezetek, ahol tárolhatja és érheti adatait tartalmazza. A tárolási szolgáltatások az API-szerepel az Azure SDK for Node.js vagy a REST API-kon keresztül is használhatja. További információkért lásd: [tárolása és az adatok elérése az Azure-ban].

Ez az oktatóanyag feltételezi, hogy végrehajtotta a [Node.js-webalkalmazás] és [expressz Node.js][Express használata Node.js-webalkalmazás] oktatóanyagok.

A következő adatokat tartalmazza:

* A Jade sablon motor használata
* Azure Data szolgáltatások használata

Az alábbi képernyőfelvételen a kész alkalmazás látható:

![A befejezett weblap az internet Explorerben](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Tárolási hitelesítő adatok beállítása a Web.config fájlban
Át kell adnia a tároló hitelesítő adatok Azure Storage eléréséhez. Ez a web.config Alkalmazásbeállítások felügyelniük történik.
A web.config beállítások átadása pedig a környezeti változók csomópontra, amely majd beolvassa vannak az Azure SDK.

> [!NOTE]
> Tárolási csak szolgálnak, amikor az alkalmazás központi telepítése az Azure-bA. Ha az emulátorban futtatja, az alkalmazás használja a storage emulator.
>
>

Hajtsa végre a tárfiók hitelesítő adatainak lekérésére, és adja hozzá a web.config beállítások az alábbi lépéseket:

1. Ha még nincs nyitva, indítsa el az Azure PowerShell, a a **Start** kibontásával menü **minden program és az Azure-**, kattintson a jobb gombbal **Azure PowerShell**, majd válassza ki a  **Futtatás rendszergazdaként**.
2. Váltson át a mappát, amely tartalmazza az alkalmazás. Például a C:\\csomópont\\tasklist\\WebRole1.
3. Az Azure Powershell ablakban adja meg a következő parancsmagot a tárfiókadatok beolvasása:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts
    ```

   A fenti parancsmag lekéri a listában tárfiókok és a kulcsok az üzemeltetett szolgáltatás társított fiókot.

   > [!NOTE]
   > Mivel az Azure SDK-t hoz tárfiókot, a szolgáltatás telepítésekor, a tárfiók már léteznie kell a az előző útmutatók az alkalmazás központi telepítése.
   >
   >
4. Nyissa meg a **ServiceDefinition.csdef** telepítik az alkalmazást az Azure-bA használt környezet beállításokat tartalmazó fájlt:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Helyezze be a következő kódblokk a **környezet** elemet, és a {TÁRFIÓK} és {TÁRELÉRÉSI kulcs} a fióknevet és a központi telepítéshez használni kívánt tárfiók elsődleges kulcs:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![A web.cloud.config tartalmát](./media/table-storage-cloud-service-nodejs/node37.png)

6. Mentse a fájlt, és zárja be a Jegyzettömböt.

### <a name="install-additional-modules"></a>A kiegészítő modulok telepítése
1. A következő paranccsal telepítse az [azure], [csomópont-uuid], [nconf] és [aszinkron] modulok helyileg, valamint hogy menteni egy bejegyzést, hogy a **package.json** fájlt:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  Ez a parancs a következőhöz hasonlóan kell megjelennie:

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>A Table szolgáltatás használata node.js-alkalmazásokban
Ebben a szakaszban az alapvető alkalmazás hozta létre a **expressz** parancs hozzáadásával ki van bővítve a **task.js** a tevékenységek a modellt tartalmazó fájl. Módosítsa a meglévő **app.js** fájlt, és hozzon létre egy új **tasklist.js** fájlt, amely a modellt használ.

### <a name="create-the-model"></a>A modell létrehozása
1. Az a **WebRole1** könyvtár, hozzon létre egy új könyvtárat nevű **modellek**.
2. Az a **modellek** könyvtár, hozzon létre egy új fájlt **task.js**. Ez a fájl tartalmazza a modell az alkalmazás által létrehozott feladatok számára.
3. Elején a **task.js** fájlt, adja hozzá a következő kódot a szükséges kódtárak hivatkozik:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. Ezután adja hozzá a kód határozza meg, és a feladat objektum exportálása. A feladatobjektum felelős a tábla kapcsolódik.

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. Ezután adja hozzá a következő kódot a feladatobjektumokhoz további metódusok meghatározásához a feladatobjektumot, amelyek lehetővé teszik a táblában tárolt adatok interakció:

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. Mentse és zárja be a **task.js** fájlt.

### <a name="create-the-controller"></a>A vezérlő létrehozása
1. Az a **WebRole1/útvonalak** könyvtár, hozzon létre egy új fájlt **tasklist.js** , majd nyissa meg szövegszerkesztőben.
2. Adja hozzá a következő kódot a **tasklist.js** fájlhoz. Ez a kód betölti az azure és async modult által használt **tasklist.js** , és meghatározzák a **TaskList** függvény, amelyet példánya a **feladat** objektum azt korábban meghatározott:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Folytassa a **tasklist.js** fájl által használt metódusok hozzáadásával **showTasks**, **Addtasks**, és **completeTasks**:

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = req.body.item;
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. Mentse a **tasklist.js** fájlt.

### <a name="modify-appjs"></a>Az app.js fájl módosítása
1. Az a **WebRole1** könyvtár, nyissa meg a **app.js** fájlt egy szövegszerkesztőben.
2. A fájl elején adja hozzá a következő, azure-moduljának betöltése, és állítsa be a táblázat nevét, és a partíció kulcsot:

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. Az app.js fájlban görgessen le, ahol megjelenik a következő sort:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Cserélje le a következő kódot az előző sorokat. Ez a kód egy példányát inicializálja <strong>feladat</strong> a tárfiók kapcsolattal rendelkező. A <strong>feladat</strong> számára a <strong>TaskList</strong>, amely használja azt a Table szolgáltatással való kommunikációra:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Mentse a **app.js** fájlt.

### <a name="modify-the-index-view"></a>Az index nézetről módosítása
1. Lépjen a **nézetek** könyvtárhoz, és nyissa meg a **index.jade** fájlt egy szövegszerkesztőben.
2. Cserélje le a tartalmát a **index.jade** fájl a következő kóddal. Ez a kód határozza meg a nézet a meglévő feladatokat megjelenő, és határozza meg az új feladatok hozzáadása és meglévőket megjelölés befejezettként űrlap.

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks != []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="item[name]", type="textbox")
        label Item Category:
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. Mentse és zárja be **index.jade** fájlt.

### <a name="modify-the-global-layout"></a>A globális elrendezés módosítása
A rendszer a **views** (nézetek) könyvtárban található **layout.jade** fájlt használja a többi **.jade** fájl globális sablonjaként. Ebben a lépésben módosítsa a **Views** használandó [Twitter Bootstrap](https://github.com/twbs/bootstrap), ez az egy eszközkészlet, amely megkönnyíti a töltött tetszetős webhelyeket tervezéséhez.

1. Töltse le és csomagolja ki a fájlokat a [Twitter Bootstrap](http://getbootstrap.com/). Másolás a **bootstrap.min.css** fájlt a **bootstrap\\eloszlás\\css** mappát a **nyilvános\\stíluslapok** az tasklist alkalmazás könyvtár.
2. Az a **nézetek** mappa, nyissa meg a **Views** fájlt a szövegszerkesztőben, és cserélje ki a tartalmát a következőre:

    DOCTYPE html html központi cím cím hivatkozás = (rel = "xsl", href='/stylesheets/bootstrap.min.css) hivatkozás (rel = "xsl", href='/stylesheets/style.css) body.app nav.navbar.navbar alapértelmezett div.navbar-fejléc a.navbar-brand(href='/') a  Feladatok blokkolja a tartalmat

3. Mentse a **Views** fájlt.

### <a name="running-the-application-in-the-emulator"></a>Az alkalmazás futtatása az emulátorban
A következő paranccsal indítsa el az alkalmazást az emulátorban.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

A böngészőben megnyílik, és megjeleníti a következő lapot:

![A webes lapozható saját feladatlista című feladatok és adjon hozzá egy új feladatot mezőket tartalmazó tábla.](./media/table-storage-cloud-service-nodejs/node44.png)

Ezen a képernyőn elemek hozzáadását, vagy távolítsa el a meglévő elemeket befejezettként jelöli meg őket.

## <a name="publishing-the-application-to-azure"></a>Az Azure-bA az alkalmazás közzététele
A Windows PowerShell-ablakban hívja meg a következő parancsmagot újratelepíteni az üzemeltetett szolgáltatás az Azure-bA.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Cserélje le **myuniquename** ehhez az alkalmazáshoz, egyedi névvel. Cserélje le **datacentername** nevű, egy Azure-adatközponthoz, például a **USA nyugati régiója**.

A telepítés befejezése után, a következőhöz hasonló választ kell megjelennie:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

Megadja a **-indítása** az előző parancsmag, a böngésző beállítás megnyílik, és megjeleníti az alkalmazás Azure-beli közzététel befejezésekor.

![A saját feladatlista lapot megjelenítő böngészőablak. Az URL-cím jelzi, az oldal most alatt az Azure-on.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Leállítása és az alkalmazás törlése
Után az alkalmazás telepítéséhez, érdemes lehet le kell tiltani, költségek elkerülése vagy létrehozhatja és más alkalmazások telepítése a ingyenes próba időn belül.

Az Azure a webesszerepkör-példányok esetében óránként számol fel díjat a felhasznált kiszolgálóidő után.
A kiszolgálóidő felhasználása az alkalmazás üzembe helyezésétől kezdődik, még akkor is, ha a példányok nem futnak, és leállított állapotban vannak.

A következő lépések bemutatják a állítsa le és törölje az alkalmazást.

1. Állítsa le az előző szakaszban létrehozott szolgáltatástelepítést a Windows PowerShell-ablakban az alábbi parancsmag használatával:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   A szolgáltatás leállítása eltarthat néhány percig. Miután a szolgáltatás leállt, kap egy üzenetet, amely tájékoztatja a leállásról.

2. A szolgáltatás törléséhez hívja meg a következő parancsot:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   Ha a rendszer rákérdez, írja be az **Y** karaktert a szolgáltatás törléséhez.

   A szolgáltatás törlése eltarthat néhány percig. A szolgáltatás törlését követően kapni fog egy üzenet, amely azt jelzi, hogy törölve lett-e a szolgáltatás.

[Express használata Node.js-webalkalmazás]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[tárolása és az adatok elérése az Azure-ban]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Node.js-webalkalmazás]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


