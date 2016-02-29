<properties 
    pageTitle="了解 Node.js-DocumentDB Node.js 教學課程 |Microsoft Azure" 
    description="了解 Node.js！ 透過教學課程探索如何使用 Microsoft Azure DocumentDB 來儲存和存取 Azure 網站上託管的 Node.js Express Web 應用程式資料。" 
    keywords="Application development, database tutorial, learn node.js, node.js tutorial, documentdb, azure, Microsoft azure"
    services="documentdb" 
    documentationCenter="nodejs" 
    authors="ryancrawcour" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="hero-article" 
    ms.date="10/20/2015" 
    ms.author="ryancraw"/>

# <a name="_Toc395783175"></a>使用 DocumentDB 建置 Node.js Web 應用程式

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

本 Node.js 教學課程說明如何使用 Azure DocumentDB 服務，從 Azure 網站上託管的 Node.js Express 應用程式儲存和存取資料。

我們建議使用者從觀看下列影片開始，內容示範如何佈建 Azure DocumentDB 資料庫帳戶，以及如何將 JSON 文件儲存於 Node.js 應用程式。 

> [AZURE.VIDEO azure-demo-getting-started-with-azure-documentdb-on-nodejs-in-linux]

然後再回到本 Node.js 教學課程，您可在此找到下列問題的答案：

- 如何透過 documentdb npm 模組搭配使用 DocumentDB？
- 如何將 Web 應用程式部署至 Azure 網站？

依照本教學課程中資料庫，您將建置簡單的網頁
工作管理應用程式，以允許建立、擷取和
完成工作。 這些工作將會以 JSON 文件形式儲存在 Azure
DocumentDB。

![本 Node.js 教學課程所建立的「我的待辦事項清單」應用程式螢幕擷取畫面](./media/documentdb-nodejs-application/image1.png)

是否沒有時間完成本教學課程，只是想要取得完整的解決方案？ 沒問題，您可以取得完整的範例方案從 [GitHub][]。

## <a name="_Toc395783176"></a>先決條件

> [AZURE.TIP] 此 Node.js 教學課程假設您有些許使用 Node.js 和 Azure 網站的經驗。

在依照本文指示進行之前，您應該確定
已具有下列項目：

