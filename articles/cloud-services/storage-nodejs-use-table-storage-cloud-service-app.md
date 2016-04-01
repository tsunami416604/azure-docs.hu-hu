<properties 
    pageTitle="使用資料表儲存體的 Web 應用程式 (Node.js) | Microsoft Azure" 
    description="本教學課程以「使用 Express 的 Web 應用程式」教學課程為基礎，再加上 Azure 儲存體服務和 Azure 模組建置而成。" 
    services="cloud-services, storage" 
    documentationCenter="nodejs" 
    authors="TomArcher" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="09/01/2015" 
    ms.author="tarcher"/>






# 使用儲存體的 Node.js Web 應用程式

## 概觀

在本教學課程中，您就可以擴充應用程式中建立
[Node.js Web Application using Express] 使用 Microsoft 的教學課程
Azure Node.js 來搭配資料管理服務的用戶端程式庫。 您
將擴充您的應用程式建立 web 架構工作清單應用程式
您可以部署到 Azure。 工作清單可讓使用者
擷取工作、 新增工作及將工作標示為已完成。

工作項目會儲存於 Azure 儲存體中。 Azure
儲存體提供可容錯的非結構化的資料儲存體和
高可用性。 Azure 儲存體包括數種資料結構
您可以儲存和存取資料，並可以運用儲存體
隨附於 Azure SDK for Node.js Api 的服務，或
透過 REST Api。 如需詳細資訊，請參閱 [Storing and Accessing Data in Azure]。

本教學課程假設您已完成 [Node.js Web
應用程式] 和 [使用 Express 的 Node.js][Node.js Web Application using Express] 教學課程。

您將了解：

-   如何使用 Jade 範本引擎
-   如何使用 Azure 資料管理服務

以下螢幕擷取畫面顯示完成的應用程式：

