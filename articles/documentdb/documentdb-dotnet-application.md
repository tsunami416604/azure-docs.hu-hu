<properties 
    pageTitle="DocumentDB 的 ASP.NET MVC 教學課程：Web 應用程式開發 | Microsoft Azure" 
    description="完成這個 ASP.NET MVC 教學課程來使用 DocumentDB 建立 MVC Web 應用程式。 您將從託管於 Azure 網站的待辦事項應用程式儲存 JSON 和存取資料。" 
    keywords="asp.net mvc tutorial, web application development, mvc web application, asp net mvc tutorial step by step"
    services="documentdb" 
    documentationCenter=".net" 
    authors="ryancrawcour" 
    manager="jhubbard" 
    editor="cgronlun"/>


<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="hero-article" 
    ms.date="11/18/2015" 
    ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>使用 DocumentDB 的 ASP.NET MVC web 應用程式開發

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md) 

為了特別說明您可以如何有效率地利用 Azure DocumentDB 來儲存和查詢 JSON 文件，本文提供如何使用 Azure DocumentDB 建置待辦事項應用程式的完整逐步解說。 在 Azure DocumentDB 中，這些工作將會儲存為 JSON 文件。

![本教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面 - ASP NET MVC 教學課程逐步解說](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image1.png)

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，來儲存和存取 Azure 上所託管 ASP.NET MVC Web 應用程式的資料。

