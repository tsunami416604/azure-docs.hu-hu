<properties
    pageTitle=".NET 多層應用程式 | Microsoft Azure"
    description="協助您在 Azure 中開發多層式應用程式，以使用服務匯流排佇列在各層之間進行通訊的 .NET 教學課程。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/07/2015"
    ms.author="sethm"/>

# 使用 Azure 服務匯流排佇列的 .NET 多層應用程式

## 簡介

使用 Visual Studio 和免費 Azure SDK for .NET 開發 Microsoft Azure 很容易。 如果您還沒有 Visual Studio，則 SDK 將自動安裝完全免費的 Visual Studio Express，供您開始進行 Azure 相關開發。 這篇文章假設您從未使用過 Azure。 完成本教學課程之後, 您必須使用的應用程式
執行於您的本機環境中，
示範多層式應用程式的運作方式。

您將了解：

-   如何透過單一下載和安裝讓您的電腦能夠進行 Azure 開發
    。
-   如何使用 Visual Studio 進行 Azure 相關開發。
-   如何使用 Web 與背景工作角色，
    在 Azure 中建立多層式應用程式。
-   如何使用服務匯流排佇列在階層間通訊。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

在本教學課程中，您將在 Azure 雲端服務中建置和執行多層式應用程式。 前端將為 ASP.NET MVC Web 角色，而後端將為背景工作角色。 您可以建立相同的多層應用程式，並使其前端作為部署至 Azure 網站而非雲端服務的 Web 專案。 如需以不同方式執行 Azure 網站前端上的指示，請參閱 [後續步驟](#nextsteps) 一節。

下列螢幕擷取畫面顯示完成的應用程式：

![][0]

> [AZURE.NOTE] Azure 也會提供儲存體佇列功能。 如需 Azure 儲存體佇列和服務匯流排佇列的詳細資訊，請參閱 [Azure 佇列和 Azure 服務匯流排佇列-比較和對照][sbqueuecomparison]。  

## 案例概觀：內部角色通訊

若要提交處理順序，執行於 Web 角色中的前端 UI 元件
web 角色中，必須與執行的中間層邏輯互動
互動。 此範例使用服務匯流排代理的傳訊進行
各層之間的通訊。

在 Web 層與中間層之間使用代理的通訊，可使這兩個元件
彼此脫鉤。 相較於直接傳訊 (亦即，TCP 或 HTTP)
web 層不會直接連接到中間層；而是會以訊息的形式
將工作單位，訊息的形式，發送至服務匯流排的可靠
直到中間層準備好加以取用和處理為止。

服務匯流排提供兩個實體來支援代理的傳訊:
佇列和主題。 使用佇列時，每個傳送至佇列的訊息
都會由單一接收者取用。 主題支援每個已發行的訊息
每個已發佈的訊息會在主題註冊的訂用帳戶可用的模式。 每個訂閱都會以邏輯方式
維護其本身的訊息佇列。 訂閱也可以
使用篩選規則來設定，以將傳至
訂閱佇列的訊息集限定為符合篩選條件的訊息。 下列範例會使用
程式庫。

![][1]

相較於直接傳訊，此通訊機制具有數個優點，
訊息:

-   **暫時分離。**透過非同步傳訊模式，
    產生者和取用者不需要同時處於線上。 服務
    流排會可靠地保管訊息，直到取用方準備好接收訊息
    為止。 這可讓分散式的元件
    在中斷連線時不會對系統造成整體性的影響，
    無論是人為中斷 (例如為了進行維護)，或是由於
    元件損毀。 此外，使用應用程式可能只
    需要在一天的某些時間上線。

-   **負載平衡。**在許多應用程式中，系統負載會隨著時間而改變，
    每個工作單位所需的處理時間時的時間
    通常是固定的。 使用佇列中介訊息生產者與取用者，
    使用應用程式 (背景工作) 只需要佈建來容納平均負載而非尖峰佇列方法
    負載。 佇列的深度成長以及隨著連入的合約
    縮短。 這在為應用程式負載提供服務所需的基礎結構方面，
    將可直接節省經費。

-   **負載平衡。**隨著負載增加，您可以新增更多背景工作處理序
    來讀取佇列。 每則訊息只會由其中一個
    背景工作處理序來處理。 此外，這個提取型負載平衡
    可讓背景工作電腦，即使最有效地利用
    背景工作電腦方面的處理能力，因為它們會以
    最大速率提取訊息而有所不同，仍是如此。 此模式通常
    稱為 *競爭消費者* 模式。

    ![][2]

下列幾節討論實作此架構的程式碼。

## 設定開發環境

在開始開發 Azure 應用程式之前，請先下載工具並設定您的開發環境。

1.  若要安裝 Azure SDK for .NET，請按一下底下連結。

    [取得工具和 SDK (英文)][]

2.  按一下您要使用的 Visual Studio 版本的連結。 本教學課程中的步驟使用 Visual Studio 2013。

    ![][32]

4.  當系統提示您執行或儲存安裝檔案時，按一下 [
    **執行**。

    ![][3]

5.  在 Web Platform Installer 中，按一下 **安裝** 並繼續進行安裝。

    ![][33]

6.  安裝完成之後，您擁有的所有項目
    若要開始開發應用程式的需要。 SDK 包括可讓您的工具
    開發 Visual Studio 中的 Azure 應用程式。 如果您
    未安裝 Visual Studio，則會安裝免費的
    Visual Studio Express for Web。

## 設定服務匯流排命名空間

下一步是建立服務命名空間，並取得共用存取簽章 (SAS) 金鑰。 命名空間提供應用程式的界限
透過服務匯流排公開的每個應用程式。 SAS 金鑰
服務命名空間時，由系統產生
自動產生。 命名空間和 SAS 金鑰的組合
提供的認證來驗證存取權的服務匯流排
重新接收。

### 使用 Azure 傳統入口網站設定命名空間：

1.  登入 [Azure 傳統入口網站][]。

2.  在入口網站的左方瀏覽窗格中，按一下
    **服務匯流排**。

3.  在入口網站的下方窗格中，按一下 [ **建立**。

    ![][6]

4.  在 **加入新的命名空間** 頁面上，輸入命名空間名稱。 系統會立即檢查此名稱是否可用。

    ![][7]

5.  確定命名空間名稱可用之後，請選擇
    您的命名空間應裝載在哪個國家或地區 (請
    確實使用您部署運算資源所在的相同
    國家/地區)。 另外，請確定您選取 **Messaging** 命名空間中 **類型** 欄位，和 **標準** 中 **傳訊層** 欄位。

    > [AZURE.IMPORTANT] 挑選 **同一個地區** 您想要選擇
    部署應用程式。 這樣可以獲得最佳效能。

6.  按一下 [確定] 核取記號。 系統此時會建立您的服務
    命名空間，並加以啟用。 您可能要稍候幾分鐘的時間，
    讓系統為您的帳戶佈建資源。

    ![][27]

7.  在主視窗中，按一下服務命名空間的名稱。

    ![][30]

8. 按一下 [ **連接資訊**。

    ![][31]

9.  在 **存取連線資訊** ] 窗格中，尋找包含 SAS 金鑰和金鑰名稱的連接字串。

    ![][35]

10.  記下這些認證，或將它們複製到剪貼簿。

## 建立 Web 角色

在本節中，您會建置應用程式的前端。 首先，您
建立應用程式顯示的各個頁面。
之後，您新增的程式碼以提交項目給服務匯流排
佇列並顯示佇列的狀態資訊。

### 建立專案

1.  使用系統管理員權限，啟動 Microsoft Visual Studio
    2013 或 Microsoft Visual Studio Express。 若要使用系統管理員權限啟動 Visual
    Studio 系統管理員權限，以滑鼠右鍵按一下 **Microsoft Visual
    Studio 2013 (或 Microsoft Visual Studio Express)** 和
    然後按一下 [ **系統管理員身分執行**。 要使用 Azure 計算模擬器
    討論稍後在本文中，必須以 Visual Studio
    以系統管理員權限啟動。

    在 Visual Studio 中，在 **檔案** ] 功能表上，按一下 [ **新增**, ，然後
    按一下 **專案**。

    ![][8]

2.  從 **已安裝的範本**, 下 **Visual C#**, ，按一下 [ **定域機組** 和
    然後按一下 [ **Azure 雲端服務**。 將專案命名為
    **MultiTierApp**。 然後按一下 [ **確定**。

    ![][9]

3.  從 **.NET Framework 4.5** 角色中，按兩下 **ASP.NET Web
    角色**。

    ![][10]

4.  將滑鼠停留在 **WebRole1** 下 **Azure Cloud Service solution**, ，按一下
    鉛筆圖示，然後將 web 角色重新命名 **FrontendWebRole**。 然後按一下 [ **確定**。 (請確定您輸入 "Frontend"，其中 e 為小寫，而不是 "FrontEnd"。)

    ![][11]

5.  從 **新增 ASP.NET 專案** 對話方塊中，於 **選取範本** 清單中，按一下 **MVC**,，
    然後按一下 [ **確定**。

    ![][12]

6.  在 **方案總管] 中**, ，以滑鼠右鍵按一下 **參考**, ，然後按一下
    **管理 NuGet 封裝** 或 **新增程式庫封裝參考**。

7.  選取 **線上** ] 對話方塊的左側。 搜尋
    「**服務匯流排**」，然後選取 **Microsoft Azure 服務
    匯流排** 項目。 然後完成安裝並關閉此對話方塊。

    ![][13]

8.  請注意，現在會參考必要的用戶端組件，
    並且已新增某些新的程式碼檔案。

9.  在 **方案總管] 中**, ，以滑鼠右鍵按一下 **模型** 按一下 **新增**,，
    然後按一下 [ **類別**。 在 **名稱** 方塊中，輸入名稱
    **OnlineOrder.cs**。 然後按一下 [ **新增**。

### 撰寫 Web 角色的程式碼

在本節中，您將建立應用程式顯示的各個頁面。

1.  在 Visual Studio 的 OnlineOrder.cs 檔案中，
    將現有的命名空間定義取代為下列程式碼：

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  在 **方案總管] 中**, ，按兩下
    **Controllers\ homecontroller.cs**。 新增下列 **使用**
    陳述式，為您剛剛建立的
    您剛才所建立，以及服務匯流排的模型。

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  同時在 Visual Studio 的 HomeController.cs 檔案中，
    將現有的命名空間定義取代為下列程式碼。 此程式碼
    包含處理的項目提交給佇列的方法。

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application.
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit.
                // Controller method for a view you will create for the submission
                // form.
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit.
                // Controller method for handling submissions from the submission
                // form.
                [HttpPost]
                // Attribute to help prevent cross-site scripting attacks and
                // cross-site request forgery.  
                [ValidateAntiForgeryToken]
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  在 **建置** ] 功能表上，按一下 [ **建置方案** 以測試您的工作精確度為止。

5.  現在，建立的檢視 **submit ()** 方法您
    稍早建立的。 以滑鼠右鍵按一下 **submit ()** 方法，然後選擇
    **新增檢視**。

    ![][14]

6.  隨即出現對話方塊，供您建立檢視。 在 **範本** 清單中，選擇 **建立**。 在 **模型類別** 清單中，按一下 **OnlineOrder** 類別。

    ![][15]

7.  按一下 [ **新增**。

8.  現在，變更您的應用程式的顯示名稱。 在 **方案總管] 中**, ，按兩下
    **Views\Shared\\_Layout.cshtml** 視覺效果中開啟檔案
    Studio 編輯器中加以開啟。

9.  取代所有出現的 **My ASP.NET Application** 與
    **LITWARE'S Products**。

10. 移除 **首頁**, ，**有關**, ，和 **連絡人** 連結。 刪除反白顯示的程式碼：

    ![][28]

11. 最後，修改提交頁面，以納入佇列的一些
    相關資訊。 在 **方案總管] 中**, ，按兩下
    **Views\Home\Submit.cshtml** ，在 Visual Studio 中開啟檔案
    編輯器中加以開啟。 加入下列這一行之後 **& lt; h 2 > 提交 & l t; / h 2 >**。 現在，
     **ViewBag.MessageCount** 是空的。 稍後您將在其中填入資料。

        <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>

12. 您現在已實作 UI。 您可以按 **F5** 執行程式
    應用程式，並確認看起來如預期般。

    ![][17]

### 撰寫增程式碼以提交項目給服務匯流排佇列

現在，將新增程式碼以提交項目給佇列。 首先，您
建立的類別，其中包含服務匯流排佇列連線
資訊。 然後，初始化從連線
Global.aspx.cs。 最後，更新的提交程式碼
您先前在 HomeController.cs 中建立的提交程式碼，以將項目實際提交至
服務匯流排佇列。

1.  在 **方案總管] 中**, ，以滑鼠右鍵按一下 **FrontendWebRole** (以滑鼠右鍵按一下專案，而非角色)。 按一下 [ **新增**, ，然後按一下 [ **類別**。

2.  將類別命名為 QueueConnector.cs。 按一下 [ **新增** 建立類別。

3.  現在，加入可封裝連線資訊、並初始化與服務匯流排佇列連線的程式碼。 在 QueueConnector.cs 中，新增下列程式碼，並輸入值 **命名空間** (您的服務命名空間) 和 **yourKey**, ，這是從先前取得的 SAS 金鑰 [Azure 傳統入口網站][]。

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the portal.
                public const string Namespace = "your service bus namespace";

                // The name of your queue.
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations.
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls.
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations.
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already.
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue.
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    稍後在本教學課程中，您將了解如何將名稱儲存您 **命名空間** 和您的 SAS 金鑰值儲存在組態檔。

4.  現在，請確定您 **初始化** 方法會受到呼叫。 在 **方案總管] 中**, ，連按兩下 **Global.asax\Global.asax.cs**。

5.  將下行新增至底部 **Application_Start**
    方法將程式碼部署至服務。

        FrontendWebRole.QueueConnector.Initialize();

6.  最後，更新您先前建立的 web 程式碼至
    將項目提交至佇列。 在 **方案總管] 中**,，
    按兩下 **controllers\ homecontroller.cs**。

7.  更新 **submit ()** 方法，如下所示來取得訊息計數
    佇列中。

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  更新 **Submit (OnlineOrder order)** 方法，如下所示，送出
    訂單資訊給佇列。

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order.
                var message = new BrokeredMessage(order);

                // Submit the order.
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  現在您可以重新執行應用程式。 每次提交
    訊息計數就會增加。

    ![][18]

## 雲端組態管理員

 [GetSetting][] 中的方法
[Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] 類別
可讓您從組態存放區讀取組態設定程式
平台。 例如，如果您的程式碼執行
在 web 或背景工作角色 [GetSetting][] 方法會讀取
ServiceConfiguration.cscfg 檔案，而且如果您的程式碼會執行位於標準
主控台應用程式 [GetSetting][] 方法會讀取 app.config 檔案。

如果您的服務匯流排命名空間中儲存連接字串
您可以使用組態檔 [GetSetting][] 方法來讀取連接
您可以使用來具現化的字串 [NamespaceMananger][] 物件。 您可以
使用 [NamespaceMananger][] 執行個體來設定您的服務匯流排命名空間
以程式設計的方式。 您可以使用相同的連接字串來具現化用戶端
物件 (例如 [QueueClient][], ，[TopicClient][], ，和 [EventHubClient][]
物件)，您可以用來執行執行階段作業，例如傳送和
接收的訊息。

### Connection string

具現化用戶端 (例如，服務匯流排 [QueueClient][])，您可以表示成連接字串的組態資訊。 在用戶端中，`CreateFromConnectionString()` 方法可以使用該連接字串來具現化該用戶端類型。 例如，假設有下列組態區段

    <ConfigurationSettings>
    ...
        <Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=RootManageSharedAccessKey;SharedSecretValue=[yourKey]" />
    </ConfigurationSettings>

下列程式碼會擷取連接字串、建立佇列，以及初始化對佇列的連線。

    QueueClient Client;

    string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

    // Initialize the connection to Service Bus queue.
    Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

下一節中的程式碼會使用 [CloudConfigurationManager][Microsoft.WindowsAzure.Configuration.CloudConfigurationManager] 類別。

## 建立背景工作角色

您現在將建立背景工作角色來處理所提交的
順序。 這個範例會使用 **具有服務匯流排佇列背景工作角色** Visual Studio 專案範本。 首先，使用 Visual Studio 中的 [伺服器總管] 取得必要認證。

1. 請確定您已將 Visual Studio 連接到您的 Azure 帳戶。

2.  在 Visual Studio 中，在 **方案總管] 中** 上按一下滑鼠右鍵
    **角色** 下的資料夾 **MultiTierApp** 專案。

3.  按一下 [ **新增**, ，然後按一下 [ **新背景工作角色專案**。  **新增角色專案** ] 對話方塊隨即出現。

    ![][26]

4.  在 **新增角色專案** 對話方塊中，按一下 [ **具有服務匯流排佇列背景工作角色**。

    ![][23]

5.  在 **名稱** 方塊中，將專案命名為 **OrderProcessingRole**。 然後按一下 [ **新增**。

6.  在 **伺服器總管**, ，以滑鼠右鍵按一下您的服務命名空間的名稱，然後按一下 [ **屬性**。 在 Visual Studio **屬性** ] 窗格中，第一個項目包含已填入命名空間端點包含必要的授權認證的連接字串。 如需範例，請參閱下列螢幕擷取畫面。 按兩下 **ConnectionString**, ，然後按下 **Ctrl + C** 將此字串複製到剪貼簿。

    ![][24]

7.  在 **方案總管] 中**, ，以滑鼠右鍵按一下 **OrderProcessingRole** 您在步驟 5 中建立 (請確定您按一下滑鼠右鍵 **OrderProcessingRole** 下 **角色**, ，而不是類別)。 然後按一下 [ **屬性**。

8.  在 **設定** ] 索引標籤的 **屬性** 對話方塊中，按一下內 **值** 方塊 **Microsoft.ServiceBus.ConnectionString**, ，然後貼上您在步驟 6 中複製的端點值。

    ![][25]

9.  建立 **OnlineOrder** 類別以代表您從佇列處理它們。 您可以重複使用已建立的類別。 在 **方案總管] 中**, ，以滑鼠右鍵按一下 **OrderProcessingRole** 專案 (以滑鼠右鍵按一下專案，不是角色)。 按一下 [ **新增**, ，然後按一下 [ **現有項目**。

10. 瀏覽至子資料夾 **FrontendWebRole\Models**, ，然後按兩下 **OnlineOrder.cs** 將它加入至這個專案。

11. 在 **WorkerRole.cs**, 的值取代 **QueueName** 變數中 **WorkerRole.cs** 從 `"ProcessingQueue"` 到 `"OrdersQueue"` 如下列程式碼所示。

        // The name of your queue.
        const string QueueName = "OrdersQueue";

12. 在 WorkerRole.cs 檔案頂端新增下列 using 陳述式。

        using FrontendWebRole.Models;

13. 在 `Run()` 函式的 `OnMessage` 呼叫內，於 `try` 子句內加入下列程式碼。

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // View the message as an OnlineOrder.
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

14. 您已完成應用程式。 您可以依照先前的方式完整測試
    以滑鼠右鍵按一下 [方案總管] 中的 [MultiTierApp] 專案的應用程式
    選取 **設定為啟始專案**, ，然後按下 F5。 請注意，
    訊息計數不會增加，因為背景工作角色處理的項目
    從佇列和標記以完成。 您所見的追蹤輸出
    背景工作角色，藉由檢視 Azure 計算模擬器 UI。 您
    可以在工作列的通知區域中以滑鼠右鍵按一下通知區域中的模擬器圖示，
    在工作列的區域，然後選取 **顯示計算模擬器 UI**。

    ![][19]

    ![][20]

## 後續步驟  

若要深入了解服務匯流排，請參閱下列資源：  

* [Azure 服務匯流排][sbmsdn]  
* [服務匯流排服務頁面][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  

若要深入了解多層式案例，或了解如何將應用程式部署至雲端服務，請參閱：  

* [使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式][mutitierstorage]  

您可能想要在 Azure 網站而非 Azure 雲端服務中實作多層應用程式的前端。 若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels]。

若要以標準 Web 專案而非雲端服務 Web 角色的形式實作您在本教學課程中建立的應用程式，請在遵循本教學課程中的步驟時注意下列差異：

1. 當您建立專案時，選擇 **ASP.NET MVC Web 應用程式** 中的專案範本 **Web** 類別，而不是 **雲端服務** 範本 **定域機組** 類別。 然後，遵循建立 MVC 應用程式中，直到您進入相同的指示 **雲端組態管理員** 一節。

2. 建立背景工作角色時，請在新的個別方案中建立，類似於 Web 角色的原始指示。 不過，您現在只在雲端服務專案中建立背景工作角色。 然後，遵循建立背景工作角色的相同指示。

3. 您可以個別測試前端與後端，也可以同時在不同 Visual Studio 執行個體中執行這兩者。

若要了解如何將前端部署至 Azure 網站，請參閱 [Azure App Service 中建立 ASP.NET web 應用程式](../app-service-web/web-sites-dotnet-get-started.md)。 若要了解如何將後端部署至 Azure 雲端服務，請參閱 [.NET 多層式應用程式使用儲存體資料表、 佇列和 Blob][mutitierstorage]。


  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: service-bus-azure-and-service-bus-queues-compared-contrasted.md
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [Azure classic portal]: http://manage.windowsazure.com
  [6]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-42-webpi.png
  [35]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

