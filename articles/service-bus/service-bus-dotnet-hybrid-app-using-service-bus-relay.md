<properties
    pageTitle="混合式內部部署/雲端應用程式 (.NET) | Microsoft Azure"
    description="了解如何使用 Azure 服務匯流排轉送建立 .NET 內部部署/雲端混合式應用程式。"
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
    ms.topic="get-started-article"
    ms.date="10/07/2015"
    ms.author="sethm"/>

# 使用 Azure 服務匯流排轉送的 .NET 內部部署/雲端混合式應用程式

## 簡介

下列工具可讓您輕鬆使用 Microsoft Azure 來開發混合式雲端應用程式：
Visual Studio 2013 和免費的 Azure SDK for.NET。 這篇文章
假設您從未使用過 Azure。 在不到
30 分鐘內，您必須使用多個 Azure 資源上的應用程式和雲端中執行。

您將了解：

-   如何建立或選擇現有的 Web 服務供
    Web 解決方案使用。
-   如何使用 Azure 服務匯流排轉送讓
    Azure 應用程式和別處裝載的 Web 服務共用資料。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 服務匯流排轉送如何協助混合式方案

商務解決方案通常由一組自訂程式碼組成，
目的是滿足新的和獨立的商務需求，並運用
既有的解決方案和系統所提供的功能
。

解決方案架構設計人員開始使用雲端來輕鬆處理
調整需求，並降低營運成本。 這麼做之後，他們發現
要用來做為解決方案建置組塊的現有服務資產
都在公司防火牆內，無法供
供雲端解決方案輕易存取。 許多內部服務的
建置或裝載方式，使得它們無法輕易地公開在
公司網路邊緣。