- 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](../../pricing/free-trial/)。
- [Node.js][] v0.10.29 版或更高版本。
- [Express 產生器](http://www.expressjs.com/starter/generator.html) (您可以透過安裝 `npm install express-generator -g`)
- [Git][]。

## <a name="_Toc395637761"></a>步驟 1：建立 DocumentDB 資料庫帳戶

現在就開始建立 DocumentDB 帳戶。 如果您已經有帳戶，您可以跳到 [步驟 2: 建立新的 Node.js 應用程式](#_Toc395783178)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

## <a name="_Toc395783178"></a>步驟 2: 了解如何建立新的 Node.js 應用程式

現在讓我們了解如何建立基本的 Hello World Node.js 專案使用 [Express](http://expressjs.com/) 架構。

1. 開啟您偏好的終端機。

2. 使用 express 產生器來產生新的應用程式呼叫 **todo**。

        express todo

3. 開啟您的新 **todo** 目錄並安裝相依性。

        cd todo
        npm install

4. 執行新的應用程式。

        npm start

5. 您可以藉由瀏覽您的瀏覽器中檢視新的應用程式 [http://localhost:3000/](http://localhost:3000)。

    ![了解 Node.js - Hello World 應用程式在瀏覽器視窗中的螢幕擷取畫面](./media/documentdb-nodejs-application/image12.png)

## <a name="_Toc395783179"></a>步驟 3: 安裝其他模組

 **Package.json** 檔案是其中一個的根目錄中建立的檔案
專案根目錄中的其中一個檔案。 這個檔案包含 Node.js 應用程式
的其他必要模組清單。 稍後，將此
應用程式部署至 Azure 網站時，會使用這個檔案來判定
需要在 Azure 上安裝以支援您應用程式的模式。 在本教學課程中，我們還需要再安裝兩個封裝。

1. 在 [終端機，安裝 **非同步** 透過 npm 模組。

        npm install async --save

1. 安裝 **documentdb** 透過 npm 模組。 這是 DocumentDB 發揮所有強大功能的模組。

        npm install documentdb --save

3. 快速檢查 **package.json** 檔案的應用程式應該會顯示其他模組。 這個檔案會告訴 Azure 在執行您的應用程式時要下載及安裝的封裝。 它看起來應該類似下面的範例。

    ![[package.json] 索引標籤的螢幕擷取畫面](./media/documentdb-nodejs-application/image17.png)

       This tells Node (and Azure later) that your application depends on these additional modules.

## <a name="_Toc395783180"></a>步驟 4: 在節點應用程式中使用 DocumentDB 服務

前面的內容在講述所有初始設定和組態，現在讓我們來了解本教學課程的真正目的，也就是使用 Azure DocumentDB 撰寫一些程式碼。

### 建立模型

1. 在專案目錄中，建立名為的新目錄 **模型**。
2. 在 **模型** 目錄中，建立新的檔名為 **taskDao.js**。 此檔案將包含應用程式所建立工作的模型。
3. 在同一個 **模型** 目錄中，建立另一個名為的新檔案 **docdbUtils.js**。 這個檔案會包含一些實用、可重複使用，適用於整個應用程式的程式碼。 
4. 複製下列程式碼中的 **docdbUtils.js**

        var DocumentDBClient = require('documentdb').DocumentClient;
            
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
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
                            
                            var requestOptions = {
                                offerType: 'S1'
                            };
                            
                            client.createCollection(databaseLink, collectionSpec, requestOptions, function (err, created) {
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

> [AZURE.TIP] createCollection 會採用選擇性的 requestOptions 參數，可用來指定集合的優惠類型。 如果未提供 requestOptions.offerType 值，則將會使用預設的優惠類型來建立集合。
> 如需 DocumentDB 優惠類型的詳細資訊請參閱 [DocumentDB 中的效能層級](documentdb-performance-levels.md) 
        
3. 儲存並關閉 **docdbUtils.js** 檔案。

4. 在開始 **taskDao.js** 檔案中，新增下列程式碼以參考 **DocumentDBClient** 和 **docdbUtils.js** 前面所建立:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');

4. 接下來，要加入程式碼以定義和匯出 Task 物件。 這會負責初始化我們的 Task 物件，並設定我們即將使用的資料庫和文件集合。

        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
        
          this.database = null;
          this.collection = null;
        }
        
        module.exports = TaskDao;

5. 接下來，新增下列程式碼以定義 Task 物件上的其他方法，可用來與 DocumentDB 中存放的資料進行互動。

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
                    query: 'SELECT * FROM root r WHERE r.id=@id',
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

6. 儲存並關閉 **taskDao.js** 檔案。 

### 建立控制器

1. 在 **路由** 您專案的目錄建立名為的新檔案 **tasklist.js**。 
2. 加入下列程式碼以 **tasklist.js**。 這會載入 DocumentDBClient 和 async 模組，可供 **tasklist.js**。 這也會定義 **TaskList** 函式，傳遞的執行個體 **工作** 我們稍早定義的物件:

        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
        
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
        
        module.exports = TaskList;

3. 繼續加入到 **tasklist.js** 檔案中所使用的方法 **showTasks、 addTask**, ，和 **completeTasks**:
        
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


4. 儲存並關閉 **tasklist.js** 檔案。
 
### 新增 config.js

1. 在您的專案目錄中建立名為的新檔案 **config.js**。
2. 將下列內容加入 **config.js**。 這會定義組態設定和我們的應用程式所需的值。

        var config = {}
        
        config.host = process.env.HOST || "[the URI value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the DocumentDB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
        
        module.exports = config;

3. 在 **config.js** 檔案中，更新 HOST 和 AUTH_KEY 使用 DocumentDB 帳戶的 [金鑰] 分頁上找到的值的值 [Microsoft Azure 入口網站](http://portal.azure.com):

4. 儲存並關閉 **config.js** 檔案。
 
### 修改 app.js

1. 在專案目錄中，開啟 **app.js** 檔案。 這是稍早建立 Express Web 應用程式時所建立的檔案。
2. 將下列程式碼加入至頂端 **app.js**
    
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');

3. 此程式碼會定義要使用的組態檔，並繼續讀出此檔案中的值到我們即將使用的變數。
4. 在下列兩行取代 **app.js** 檔案:

        app.use('/', routes);
        app.use('/users', users); 

      下列程式碼片段：

        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
        
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));


6. 這幾行會定義的新執行個體我們 **TaskDao** 物件，使用 DocumentDB 的新連線 (使用值讀取 **config.js**)，初始化工作物件，然後將方法的表單動作繫結我們 **TaskList** 控制站。 

7. 最後，儲存並關閉 **app.js** 檔案中，我們即將結束。
 
## <a name="_Toc395783181"></a>步驟 5: 建置使用者介面

現在，讓我們將注意力轉到建置使用者介面，以便使用者可以實際與我們的應用程式互動。 我們建立使用的 Express 應用程式 **Jade** 做為檢視引擎。 如需 Jade 的詳細資訊請參閱 [http://jade-lang.com/](http://jade-lang.com/)。

1.  **Layout.jade** 檔案中 **檢視** 目錄用來做為全域範本其他 **.jade** 檔案。 在此步驟中您將修改它以使用 [Twitter Bootstrap](https://github.com/twbs/bootstrap), ，工具組能夠方便設計美觀的網站的。 
2. 開啟 **layout.jade** 檔案中找到 **檢視** 資料夾並將內容取代為下列內容;
    
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



    這實際上會指示 **Jade** 引擎呈現我們應用程式的部分 HTML，並建立 **區塊** 呼叫 **內容** 我們可以在其中提供內容頁面的配置。
    儲存並關閉此 **layout.jade** 檔案。

4. 現在開啟 **index.jade** 檔案，將由我們的應用程式，並將檔案的內容取代為下列檢視:

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

    這會擴充配置，並提供內容 **內容** 我們中看到的預留位置 **layout.jade** 先前檔案。
    
    在此配置中，我們建立了兩個 HTML 表單。 
    第一個表單包含我們的資料和一個按鈕，讓我們透過張貼到更新項目資料表 **/completetask** 控制器的方法。
    第二個表單包含兩個輸入的欄位和一個按鈕，讓我們透過張貼到建立新的項目 **/addtask** 控制器的方法。
    
    這應該就是要讓應用程式開始運作所需的所有程式碼。

5. 開啟 **style.css** 檔案中 **public\stylesheets** 目錄並取代為下列程式碼:

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

    儲存並關閉此 **style.css** 檔案。

## <a name="_Toc395783181"></a>步驟 6: 在本機執行您的應用程式

1. 若要在本機電腦上測試應用程式，請在終端機中執行 `npm start` 以啟動應用程式，並啟動顯示如下圖所示頁面的瀏覽器：

    ![[我的待辦事項清單] 應用程式在瀏覽器視窗中的螢幕擷取畫面](./media/documentdb-nodejs-application/image18.png)


2. 使用所提供的 [項目]、[項目名稱] 和 [類別] 欄位來輸入
詳細資訊，，然後按一下 **加入項目**。

3. 系統應該會更新此頁面以在 ToDo
清單中顯示新建立的項目。

    ![[待辦事項] 清單中包含一個新項目的應用程式螢幕擷取畫面](./media/documentdb-nodejs-application/image19.png)

4. 若要完成工作，您只需勾選 [已完成] 資料行中的核取方塊，
然後按一下 [ **更新工作**。

## <a name="_Toc395783182"></a>步驟 7: 將您的應用程式開發專案部署至 Azure 網站

1. 如果您還沒有這麼做，請為您的 Azure 網站提供一個 Git 儲存機制。 您可以找到有關如何執行這項操作的指示 [這裡](../web-sites-publish-source-control-git.md#step4)。

2. 新增您的 Azure 網站做為 Git 遠端。

        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git

3. 透過推送到遠端進行部署。

        git push azure master

4. 幾秒後，git 便會完成發佈 Web
應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式
已在 Azure 中執行！

## <a name="_Toc395637775"></a>後續步驟

恭喜！ 您剛剛已使用 Azure DocumentDB 建立您的第一個 Express Web
應用程式並發行至 Azure 網站。

可以從下載完整參考應用程式的原始程式碼 [GitHub][]。

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[Github]: https://github.com/Azure-Samples/documentdb-node-todo-app
 

