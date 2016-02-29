<properties
    pageTitle="NoSQL 教學課程：DocumentDB .NET SDK | Microsoft Azure"
    description="NoSQL 教學課程，將使用 DocumentDB.NET SDK 來建立線上資料庫以及 C# 主控台應用程式。 DocumentDB 是 JSON 的 NoSQL 資料庫。"
    keywords="nosql tutorial, online database, c# console application"
    services="documentdb"
    documentationCenter=".net"
    authors="AndrewHoh"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article" 
    ms.date="11/18/2015"
    ms.author="anhoh"/>

# NoSQL 教學課程：DocumentDB C# 主控台應用程式 

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

歡迎使用 DocumentDB .NET SDK 的 NoSQL 教學課程！ 完成本教學課程之後，您將會有一個主控台應用程式，可用來建立和查詢 DocumentDB 資源。

本文將討論：

- 建立和連接到 DocumentDB 帳戶
- 設定 Visual Studio 方案
- 建立線上資料庫
- 建立集合
- 建立 JSON 文件
- 查詢集合
- 刪除資料庫

沒有時間嗎？ 別擔心！ 完整的方案位於 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started)。 請參閱 [取得完整的解決方案](#GetSolution) 取得簡要指示。 

之後，請使用此頁面頂端或底部的投票按鈕，將意見反應提供給我們。 如果想要我們直接與您連絡，歡迎在留言中留下電子郵件地址。

讓我們開始吧！

## 必要條件

請確定您具有下列項目：

- 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以註冊 [免費試用版](http://azure.microsoft.com/pricing/free-trial/)。
- [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com/)。

## 步驟 1：建立 DocumentDB 帳戶

讓我們建立 DocumentDB 帳戶。 如果您已經有您想要使用的帳戶，您可以直接跳到 [安裝 Visual Studio 方案](#SetupVS)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> 步驟 2: 安裝 Visual Studio 方案

1. 開啟 **Visual Studio** 電腦上。
2. 在 **檔案** 功能表上，選取 **新增**, ，然後選擇 [ **專案**。
3. 在 **新的專案** 對話方塊中，選取 **範本** / **Visual C#** / **主控台應用程式**, ，將您的專案，然後按一下 **確定**。
4. 在 **方案總管] 中**, ，以滑鼠右鍵按一下新的主控台應用程式，這是 Visual Studio 方案底下。
5. 而不需離開功能表，然後按一下 [ **管理 NuGet 封裝...**
6. 在左側的大多數面板 **管理 NuGet 封裝** ] 視窗中，按一下 [ **線上** / **nuget.org**。
7. 在 **線上搜尋** 輸入方塊中，搜尋 **DocumentDB 用戶端程式庫**。
8. 在結果中，尋找 **Microsoft Azure DocumentDB 用戶端程式庫** 按一下 **安裝**。  
   DocumentDB 用戶端程式庫套件識別碼是 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

太棒了！ 現在已完成安裝程式，讓我們開始撰寫一些程式碼。

##<a id="Connect"></a> 步驟 3: 連接到 DocumentDB 帳戶

首先，在 Program.cs 檔案中，將這些參考新增到 C# 應用程式的開頭：

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] 若要完成本教學課程中 NoSQL，請確定您已加入前述的相依性。

接著，將儲存 DocumentDB 帳戶端點及的主要或次要存取金鑰，可以找到此 [Azure 入口網站](https://portal.azure.com)。

![顯示 DocumentDB 帳戶的 Azure 入口網站螢幕擷取畫面，內含反白顯示的 [主動式] 集線器、[DocumentDB 帳戶] 刀鋒視窗上反白顯示的 [金鑰] 按鈕、[金鑰] 刀鋒視窗上反白顯示的 [URI]、[主要金鑰] 和 [次要金鑰] 值][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

我們會先取得開始的示範應用程式中建立的新執行個體 **DocumentClient**。 建立新的非同步工作，稱為 **GetStartedDemo** 並具現化新 **DocumentClient**。

    private static async Task GetStartedDemo()
    {
        // Create a new instance of the DocumentClient
        var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
    }

呼叫非同步工作從您 **Main** 方法類似於下列程式碼。

    public static void Main(string[] args)
    {
        try
        {
            GetStartedDemo().Wait();
        }
        catch (Exception e)
        {
            Exception baseException = e.GetBaseException();
            Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
    }

> [AZURE.WARNING]請勿將認證儲存在原始程式碼中。 為了讓這個範例簡單明瞭，原始程式碼中會顯示認證。 請參閱 [Azure 網站: 應用程式字串與連接字串的運作](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) 如需有關如何在生產環境中儲存認證資訊。 看一下我們的範例應用程式 [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) 如需原始程式碼外儲存認證的範例。

既然您已了解如何連接到 DocumentDB 帳戶，並建立的執行個體 **DocumentClient** 類別中，讓我們來看看如何使用與 DocumentDB 資源。  

## 步驟 4：建立線上資料庫
DocumentDB [資料庫](documentdb-resources.md#databases) 可以藉由建立 [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法 **DocumentClient** 類別。 資料庫是分割給多個集合之 JSON 文件儲存體的邏輯容器。 建立新的資料庫中您 **GetStartedDemo** 方法之後您 **DocumentClient** 建立。

    // Check to verify a database with the id=FamilyRegistry does not exist
    Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

    // If the database does not exist, create a new database
    if (database == null)
    {
        database = await client.CreateDatabaseAsync(
            new Database
            {
                Id = "FamilyRegistry"
            });

        // Write the new database's id to the console
        Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
    }

##<a id="CreateColl"></a>步驟 5: 建立集合  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** 會建立具有定價含意的新 S1 集合。 如需詳細資訊，請造訪我們 [定價頁面](https://azure.microsoft.com/pricing/details/documentdb/)。

A [集合](documentdb-resources.md#collections) 可以藉由建立 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法 **DocumentClient** 類別。 集合是 JSON 文件和相關聯 JavaScript 應用程式邏輯的容器。 新建立的集合將會對應至 [S1 效能層級](documentdb-performance-levels.md)。 建立名為的新集合 **FamilyCollection** 您將在建立資料庫之後 **GetStartedDemo** 方法。

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

    // If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
        documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
            new DocumentCollection
            {
                Id = "FamilyCollection"
            });

        // Write the new collection's id to the console
        Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
    }

##<a id="CreateDoc"></a>步驟 6：建立 JSON 文件
A [文件](documentdb-resources.md#documents) 可以藉由建立 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 方法 **DocumentClient** 類別。 文件會是使用者定義的 (任意) JSON 內容。 現在可插入一或多份文件。 如果您已經有您想要儲存在資料庫中的資料，您可以使用 DocumentDB 的 [資料移轉工具](documentdb-import-data.md)。

首先，我們需要建立 **父**, ，**子**, ，**寵物**, ，**位址** 和 **系列** 類別。 加入下列內部子類別之後，建立這些類別 **GetStartedDemo** 方法。

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

接下來，建立您的文件內您 **GetStartedDemo** 非同步方法。

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

    // If the document does not exist, create a new document
    if (document == null)
    {
        // Create the Andersen Family document
        Family andersonFamily = new Family
        {
            Id = "AndersenFamily",
            LastName = "Andersen",
            Parents = new Parent[] {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay"}
            },
            Children = new Child[] {
                new Child
                { 
                    FirstName = "Henriette Thaulow", 
                    Gender = "female", 
                    Grade = 5, 
                    Pets = new Pet[] {
                        new Pet { GivenName = "Fluffy" } 
                    }
                } 
            },
            Address = new Address { State = "WA", County = "King", City = "Seattle" },
            IsRegistered = true
        };
    
        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
    }

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
    }

您現在已經在 DocumentDB 帳戶中建立下列資料庫、集合和文件。

![說明帳戶、資料庫、集合和文件之間階層式關聯性的圖表](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>步驟 7: 查詢 DocumentDB 資源

DocumentDB 支援豐富 [查詢](documentdb-sql-query.md) 對每個集合中儲存的 JSON 文件。  下列範例程式碼示範可在我們於前一個步驟中插入的文件上執行的各種查詢 (同時使用 DocumentDB SQL 語法和 LINQ)。 將這些查詢加入您 **GetStartedDemo** 非同步方法。

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

下圖說明如何針對您所建立的集合呼叫 DocumentDB SQL 查詢語法，相同邏輯也可以套用至 LINQ 查詢。

![說明查詢範圍和意義的圖表](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

 [FROM](documentdb-sql-query.md#from-clause) 關鍵字是選擇性的查詢中，因為 DocumentDB 查詢已侷限於單一集合。 因此，"FROM Families f" 可以換成 "FROM root r"，或您選擇的任何其他變數名稱。 依預設，DocumentDB 會推斷該系列、根或您選擇的變數名稱來參考目前的集合。

##<a id="DeleteDatabase"></a>步驟 8: 刪除資料庫

刪除已建立的資料庫將會移除資料庫和所有子系資源 (集合、文件等)。 您可以刪除資料庫和文件用戶端的結尾加入下列程式碼片段程式 **GetStartedDemo** 非同步方法。

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
    client.Dispose();

##<a id="Run"></a>步驟 9: 執行 C# 主控台應用程式!

您現在可以開始執行應用程式。 在結束您 **Main** 方法中，新增下行程式碼，可讓您讀取主控台輸出應用程式完成執行之前。

    Console.ReadLine();

現在，在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。

您現在應該可以看到入門應用程式的輸出。 輸出將會顯示新增的查詢結果，而且應該符合以下的範例文字。

    Read {
      "id": "AndersenFamily",
      "LastName": "Andersen",
      "Parents": [
        {
          "FamilyName": null,
          "FirstName": "Thomas"
        },
        {
          "FamilyName": null,
          "FirstName": "Mary Kay"
        }
      ],
      "Children": [
        {
          "FamilyName": null,
          "FirstName": "Henriette Thaulow",
          "Gender": "female",
          "Grade": 5,
          "Pets": [
            {
              "GivenName": "Fluffy"
            }
          ]
        }
      ],
      "Address": {
        "State": "WA",
        "County": "King",
        "City": "Seattle"
      },
      "IsRegistered": true,
      "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
      "_ts": 1428372205,
      "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
      "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
      "_attachments": "attachments/"
    } from SQL
    Read {
      "id": "AndersenFamily",
      "LastName": "Andersen",
      "Parents": [
        {
          "FamilyName": null,
          "FirstName": "Thomas"
        },
        {
          "FamilyName": null,
          "FirstName": "Mary Kay"
        }
      ],
      "Children": [
        {
          "FamilyName": null,
          "FirstName": "Henriette Thaulow",
          "Gender": "female",
          "Grade": 5,
          "Pets": [
            {
              "GivenName": "Fluffy"
            }
          ]
        }
      ],
      "Address": {
        "State": "WA",
        "County": "King",
        "City": "Seattle"
      },
      "IsRegistered": true,
      "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
      "_ts": 1428372205,
      "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
      "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
      "_attachments": "attachments/"
    } from LINQ
    Read {
      "id": "AndersenFamily",
      "LastName": "Andersen",
      "Parents": [
        {
          "FamilyName": null,
          "FirstName": "Thomas"
        },
        {
          "FamilyName": null,
          "FirstName": "Mary Kay"
        }
      ],
      "Children": [
        {
          "FamilyName": null,
          "FirstName": "Henriette Thaulow",
          "Gender": "female",
          "Grade": 5,
          "Pets": [
            {
              "GivenName": "Fluffy"
            }
          ]
        }
      ],
      "Address": {
        "State": "WA",
        "County": "King",
        "City": "Seattle"
      },
      "IsRegistered": true,
      "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
      "_ts": 1428372205,
      "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
      "_etag": "\"0000400c-0000-0000-0000-55233aed0000\"",
      "_attachments": "attachments/"
    } from LINQ query

恭喜！ 您已經完成本 NoSQL 教學課程！ 

##<a id="GetSolution"></a> 取得完整的方案
若要建置包含本文中所有範例的 GetStarted 方案，您將需要下列項目：

-   [DocumentDB 帳戶][documentdb-create-account]。
-    [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) GitHub 上有可用的解決方案。

若要還原在 Visual Studio 中對 DocumentDB.NET sdk 的參考，以滑鼠右鍵按一下 **GetStarted** 方案中的方案總管] 中，然後按一下 **啟用 NuGet 封裝還原**。 接下來，在 App.config 檔案中更新 EndpointUrl 和 AuthorizationKey 值中所述 [連接至 DocumentDB 帳戶](#Connect)。

## 後續步驟

-   需要更複雜的 ASP.NET MVC NoSQL 教學課程嗎？ 請參閱 [使用 ASP.NET MVC 使用 DocumentDB 來建置 web 應用程式](documentdb-dotnet-application.md)。
-   了解如何 [監視 DocumentDB 帳戶](documentdb-monitor-accounts.md)。
-   對我們的範例資料集，在執行查詢 [查詢遊樂場](https://www.documentdb.com/sql/demo)。
-   深入了解的開發 > 一節中的程式設計模型 [DocumentDB 文件頁面](../../services/documentdb/)。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
 