> [AZURE.TIP] 本教學課程假設您使用 ASP.NET MVC 和 Azure 網站的經驗。 如果您不熟悉 ASP.NET 或 [必備工具](#_Toc395637760), ，我們建議您下載完整的範例專案，從 [GitHub][] 並遵循此範例中的指示。 建置完成後，您可以檢閱此文章，以加深對專案內容中程式碼的了解。

## <a name="_Toc395637760"></a>在此資料庫的教學課程的必要條件

在依照本文中的指示進行之前，您應先確定備妥下列項目：

- 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](../../pricing/free-trial/)。
- [Visual Studio 2013](http://www.visualstudio.com/) Update 4 或更新版本。
- Azure SDK for.NET 2.5.1 版或更高版本，可透過 [Microsoft Web Platform Installer][]。

本文中的所有螢幕擷取畫面都是使用已套用 Update 4 的 Visual Studio 2013 以及 Azure SDK for .NET 2.5.1 版所取得。 如果您的系統是設定使用不同的版本，則您的畫面和選項可能不會完全相符，但只要您符合上述必要條件，本方案應該還是有效。

## <a name="_Toc395637761"></a>步驟 1：建立 DocumentDB 資料庫帳戶

現在就開始建立 DocumentDB 帳戶。 如果您已經有帳戶，您可以跳到 [建立新的 ASP.NET MVC 應用程式](#_Toc395637762)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>
我們現在將從頭開始逐步解說如何建立新的 ASP.NET MVC 應用程式。 

## <a name="_Toc395637762"></a>步驟 2: 建立新的 ASP.NET MVC 應用程式

既然您已有帳戶，我們可以開始建立新的 ASP.NET 專案。

1. 在 Visual Studio 中，在 **檔案** 功能表上，指向 **新增**, ，然後按一下 [ **專案**。

     **新的專案** ] 對話方塊隨即出現。
2. 在 **專案類型** ] 窗格中，展開 **範本**, ，**Visual C#**, ，**Web**, ，然後選取 **ASP.NET Web 應用程式**。

    ![[新增專案] 對話方塊的螢幕擷取畫面，內含反白顯示的 ASP.NET Web 應用程式專案類型](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image10.png)

3. 在 **名稱** 方塊中，輸入專案的名稱。 本教學課程使用 "todo" 名稱。 如果您選擇使用其他名稱，則每當本教學課程提及 todo 命名空間時，您必須調整提供的程式碼範例，以便使用您為應用程式所命名的名稱。 

4. 按一下 [ **瀏覽** 瀏覽至您想要以建立專案，然後按一下資料夾 **確定**。

     **新增 ASP.NET 專案** ] 對話方塊隨即出現。

    ![[新增 ASP.NET 專案] 對話方塊的螢幕擷取畫面，內含反白顯示的 MVC 應用程式範本，和勾選的 [在雲端託管] 方塊。](./media/documentdb-dotnet-application/asp-net-mvc-tutorial-image11.png)

5. 在 [範本] 窗格中，選取 **MVC**。

6. 如果您打算在 Azure 中託管應用程式然後選取 **定域機組中的主機** 右下角，Azure 才能託管應用程式。 我們已選擇要在雲端託管，並執行 Azure 網站中所託管的應用程式。 選取此選項將會預先佈建 Azure 網站，讓您在需要部署最終的運作應用程式時更為容易。 如果您想要裝載位置，或不想要設定 Azure 預付，則請清除 **定域機組中的主機**。

7. 按一下 [ **確定** ，並讓 Visual Studio 執行有關 scaffolding 空的 ASP.NET MVC 範本。 

8. 如果您選擇在雲端託管，則至少會有一個其他畫面出現，要求您登入 Azure 帳戶並提供新網站的部分值。 提供所有其他的值，然後繼續進行。 

    我在這裡沒有選擇 [資料庫伺服器]，因為我們並未使用 Azure SQL Database Server，稍後我們會在 Azure 入口網站中建立新的 Azure DocumentDB 帳戶。

    如需有關選擇 **應用程式服務方案** 和 **資源群組**, ，請參閱 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。

    ![[設定 Microsoft Azure 網站] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image11_1.png)

9. Visual Studio 建立好未定案 MVC 應用程式之後，您便擁有可以在本機執行的空白 ASP.NET 應用程式。

    我們會跳過在本機執行專案，因為我確定我們都已看過 ASP.NET "Hello World" 應用程式。 讓我們直接跳到將 DocumentDB 新增至此專案並建置應用程式的步驟。

## <a name="_Toc395637767"></a>步驟 3: 將 DocumentDB 新增至您的 MVC web 應用程式專案

既然我們已經有大部分的 ASP.NET MVC 配管，我們需要
此解決方案，讓我們開始本教學課程中，將 Azure DocumentDB 新增至 MVC web 應用程式的真正目的。

1. DocumentDB .NET SDK 會隨 NuGet 封裝而分散。 若要取得 NuGet 封裝在 Visual Studio 中，使用 NuGet 封裝管理員在 Visual Studio 中的專案上按一下滑鼠右鍵 **方案總管] 中** ，然後按一下 [ **管理 NuGet 封裝**。

    ![[方案總管] 中 Web 應用程式專案的滑鼠右鍵選項的螢幕擷取畫面，內含反白顯示的 [管理 NuGet 封裝]。](./media/documentdb-dotnet-application/image21.png)

     **管理 NuGet 封裝** ] 對話方塊隨即出現。

2. 在 **線上搜尋** 方塊中，輸入 ***Azure DocumentDB***。 
    
    從結果中，安裝 **Microsoft Azure DocumentDB 用戶端程式庫** 封裝。 這會下載和安裝 DocumentDB 封裝，以及所有依存項目 (例如 Newtonsoft.Json)。

    ![[管理 NuGet 封裝] 視窗的螢幕擷取畫面，內含反白顯示的 Microsoft Azure DocumentDB 用戶端程式庫](./media/documentdb-dotnet-application/nuget.png)

    或者，您也可以使用 Package Manager Console 來安裝封裝。 若要這樣做，在 **工具** ] 功能表上，按一下 [ **NuGet 封裝管理員**, ，然後按一下 [ **Package Manager Console**。 在出現提示時輸入下列內容：

        Install-Package Microsoft.Azure.DocumentDB

3. 安裝封裝之後，您的 Visual Studio 方案應該類似下列已新增兩個新參考 (Microsoft.Azure.Documents.Client 和 Newtonsoft.Json) 的方案。

    ![[方案總管] 中 JSON 資料專案新增兩個參考的螢幕擷取畫面](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>步驟 4: 設定 ASP.NET MVC 應用程式
 
現在我們可以開始將模型、檢視和控制站新增至此 MVC 應用程式：

- [將模型加入](#_Toc395637764)。
- [新增控制器](#_Toc395637765)。
- [新增檢視](#_Toc395637766)。


### <a name="_Toc395637764"></a>將 JSON 資料模型

讓我們先建立 **M** 在 MVC 中的模型。 

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **模型** 資料夾中，按一下 [ **新增**, ，然後按一下 [ **類別**。

     **加入新項目** ] 對話方塊隨即出現。

2. 將新的類別 **Item.cs** 按一下 **新增**。 

3. 在這個新 **Item.cs** 檔案中，新增下列最後一個之後 *using 陳述式*。
        
        using Newtonsoft.Json;
    
4. 現在，將此程式碼取代 
        
        public class Item
        {
        }

    為下列程式碼。
        
        public class Item
        {
            [JsonProperty(PropertyName="id")]
            public string Id { get; set; }
        
            [JsonProperty(PropertyName="name")]
            public string Name { get; set; }
        
            [JsonProperty(PropertyName = "desc")]
            public string Description { get; set; }
        
            [JsonProperty(PropertyName="isComplete")]
            public bool Completed { get; set; }    
        }

    DocumentDB 中的所有資料都會透過線路傳遞，並儲存為 JSON。 若要控制的方式將物件序列化/還原序列化您可以使用的 JSON.NET **JsonProperty** 屬性中所示 **項目** 我們剛剛建立的類別。 您沒有 **有** 這樣做，但我想確定所有屬性都會依照 JSON camelCase 命名慣例。 
    
    不只控制屬性名稱的格式時的 JSON，不但可以跟我完全重新命名您的.NET 屬性 **描述** 屬性。 
    

### <a name="_Toc395637765"></a>新增控制器

之前都是談論 **M**, ，現在讓我們建立 **C** 在 MVC 控制器類別。

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **控制器** 資料夾中，按一下 **新增**, ，然後按一下 [ **控制器**。

     **Add Scaffold** ] 對話方塊隨即出現。

2. 選取 **MVC 5 控制器-空** 然後按一下 [ **新增**。

    ![[新增 Scaffold] 對話方塊的螢幕擷取畫面，內含反白顯示的 [MVC 5 控制器 - 空的] 選項](./media/documentdb-dotnet-application/image14.png)

3. 將新的控制器， **ItemController。**

    ![[新增控制器] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image15.png)

    一旦建立檔案時，Visual Studio 方案應該類似下列中的新 ItemController.cs 檔案 **方案總管] 中**。 稍早建立的新 Item.cs 檔案也會顯示出來。

    ![Visual Studio 的螢幕擷取畫面，[方案總管] 內含反白顯示的新 ItemController.cs 檔案和 Item.cs 檔案](./media/documentdb-dotnet-application/image16.png)

    您可以先將 ItemController.cs 關閉，我們稍後會回頭使用此檔案。 

### <a name="_Toc395637766"></a>加入檢視

現在，我們將建立 **V** 在 MVC 中的檢視:

- [新增項目索引檢視](#AddItemIndexView)。
- [加入新項目檢視](#AddNewIndexView)。
- [新增編輯項目檢視](#_Toc395888515)。


#### <a name="AddItemIndexView"></a>加入項目索引檢視

1. 在 **方案總管] 中**, ，展開 **檢視**  資料夾中，以滑鼠右鍵按一下空白 **項目** 資料夾，當您新增為您建立 Visual Studio **ItemController** 更早版本，按一下 [ **新增**, ，然後按一下 [ **檢視**。

    ![顯示 Visual Studio 方案建立之 Item 資料夾的 [方案總管] 螢幕擷取畫面，內含反白顯示的 [新增檢視] 命令](./media/documentdb-dotnet-application/image17.png)

2. 在 **加入檢視** 對話方塊方塊中，執行下列動作:
    - 在 **檢視名稱** 方塊中，輸入 ***索引***。
    - 在 **範本** 方塊中，選取 ***清單***。
    - 在 **模型類別** 方塊中，選取 ***項目 (待辦事項。模型)***。
    - 保留 **資料內容類別** 空的方塊。 
    - 在 [版面配置頁面方塊中，輸入 ***~/Views/Shared/_Layout.cshtml***。
    
    ![顯示 [新增檢視] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image18.png)

3. 所有這些值設定之後，按一下 [ **新增** ，並讓 Visual Studio 建立新的範本檢視。 完成時，它會開啟建立的 cshtml 檔案。 我們稍後會回頭使用此檔案，您可以先在 Visual Studio 中將該檔案關閉。

#### <a name="AddNewIndexView"></a>加入新項目檢視

類似於如何建立 **項目索引** ] 檢視中，我們將建立新的檢視，供建立新 **項目**。

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **項目** 資料夾再按一下 **新增**, ，然後按一下 [ **檢視**。

2. 在 **加入檢視** 對話方塊方塊中，執行下列動作:
    - 在 **檢視名稱** 方塊中，輸入 ***建立***。
    - 在 **範本** 方塊中，選取 ***建立***。
    - 在 **模型類別** 方塊中，選取 ***項目 (待辦事項。模型)***。
    - 保留 **資料內容類別** 空的方塊。
    - 在 [版面配置頁面方塊中，輸入 ***~/Views/Shared/_Layout.cshtml***。
    - 按一下 [ **新增**。

#### <a name="_Toc395888515"></a>加入編輯項目檢視

最後，新增最後一個檢視來編輯 **項目** 與之前相同的方式。

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **項目** 資料夾再按一下 **新增**, ，然後按一下 [ **檢視**。

2. 在 **加入檢視** 對話方塊方塊中，執行下列動作:
    - 在 **檢視名稱** 方塊中，輸入 ***編輯***。
    - 在 **範本** 方塊中，選取 ***編輯***。
    - 在 **模型類別** 方塊中，選取 ***項目 (待辦事項。模型)***。
    - 保留 **資料內容類別** 空的方塊。 
    - 在 [版面配置頁面方塊中，輸入 ***~/Views/Shared/_Layout.cshtml***。
    - 按一下 [ **新增**。

完成這項作業之後，請將 Visual Studio 中的所有 cshtml 文件關閉，我們稍後會回頭使用這些檢視。

## <a name="_Toc395637769"></a>步驟 5: 組建 documentdb

我們已經建立了標準的 MVC 項目，現在我們可以開始新增 DocumentDB 的程式碼。 

在本節中，我們將新增程式碼來處理下列作業：

- [列出未完成的項目](#_Toc395637770)。
- [將項目加入](#_Toc395637771)。
- [編輯項目](#_Toc395637772)。

### <a name="_Toc395637770"></a>列出未完成的項目，在 MVC web 應用程式

首先要執行的作業是新增類別，其中包含連線至及使用 DocumentDB 的所有邏輯。 在本教學課程中，我們會將所有邏輯封裝到名為 DocumentDBRepository 的儲存機制類別中。 

1. 在 **方案總管] 中**, ，以滑鼠右鍵按一下專案，按一下 **新增**, ，然後按一下 [ **類別**。 將新類別 **DocumentDBRepository** 按一下 **新增**。
 
2. 在新建立 **DocumentDBRepository** 類別並新增下列 *using 陳述式* 上方 *命名空間* 宣告
        
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;

    現在，將此程式碼取代 

        public class DocumentDBRepository
        {
        }

    為下列程式碼。

        
        public static class DocumentDBRepository<T>
        {
            //Use the Database if it exists, if not create a new Database
            private static Database ReadOrCreateDatabase()
            {
                var db = Client.CreateDatabaseQuery()
                                .Where(d => d.Id == DatabaseId)
                                .AsEnumerable()
                                .FirstOrDefault();
                
                if (db == null)
                {
                    db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
                }
                
                return db;
            }
            
            //Use the DocumentCollection if it exists, if not create a new Collection
            private static DocumentCollection ReadOrCreateCollection(string databaseLink)
            {
                var col = Client.CreateDocumentCollectionQuery(databaseLink)
                                  .Where(c => c.Id == CollectionId)
                                  .AsEnumerable()
                                  .FirstOrDefault();
        
                if (col == null)
                {
                    var collectionSpec = new DocumentCollection { Id = CollectionId };
                    var requestOptions = new RequestOptions {OfferType = "S1" };
                    
                    col = Client.CreateDocumentCollectionAsync(databaseLink, collectionSpec, requestOptions).Result;
                }
                
                return col;
            }
            
            //Expose the "database" value from configuration as a property for internal use
            private static string databaseId;
            private static String DatabaseId
            {
                get
                {
                    if (string.IsNullOrEmpty(databaseId))
                    {
                        databaseId = ConfigurationManager.AppSettings["database"];
                    }
                
                    return databaseId;
                }
            }
            
            //Expose the "collection" value from configuration as a property for internal use
            private static string collectionId;
            private static String CollectionId
            {
                get
                {
                    if (string.IsNullOrEmpty(collectionId))
                    {
                        collectionId = ConfigurationManager.AppSettings["collection"];
                    }
                
                    return collectionId;
                }
            }
            
            //Use the ReadOrCreateDatabase function to get a reference to the database.
            private static Database database;
            private static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = ReadOrCreateDatabase();
                    }
                    
                    return database;
                }
            }
            
            //Use the ReadOrCreateCollection function to get a reference to the collection.
            private static DocumentCollection collection;
            private static DocumentCollection Collection
            {
                get
                {
                    if (collection == null)
                    {
                        collection = ReadOrCreateCollection(Database.SelfLink);
                    }
                    
                    return collection;
                }
            }
            
            //This property establishes a new connection to DocumentDB the first time it is used, 
            //and then reuses this instance for the duration of the application avoiding the
            //overhead of instantiating a new instance of DocumentClient with each request
            private static DocumentClient client;
            private static DocumentClient Client
            {
                get
                {
                    if (client == null)
                    {
                        string endpoint = ConfigurationManager.AppSettings["endpoint"];
                        string authKey = ConfigurationManager.AppSettings["authKey"];
                        Uri endpointUri = new Uri(endpoint);
                        client = new DocumentClient(endpointUri, authKey);
                    }
                    
                    return client;
                }
            }
        }

    > [AZURE.TIP] When creating a new DocumentCollection you can supply an optional RequestOptions parameter of OfferType, which allows you to specify the performance level of the new collection. If this parameter is not passed the default offer type will be used. For more on DocumentDB offer types please refer to [DocumentDB Performance Levels](documentdb-performance-levels.md)

3. 我們打算從組態中，因此請開啟來讀取部分值 **Web.config** 應用程式檔案，並新增下列幾行下的 `<AppSettings>` 一節。
    
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
    
4. 現在，更新的值 *端點* 和 *authKey* 使用 Azure 入口網站的 [金鑰] 分頁。 使用 **URI** 端點設定，並使用值為 [金鑰] 分頁 **主索引鍵**, ，或 **次要金鑰** [金鑰] 分頁，做為 authKey 設定的值。


    That takes care of wiring up the DocumentDB repository, now let's add our application logic.

5. 我們想要對 todo 清單應用程式進行的第一件事就是顯示未完成的項目。  複製並貼上下列程式碼片段隨處 **DocumentDBRepository** 類別。

        public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
        {
            return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
                .Where(predicate) 
                .AsEnumerable(); 
        } 

6. 開啟 **ItemController** 我們稍早加入，並新增下列 *using 陳述式* 命名空間宣告上方。

        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;

    如果您的專案名稱不是 "todo"，則您必須使用 "todo.Models" 進行更新，才能反映您的專案名稱。

    現在，將此程式碼取代

        //GET: Item
        public ActionResult Index()
        {
            return View();
        }

    為下列程式碼。

        public ActionResult Index()
        {
            var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
            return View(items);
        }
    
此時，應該已經可以建置方案，而不會發生任何錯誤。

如果您執行應用程式現在，您可以前往 **HomeController** 和 **索引** 該控制器的檢視。 這是我們在一開始時所選擇的 MVC 範本專案預設行為，但是我們不想要這樣的行為！ 讓我們變更此 MVC 應用程式上的路由以改變此行為。

開啟 ***App\_Start\RouteConfig.cs*** 並尋找開頭為 「 預設值:"並將它變更為如下所示。

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

這會讓 ASP.NET MVC，如果您未指定值的 url，以控制路由行為，改用 **首頁**, ，使用 **項目** 作為控制器，並使用者 **索引** 作為檢視。

現在如果您執行應用程式，它會呼叫至您 **ItemController** 進而呼叫至儲存機制類別，並使用 GetItems 方法以傳回所有未完成的項目至 **檢視**\\**項目**\\**索引** 檢視。 

如果建置並立即執行此專案，您現在應該會看到如下的內容。    

![本資料庫教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>新增項目

我們可以開始將一些項目放入資料庫中，所以除了空白方格以外，我們還可以看到其他項目。

讓我們加入一些程式碼至 DocumentDBRepository 和 ItemController，以保留在 DocumentDB 中的記錄。

1.  將下列方法加入您 **DocumentDBRepository** 類別。

        public static async Task<Document> CreateItemAsync(T item)
        {
            return await Client.CreateDocumentAsync(Collection.SelfLink, item);
        }

    此方法只會接受傳遞給它的物件，並將它保留在 DocumentDB 中。

2. 開啟 ItemController.cs 檔案，並在類別中加入下列程式碼片段。 這是 ASP.NET MVC 知道如何進行 **建立** 動作。 在此情況下，只需轉譯先前建立的關聯 Create.cshtml 檢視。

        public ActionResult Create()
        { 
            return View(); 
        }

    我們現在需要更多的程式碼將會接受從提交此控制器中 **建立** 檢視。

2. 將下一個程式碼區塊新增至 ItemController.cs 類別，以告訴 ASP.NET MVC 如何使用表單 POST 來執行此控制器的作業。
    
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create([Bind(Include =  "Id,Name,Description,Completed")] Item item)  
        {
            if (ModelState.IsValid)  
            {  
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");  
            }   
            return View(item);   
        }
    這段程式碼會呼叫 DocumentDBRepository，並且使用 CreateItemAsync 方法將新的待辦事項項目保存到資料庫。 
 
    **安全性注意事項**: **ValidateAntiForgeryToken** 此處所使用屬性來協助保護應用程式防止跨網站偽造要求攻擊。 這不光只是新增此屬性，您的檢視也必須使用這個防偽權杖。 如需詳細資訊的主體和如何正確實作此範例，請參閱 [防止跨網站偽造要求][]。 在提供的原始程式碼 [GitHub][] 已完整實作的。

    **安全性注意事項**: 我們也會使用 **繫結** 屬性上的方法參數，以協助防範 over-posting 攻擊。 如需詳細資訊，請參閱 [ASP.NET MVC 中的基本 CRUD 作業][]。

這包括將新項目新增至資料庫所需的程式碼。


### <a name="_Toc395637772"></a>編輯項目

還有最後一點，我們也就是新增的功能來編輯 **項目** 資料庫中，並將它們標示為完成。 因此我們只需要加入一些程式碼至我們的控制器及已編輯的檢視加入至專案， **DocumentDBRepository** 類別一次。

1. 將下列內容加入 **DocumentDBRepository** 類別。

        public static T GetItem(Expression<Func<T, bool>> predicate)
        {
            return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                        .Where(predicate)
                        .AsEnumerable()
                        .FirstOrDefault();
        }       

        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            Document doc = GetDocument(id); 
            return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
        }

        private static Document GetDocument(string id) 
        { 
            return Client.CreateDocumentQuery(Collection.DocumentsLink) 
                .Where(d => d.Id == id) 
                .AsEnumerable() 
                .FirstOrDefault(); 
        } 
    
    這些方法的第一個 **GetItem** 從傳遞至 DocumentDB 提取項目 **ItemController** 然後將 **編輯** 檢視。
    
    方法的第二個剛剛新增 **文件** 的版本來取代 DocumentDB 中 **文件** 從傳入的 **ItemController**。

2. 將下列內容加入 **ItemController** 類別。

        public ActionResult Edit(string id)
        {
            if (string.IsNullOrEmpty(id))
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
                     
            Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
            
            if (item == null)
            {
                return HttpNotFound();
            }
            
            return View(item);
        }
        
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
           if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }

          return View(item);
        }
        
    
    第一種方法會處理當使用者在 [Http GET **編輯** 從連結 **索引** 檢視。 此方法會提取 [**文件**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) 從 DocumentDB 並將它傳遞給 **編輯** 檢視。

     **編輯** 檢視會接著執行 Http POST **IndexController**。 
    
    新增的第二個方法會處理此作業，將更新物件傳遞至 DocumentDB 並保留在資料庫中。

這是我們要執行應用程式，列出未完成的所有項目 **項目**, ，加入新 **項目**, ，並編輯 **項目**。

## <a name="_Toc395637773"></a>步驟 6: 在本機執行應用程式

若要在本機電腦測試應用程式，請執行下列動作：

1. 在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。 這樣應該可以建置應用程式，並啟動含有先前所看過之空白方格頁面的瀏覽器：

    ![本資料庫教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image24.png)

    如果在此時遇到錯誤，您可以比較您的程式碼範例專案上 [GitHub][]

2. 按一下 [ **新建** 連結並新增值至 **名稱** 和 **描述** 欄位。 保留 **完成** 核取方塊未選取否則新 **項目** 將完成狀態，且不會出現在初始清單。

    ![[建立] 檢視的螢幕擷取畫面](./media/documentdb-dotnet-application/image25.png)

3. 按一下 [ **建立** 和系統重新導向回到 **索引** 檢視和 **項目** 出現在清單中。

    ![[索引] 檢視的螢幕擷取畫面](./media/documentdb-dotnet-application/image26.png)

    依需要加入幾個 **項目** 至待辦事項] 清單。

3. 按一下 [ **編輯** 旁 **項目** 清單也會帶您到 **編輯** 檢視，您可以更新任何屬性的物件，包括 **完成** 旗標。 如果您將標示 **完成** 旗標，然後按一下 [ **儲存**, 、 **項目** 從從未完成的工作清單中移除。

    ![[索引] 檢視的螢幕擷取畫面，內含勾選的 [已完成] 方塊](./media/documentdb-dotnet-application/image27.png)

4. 完成測試應用程式後，按 Ctrl + F5 停止偵錯應用程式。 您現在可以開始進行部署。

##<a name="_Toc395637774"></a>步驟 7: 部署至 Azure 網站應用程式

您已經擁有可在 DocumentDB 正常運作的完整應用程式，我們現在要將此 Web 應用程式部署至 Azure 網站。 如果您選取 **定域機組中的主機** 當您建立空白 ASP.NET MVC 專案，Visual Studio 可讓這簡單，並為您完成大部分的工作。 

1. 若要發佈此應用程式，您只需要在專案上按一下滑鼠右鍵 **方案總管] 中** 按一下 **發行**。

    ![[方案總管] 中 [發佈] 選項的螢幕擷取畫面](./media/documentdb-dotnet-application/image28.png)

2. 系統應該已根據您的認證; 設定事實上網站已建立在 Azure 中為您在 **目的地 URL** 所示，您只需要為按一下 **發行**。

    ![Visual Studio 中 [發佈 Web] 對話方塊的螢幕擷取畫面 - ASP NET MVC 教學課程逐步解說](./media/documentdb-dotnet-application/image29.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

##<a name="_Toc395637775"></a>後續步驟

恭喜！ 您剛剛使用 Azure DocumentDB 建置您的第一個 ASP.NET MVC Web 應用程式，並將它發佈至 Azure 網站。 完整的應用程式，包括未包含在本教學課程的詳細資料和刪除功能的原始程式碼，可以下載或複製從 [GitHub][]。 所以如果您想要將程式碼加入您的應用程式，請抓取程式碼，並將它加入這個應用程式。

若要加入您的應用程式的其他功能，檢閱 [中提供的 Api [文件 DB.NET 程式庫](https://msdn.microsoft.com/library/azure/dn948556.aspx) 並歡迎您貢獻到 DocumentDB.NET 程式庫，在 [GitHub][]。 


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app