![The completed web page in internet explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## 在 Web.Config 中設定儲存體認證

若要存取 Azure 儲存體，您要傳遞儲存體中
認證。 若要這樣做，您必須使用 web.config 應用程式設定。
這些設定將環境變數的形式傳遞至節點，其中
接著再由 Azure SDK 讀取。

> [AZURE.NOTE] 在應用程式時，才會用儲存體認證
才會用到儲存體認證。 在模擬器中執行時，應用程式
將使用儲存體模擬器。

執行下列步驟，以擷取儲存體帳戶認證
並將其新增至 web.config 設定 ︰

1.  如果它尚未開啟，開始使用 Azure PowerShell 從 **開始** ] 功能表上，依序展開 **所有程式]、 [Azure**, ，以滑鼠右鍵按一下 **PowerShell**, ，然後選取 **系統管理員身分執行**。

2.  將目錄變更到包含應用程式的資料夾。 例如 C:\\node\\tasklist\\WebRole1。

3.  在 [Azure Powershell] 視窗中，輸入下列 Cmdlet 以擷取儲存體帳戶資訊：

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

    這會擷取與託管服務相關的儲存體帳戶和帳戶金鑰清單。

    > [AZURE.NOTE] Azure SDK 會建立儲存體帳戶，當您部署服務，從上一個說明中的應用程式的部署中已存在儲存體帳戶。

4.  開啟 **ServiceDefinition.csdef** 包含環境設定應用程式部署至 Azure 時所使用的檔案 ︰

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  將下列區塊下方插入 **環境** 項目，取代 {STORAGE ACCOUNT} 和 {STORAGE ACCESS KEY} 帳戶名稱與您想要用於部署的儲存體帳戶的主索引鍵 ︰

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

    ![web.cloud.config 檔案內容](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  儲存檔案並關閉記事本。

### 安裝其他模組

2. 使用下列命令來安裝 [azure]、 [node-uuid]、 [nconf] 及 [async] 模組，在本機儲存其項目，才能 **package.json** 檔案 ︰

        PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

    此命令的輸出應類似這樣：

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

##在節點應用程式中使用資料表服務

在本節中，您將擴充所建立的基本應用程式 **express** 命令，以新增 **task.js** 檔案，其中包含您工作的模型。 您也將修改現有 **app.js** ，並建立新 **tasklist.js** 檔案，以使用模型。

### 建立模型

1. 在 **WebRole1** 目錄中，建立名為的新目錄 **模型**。

2. 在 **模型** 目錄中，建立新的檔名為 **task.js**。 此檔案將包含您的應用程式建立之工作的模型。

3. 在開始 **task.js** 檔案中，新增下列程式碼以參考所需的程式庫 ︰

        var azure = require('azure-storage');
        var uuid = require('node-uuid');
        var entityGen = azure.TableUtilities.entityGenerator;

4. 接下來，要加入程式碼以定義和匯出 Task 物件。 此物件負責連接至資料表。

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

5. 接下來，新增下列程式碼以定義 Task 物件上的其他方法，允許與資料表中存放的資料互動：

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

6. 儲存並關閉 **task.js** 檔案。

### 建立控制器

1. 在 **WebRole1/routes** 目錄中，建立新的檔名為 **tasklist.js** 和文字編輯器中開啟。

2. 加入下列程式碼以 **tasklist.js**。 這會載入 azure 和 async 模組，可供 **tasklist.js**。 這也會定義 **TaskList** 函式，傳遞的執行個體 **工作** 我們稍早定義的物件 ︰

        var azure = require('azure-storage');
        var async = require('async');

        module.exports = TaskList;

        function TaskList(task) {
          this.task = task;
        }

2. 繼續加入到 **tasklist.js** 檔案中所使用的方法 **showTasks**, ，**addTask**, ，和 **completeTasks**:

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

3. 儲存 **tasklist.js** 檔案。

### 修改 app.js

1. 在 **WebRole1** 目錄中，開啟 **app.js** 在文字編輯器中的檔案。 

2. 在檔案開頭，加入下列內容以載入 azure 模組，以及設定資料表名稱和資料分割索引鍵：

        var azure = require('azure-storage');
        var tableName = 'tasks';
        var partitionKey = 'hometasks';

3. 在 app.js 檔中，向下捲動到看見此行處：

        app.use('/', routes);
        app.use('/users', users);

    將以上幾行取代為以下顯示的程式碼。 這會初始化 <strong>Task</strong> 的執行個體，並連接到您的儲存體帳戶。 這會傳遞給 <strong>TaskList</strong>，它將用來與表格服務通訊：

        var TaskList = require('./routes/tasklist');
        var Task = require('./models/task');
        var task = new Task(azure.createTableService(), tableName, partitionKey);
        var taskList = new TaskList(task);

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
    
4. 儲存 **app.js** 檔案。

### 修改索引檢視

1. 將目錄變更至 **檢視** 目錄，開啟 **index.jade** 在文字編輯器中的檔案。

2. 內容取代 **index.jade** 下列程式碼檔案。 這會定義用於顯示現有工作的檢視，以及用於加入新工作及將現有工作標示為完成的表單。

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

3. 儲存並關閉 **index.jade** 檔案。

### 修改全域版面配置

 **Layout.jade** 檔案中 **檢視** 目錄用來做為全域範本其他 **.jade** 檔案。 在此步驟中您將修改它以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap), ，工具組能夠方便設計美觀的網站的。

1. 下載並解壓縮的檔案 [Twitter Bootstrap](http://getbootstrap.com/)。 複製 **bootstrap.min.css** 檔案從 **bootstrap\\dist\\css** 資料夾 **public\\stylesheets** tasklist 應用程式的目錄。

2. 從 **檢視** 資料夾中，開啟 **layout.jade** 在文字編輯器中，將取代為下列內容 ︰

        doctype html
        html
          head
            title= title
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
            link(rel='stylesheet', href='/stylesheets/style.css')
          body.app
            nav.navbar.navbar-default
              div.navbar-header
                a.navbar-brand(href='/') My Tasks
            block content

3. 儲存 **layout.jade** 檔案。

### 在模擬器中執行應用程式

使用下列命令在模擬器中啟動應用程式。

    PS C:\node\tasklist\WebRole1> start-azureemulator -launch

瀏覽器會開啟並顯示下列頁面：

![A web paged titled My Task List with a table containing tasks and fields to add a new task.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

使用表單新增項目，或將現有的項目標示為已完成以移除它們。

## 將應用程式發行至 Azure


在 Windows PowerShell 視窗中，呼叫下列 Cmdlet 以將您的託管服務重新部署至 Azure。

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

取代 **myuniquename** 與此應用程式的唯一名稱。 取代 **datacentername** 與 Azure 資料中心的名稱，例如 **美國西部**。

部署完成之後，您應該會看到類似這樣的回應：

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

和之前一樣，因為指定 **-啟動** 選項時，瀏覽器會開啟並顯示當發行完成時，在 Azure 中執行的應用程式。

![顯示 [我的工作清單] 頁面的瀏覽器視窗。 URL 會指出頁面目前由 Azure 所主控。](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## 停止並刪除您的應用程式

部署後您的應用程式，您可能想要讓您可以將它停用
避免成本，或建置並部署其他應用程式內使用免費試用版
一段時間。

Azure 會就每小時伺服器時間所使用的 Web 角色執行個體數進行收費。
您的應用程式部署之後，都會耗用伺服器時間即使
執行個體未執行並處於停止狀態。

下列步驟示範如何停止並刪除應用程式。

1.  在 Windows PowerShell 視窗中，停止的服務部署
    建立在上一節，使用下列 cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

    停止服務可能需要幾分鐘的時間。 服務停止時，您將收到表示停止的訊息。

3.  若要刪除服務，請呼叫下列 Cmdlet：

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

    出現提示時，輸入 **Y** 以刪除服務。

    刪除服務可能需要幾分鐘的時間。 刪除服務後，您將收到表示已刪除服務的訊息。

  [Node.js Web Application using Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Node.js Web Application]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 