服務匯流排轉送是利用現有的使用案例
Windows Communication Foundation (WCF) Web 服務，並讓
公司週邊外的解決方案能夠安全地存取這些服務，
而不需要侵入式變更公司
網路基礎結構。 這類服務匯流排轉送服務仍然裝載
於現有的環境內，但將接聽
傳入工作階段和雲端裝載的服務匯流排的要求。 服務匯流排也會防止那些服務未經授權的存取使用 [共用存取簽章](https://msdn.microsoft.com/library/dn170478.aspx) (SAS) 驗證。

## 解決方案案例

在本教學課程中，您將建立 ASP.NET MVC 網站，讓您可在產品庫存頁面上看到產品清單。

![][0]

本教學課程假設您在現有的
內部部署系統中有產品資訊，而且使用服務匯流排轉送來存取該
。 這項模擬是由執行在簡單的 web 服務
主控台應用程式中執行，且後背有一組記憶體中的產品所支援。 您
將能夠在自己的電腦上執行此主控台應用程式，並
將 Web 角色部署到 Azure。 如此一來，您將瞭解
Azure 資料中心內執行的 Web 角色實際上如何呼叫
您的電腦，即使幾乎可確定您的電腦
位於至少一個防火牆和網路位址轉譯
(NAT) 層的後方。

下列是已完成之 Web 應用程式的開始頁面螢幕擷取畫面。

![][1]

## 設定開發環境

在開始開發 Azure 應用程式之前，請取得工具，並設定開發環境：

1.  安裝 Azure SDK for.NET， [取得工具和 SDK][]。

2.  按一下 [ **安裝 SDK** 您使用 Visual Studio 的版本。 本教學課程中的步驟使用 Visual Studio 2013。

    ![][42]

4.  當系統提示您執行或儲存安裝程式，按一下 [ **執行**。

    ![][2]

5.  在 **Web Platform Installer**, ，按一下 [ **安裝** 並繼續進行安裝。

    ![][3]

6.  安裝完成之後，您將具備開始開發
    若要開始開發應用程式的需要。 SDK 中的工具可讓您
    在 Visual Studio 中輕鬆開發 Azure 應用程式。 如果您
    未安裝 SDK，則會安裝免費的
    Visual Studio Express。

## 建立服務命名空間

若要開始在 Azure 中使用服務匯流排功能，首先必須建立服務命名空間。 命名空間提供範圍容器，可在應用程式內定址服務匯流排資源。

您可以管理命名空間和服務匯流排訊息實體使用 [Azure 傳統入口網站][] 或 Visual Studio 伺服器總管] 中，但是您只能建立新的命名空間的入口網站中。

### 使用 Azure 傳統入口網站建立命名空間：

1.  登入 [Azure 傳統入口網站][]。

2.  在入口網站的左方瀏覽窗格中，按一下
    **服務匯流排**。

3.  在入口網站的下方窗格中，按一下 [ **建立**。

    ![][5]

4.  在 **加入新的命名空間** ] 對話方塊中，輸入命名空間名稱。
    系統會立即檢查此名稱是否可用。
    ![][6]

5.  確定命名空間名稱可用之後，請選擇
    您的命名空間應裝載在哪個國家或地區 (請
    確實使用您部署運算資源所在的相同
    國家/地區)。

    > [AZURE.IMPORTANT] 挑選 *同一個地區* 您想要的選擇
    來部署應用程式。 這樣可以獲得最佳效能。

6.  讓對話方塊中的其他欄位保留其預設值 (**Messaging** 和 **標準** 層)，然後按一下核取記號。 此時系統會建立並啟用命名空間。 系統為帳戶提供資源時，您可能需要等幾分鐘。

    ![][38]

您所建立的命名空間會出現在 Azure 傳統入口網站，不過啟動可能需要花一些時間。 等到狀態變成 **Active** 之後再繼續。

## 取得命名空間的預設管理認證

為了在新的命名空間上執行管理作業，例如建立訊息實體，您必須取得命名空間的認證。

1.  在主視窗中，按一下服務命名空間的名稱。

    ![][39]

2.  按一下 [ **連接資訊**。

    ![][40]

3.  在 **存取連線資訊** ] 窗格中，尋找包含 SAS 金鑰和金鑰名稱的連接字串。

    ![][45]

4.  記下這些認證，或將它們複製到剪貼簿。

## 建立內部部署伺服器

首先，您將建置 (模擬) 內部部署產品目錄系統。 此程式碼
非常簡單，您可以將這視為實際的
內部部署產品目錄系統，具有我們試著整合的完整服務介面
。

此專案啟動為 Visual Studio 主控台應用程式。 Auch die Eigenschaften
專案使用服務匯流排 NuGet 封裝來包含服務匯流排
程式庫和組態設定。 NuGet Visual Studio 擴充功能
可讓您在 Visual Studio 和 Visual Studio Express 中輕鬆地安裝及更新程式庫和工具
。 服務匯流排 NuGet 封裝是最簡單
的方法，可讓您取得服務匯流排 API，並為您的應用程式設定
所有服務匯流排相依性。 如需有關使用 NuGet 和
服務匯流排套件，請參閱 [使用 NuGet 服務匯流排封裝][]。

### 建立專案

1.  使用系統管理員權限，啟動 Microsoft Visual
    Studio 2013 或 Microsoft Visual Studio Express。 收件人
    以系統管理員權限啟動 Visual Studio 中，以滑鼠右鍵按一下
    **Microsoft Visual Studio 2013 (或 Microsoft Visual Studio Express)** 然後按一下 [ **系統管理員身分執行**。

2.  在 Visual Studio 中，在 **檔案** ] 功能表上，按一下 [ **新增**, ，然後
    按一下 **專案**。

    ![][10]

3.  從 **已安裝的範本**, 下 **Visual C#**, ，按一下 [ **主控台
    應用程式**。 在 **名稱** 方塊中，輸入名稱
    **ProductsServer**:

    ![][11]

4.  按一下 [ **確定** 建立 **ProductsServer** 專案。

5.  在 [方案總管] 中，以滑鼠右鍵按一下 **ProductsServer**, ，然後
    按一下 [ **屬性**。

6.  按一下 [ **應用程式** 左邊的索引標籤，然後確定 **.NET
    Framework 4** 或 **.NET Framework 4.5** 會出現在 **目標 framework** 清單。 如果沒有，請從清單中選取，然後按一下 **是** 當系統提示您重新載入專案。

    ![][12]

7.  如果已安裝 Visual Studio 的 NuGet 套件管理員，請跳至下一個步驟。 否則，請造訪 [NuGet][] 按一下 [安裝 NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)。 按照提示安裝 NuGet 套件管理員，然後重新啟動 Visual Studio。

7.  在 [方案總管] 中，以滑鼠右鍵按一下 **參考**, ，然後按一下
    **管理 NuGet 封裝**。

8.  在左欄中的 **NuGet** 對話方塊中，按一下 [ **線上**。

9.  在右欄中，按一下 [ **搜尋** 方塊中，輸入 「**服務匯流排**」，然後選取 **Microsoft
    Azure 服務匯流排** 項目。 按一下 [ **安裝** 完成
    安裝，然後關閉對話方塊。

    ![][13]

    請注意，現在會參考必要的用戶端組件。

9.  新增產品連絡人的新類別。 在 [方案總管] 中，
    以滑鼠右鍵按一下 **ProductsServer** 專案，然後按一下 **新增**, ，然後按一下
    **類別**。

    ![][14]

10. 在 **名稱** 方塊中，輸入名稱 **ProductsContract.cs**。 然後
    按一下 [ **新增**。

11. 在 **ProductsContract.cs**, ，將命名空間定義換成
    下列程式碼，其中定義服務合約。

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties.
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. 在 Program.cs 中，將命名空間定義換成下列
    程式碼，加入設定檔服務和其主機。

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface.
            class ProductsService : IProducts
            {

                // Populate array of products for display on website.
                ProductData[] products =
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock",
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper",
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors",
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well",
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application
                // when the list of products is retrieved.
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application.
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. 在 [方案總管] 中，按兩下 **App.config** 檔案重新命名為
    在 Visual Studio 編輯器中開啟它。 將
    **& l t; 系統。ServiceModel & gt;** 下列 XML 程式碼中。 請務必
    取代 *yourServiceNamespace* 與您的服務名稱
    命名空間，以及 *yourKey* 與您先前擷取的 SAS 金鑰
    從 Azure 傳統入口網站:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. 按 F6 或從 **建置** ] 功能表上，按一下 [ **建置方案** 來建置應用程式到目前為止確認工作的準確性。

## 建立 ASP.NET MVC 應用程式

在本節中，您將建置簡單的 ASP.NET 應用程式，來顯示從產品服務擷取的資料。

### 建立專案

1.  確定 Visual Studio 是以系統管理員權限來執行。 如果不是，若要
    以系統管理員權限啟動 Visual Studio 中，以滑鼠右鍵按一下
    **Microsoft Visual Studio 2013 (或 Microsoft Visual Studio Express)** 然後按一下 [ **系統管理員身分執行**。 Microsoft Azure 計算模擬器，稍後在本文中討論過需要
    系統管理員權限啟動 visual Studio。

2.  在 Visual Studio 中，在 **檔案** ] 功能表上，按一下 [ **新增**, ，然後
    按一下 **專案**。

3.  從 **已安裝的範本**, 下 **Visual C#**, ，按一下 [ **ASP.NET Web 應用程式**。 將專案命名為 **ProductsPortal**。 然後
    按一下 [ **確定**。

    ![][15]

4.  從 **選取範本** 清單中，按一下 **MVC**,，
    然後按一下 [ **確定**。

    ![][16]

5.  在 [方案總管] 中，以滑鼠右鍵按一下 **模型** 然後按一下 [ **新增**,，
    然後按一下 [ **類別**。 在 **名稱** 方塊中，輸入名稱
    **Product.cs**。 然後按一下 [ **新增**。

    ![][17]

### 修改 Web 應用程式

1.  在 Visual Studio 中，將 Product.cs 檔案中現有的
    命名空間定義取代為下列程式碼。

        // Declare properties for the products inventory.
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  在 Visual Studio 中，將 HomeController.cs 檔案中現有的
    命名空間定義取代為下列程式碼。

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory.
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  在 [方案總管] 中，展開 Views\Shared 資料夾。

    ![][18]

4.  按兩下 **_Layout.cshtml** 在 Visual Studio 編輯器中開啟它。

5.  將所有 **My ASP.NET Application** 至 **LITWARE's Products**。

6. 移除 **首頁**, ，**有關**, ，和 **連絡人** 連結。 在下列範例中，刪除反白顯示的程式碼。

    ![][41]

7.  在 [方案總管] 中，展開 Views\Home 資料夾。

    ![][20]

8.  按兩下 **Index.cshtml** 在 Visual Studio 編輯器中開啟它。
    將檔案的整個內容取代為下列程式碼。

        @model IEnumerable<ProductsWeb.Models.Product>

        @{
            ViewBag.Title = "Index";
        }

        <h2>Prod Inventory</h2>

        <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>

        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
        }

        </table>

9.  若要驗證是否正確，您的工作到目前為止，您可以按 **F6** 或
    **Ctrl + Shift + B** 來建置專案。


### 在本機執行您的應用程式

執行應用程式以驗證它是否能正常運作。

1.  請確認 **ProductsPortal** 為作用中專案。 以滑鼠右鍵按一下
    專案名稱在 [方案總管]，然後選取 **設為
    啟始專案**。
2.  在 **Visual Studio**, ，請按 F5。
3.  您的應用程式應該就會出現在瀏覽器中並正在執行。

    ![][21]

## 將應用程式準備好部署到 Azure

您可以將您的應用程式部署到 Azure 雲端服務或 Azure 網站。 若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels]。 若要了解如何部署至 Azure 網站應用程式，請參閱 [ASP.NET Web 應用程式部署至 Azure 網站](http://azure.microsoft.com/develop/net/tutorials/get-started/)。 本節包含將應用程式部署至 Azure 雲端服務的詳細步驟。

若要將應用程式部署至雲端服務，您需要新增雲端服務專案部署專案至方案。 部署專案包含在雲端適當地執行應用程式所需的組態資訊。

1.  若要讓您的應用程式可以部署至雲端，以滑鼠右鍵按一下 **ProductsPortal** 專案方案總管] 中，按一下 **轉換**, ，然後按一下 [ **Microsoft Azure 雲端服務專案轉換成**。

    ![][22]

2.  若要測試應用程式，請按 F5。

3.  這將啟動 Azure 運算模擬器。 運算模擬器會使用本機電腦來模擬 Azure 中執行的應用程式。 您可以查看系統匣來確認模擬器是否已啟動。

       ![][23]

4.  瀏覽器仍將顯示本機執行的應用程式，而且它的外觀和運作方式，將和您先前將它當作一般 ASP.NET MVC 4 應用程式執行時相同。

## 組合在一起

下一步是利用 ASP.NET MVC 應用程式連接內部部署產品伺服器。

1.  如果它尚未開啟，請在 Visual Studio 重新開啟 **ProductsPortal** 您在 < 建立 ASP.NET MVC 應用程式 」 一節中建立的專案。

2.  類似於＜建立內部部署伺服器＞一節中的步驟，將 NuGet 套件新增至 References 專案。 在 [方案總管] 中，以滑鼠右鍵按一下 **參考**, ，然後按一下 [ **管理 NuGet 封裝**。

3.  搜尋 「 服務匯流排 」 並選取 **Microsoft Azure 服務匯流排** 項目。 然後完成安裝並關閉此對話方塊。

4.  在 [方案總管] 中，以滑鼠右鍵按一下 **ProductsPortal** 專案，然後按一下 **新增**, ，然後 **現有項目**。

5.  瀏覽至 **ProductsContract.cs** 檔案從 **ProductsServer** 主控台專案。 按一下以反白顯示 ProductsContract.cs。 按一下向下箭號旁 **新增**, ，然後按一下 [ **加入做為連結**。

    ![][24]

6.  現在開啟 **HomeController.cs** Visual Studio 編輯器中的檔案，並將命名空間定義取代下列程式碼。 請務必取代 *yourServiceNamespace* 服務命名空間的名稱和 *yourKey* 與您的 SAS 金鑰。 這可讓用戶端呼叫內部部署服務，並傳回呼叫結果。

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory.
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication.
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory.
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  在 [方案總管] 中，以滑鼠右鍵按一下 **ProductsPortal**方案中，按一下 [ **新增**, ，然後按一下 [ **現有專案**。

8.  瀏覽至 **ProductsServer** 專案，然後按兩下 **ProductsServer.csproj** 方案檔來新增它。

9.  在 [方案總管] 中，以滑鼠右鍵按一下 **ProductsPortal** 方案，然後按一下 **屬性**。

10. 在左側，按一下 [ **啟始專案**。 在右側，按一下 [ **多個啟始專案**。 請確認 **ProductsServer**, ，**ProductsPortal.Azure**, ，和 **ProductsPortal** 出現，請使用 **啟動** 設為動作 **ProductsServer** 和 **ProductsPortal.Azure**, ，和 **無** 設為動作 **ProductsPortal**。

      ![][25]

11. 仍在 **屬性** 對話方塊中，按一下 [ **ProjectDependencies** 左邊。

12. 在 **專案** 清單中，按一下 **ProductsServer**。 請確認 **ProductsPortal** 未選取，並 **ProductsPortal.Azure** 已選取。 然後按一下 [ **確定**:

    ![][26]

## 執行應用程式

1.  從 **檔案** ] 功能表，在 Visual Studio 中，按一下 [ **全部儲存**。

2.  按 F5 以建置並執行應用程式。 內部部署伺服器 ( **ProductsServer** 主控台應用程式) 應該會第一個啟動，然後在 **ProductsWeb** 應用程式應該啟動瀏覽器視窗中，如下列螢幕擷取畫面所示。 此時，您將看到從產品服務內部部署系統擷取的產品庫存清單資料。

    ![][1]

## 將應用程式部署至 Azure

1.  以滑鼠右鍵按一下 **ProductsPortal** 專案在 [方案總管]，然後按一下 **發佈到 Microsoft Azure**。

2.  您可能必須登入，才能看到所有訂用帳戶。

    按一下 [ **登入以查看更多的訂閱**:

    ![][27]

3.  使用 Microsoft 帳戶登入。

8.  按一下 [ **下一步**。 如果訂用帳戶未包含任何託管服務，系統會要求您建立一個。 託管服務會充當 Azure 訂用帳戶內應用程式的容器。 輸入應用程式的識別名稱，並選擇應該最佳化其應用程式的區域。 (從這個區域存取它的使用者預期會有更快的載入時間。)

9.  選取應用程式發佈所在的託管服務。 其餘設定請保留如下所示的預設值。 按一下 [ **下一步**。

    ![][33]

10. 在最後一個頁面上，按一下 [ **發行** 開始部署程序。

    ![][34]
這將需要大約 5-7 分鐘的時間。 因為這是您第一次發佈，所以 Azure 會佈建虛擬機器 (VM)、執行安全性強化、在 VM 上建立 Web 角色來主控應用程式、將程式碼部署至該 Web 角色，最後並設定負載平衡器及網路，以便應用程式可供大眾使用。

11. 在進行發行時您將能夠中監視活動 **Azure 活動記錄檔** 視窗，通常停駐於 Visual Studio 或 Visual Web Developer 底部。

    ![][35]

12. 部署完成時，您可以檢視您的網站，即可 **網站 URL** 監視視窗中的連結。

    ![][36]

    您的網站依賴內部部署伺服器，因此您必須執行 **ProductsServer** 本機應用程式的網站才能正常運作。 在雲端網站上執行要求時，您將看到要求進入內部部署主控台應用程式，如下列螢幕擷取畫面中所顯示的 "GetProducts called" 輸出所指出。

    ![][37]

若要深入了解網站與雲端服務之間的差異，請參閱 [Azure 執行模型][executionmodels]。

## 停止並刪除您的應用程式

部署您的應用程式後，您可能想要停用它，以便您
在 750 小時/月 (31 天/月) 的免費伺服器時間內，再建置及部署其他應用程式
。

Azure 會依耗用的每小時伺服器時間對 Web 角色執行個體計費。
取用。 您的應用程式部署之後就會開始耗用伺服器時間，
即使執行個體未執行且處於停止狀態也一樣。
免費帳戶有 750 小時/月 (31 天/月) 的專用
虛擬機器伺服器時間可託管這些 Web 角色執行個體。

下列步驟示範如何停止並刪除您的
重新接收。

1.  登入 [Azure 傳統入口網站][], ，按一下 [ **雲端服務**, ，然後按一下您的服務名稱。

2.  按一下 [ **儀表板** 索引標籤，然後再按一下 **停止** 來暫停應用程式。 您可以按一下 [重新啟動 **啟動**。 按一下 [ **刪除** 再將其還原到也無法從 Azure 完全移除應用程式。

    ![][43]

## 後續步驟  

若要深入了解服務匯流排，請參閱下列資源：  

* [Azure 服務匯流排][sbwacom]  
* [如何使用服務匯流排佇列][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Get Tools and SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-42-webpi.png


  [Azure classic portal]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [Using the NuGet Service Bus Package]: https://msdn.microsoft.com/library/azure/dn741354.aspx
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

