<properties
   pageTitle="建立應用程式的 Web 前端 | Microsoft Azure"
   description="使用 ASP.NET 5 Web API 專案對 Web 公開 Service Fabric 應用程式，以及透過 ServiceProxy 進行服務間通訊。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/21/2015"
   ms.author="seanmck"/>



# 建置應用程式的 Web 服務前端

根據預設，Service Fabric 服務不提供 Web 的公用介面。 若要對 HTTP 用戶端公開應用程式的功能，您必須建立 Web 專案來做為進入點，然後從該處與個別服務進行通訊。

在本教學課程中，我們將逐步解說如何將 ASP.NET 5 Web API 前端新增至已依據具狀態服務專案範本包含「可靠的服務」的應用程式。 如果您尚未這麼做，請考慮將逐步解說 [Visual Studio 中建立第一個應用程式](service-fabric-create-your-first-application-in-visual-studio.md) 之前開始本教學課程。


## 將 ASP.NET 5 服務新增至您的應用程式

ASP.NET 5 是輕量型、跨平台的 Web 開發架構，能夠建立新式 Web UI 和 Web API。 讓我們將 ASP.NET Web API 專案新增至現有的應用程式。

1. 在 [方案總管] 中，以滑鼠右鍵按一下應用程式專案中的 [服務]****，然後選擇 [新增 Fabric Service]****。

    ![將新服務加入至現有的應用程式][vs-add-new-service]

2. 在新的服務對話方塊中，選擇 **ASP.NET 5** 並予以命名。

    ![在新服務對話方塊中選擇 ASP.NET Web 服務][vs-new-service-dialog]

3. 下一個對話方塊提供一組 ASP.NET 5 專案範本。 請注意，這些都是您在 Service Fabric 應用程式外部建立 ASP.NET 5 專案時所會看到的相同範本。 在本教學課程中，我們會選擇 **Web API**，但您可以將相同的概念套用於建置完整的 Web 應用程式。

    ![選擇 ASP.NET 專案類型][vs-new-aspnet-project-dialog]

    建立 Web API 專案後，您的應用程式中會有兩個服務。 隨著您繼續建置應用程式，您將以完全相同的方式加入更多服務，而每個服務都可以獨立設定版本和升級。


## 執行應用程式

若要了解我們所做的事情，就讓我們部署新的應用程式並看看 ASP.NET 5 Web API 範本所提供的預設行為。

1. 在 Visual Studio 按 F5 以進行應用程式偵錯。

2. 部署完成時，Visual Studio 會啟動瀏覽器以 ASP.NET Web API 服務，如下所示 http://localhost:33003 (連接埠號碼會隨機指派和可能與您的電腦不同) 的根目錄。 ASP.NET 5 Web API 範本不根提供根目錄的預設行為，因此您將在瀏覽器中收到錯誤。

3. 新增 `/api/值` 瀏覽器中的位置。 這將會叫用 `取得` 方法中的 Web API 範本和傳回預設回應 ValuesController 範本所提供，JSON 陣列包含兩個字串:

    ![從 ASP.NET 5 Web API 範本傳回的預設值][browser-aspnet-template-values]

    在本教學課程結束前，我們會以具狀態服務的最新計數器值取代這些預設值。


## 連接服務

對於您與可靠服務通訊的方式，Service Fabric 會提供完整的彈性。 在單一應用程式中，您可能有可透過 TCP 存取的服務、可透過 HTTP REST API 存取的服務，以及可透過 Web 通訊端存取的服務。 如需有關可用的選項和權衡取捨的背景，請參閱 [與服務進行通訊](service-fabric-connect-and-communicate-with-services.md)。 在本教學課程中，我們將遵循下列其中一個更簡單的方法，並使用 `ServiceProxy`/`ServiceRemotingListener` SDK 中提供的類別。

在 `ServiceProxy` (建立模型的遠端程序呼叫或 RPC) 的方法定義做為公用，讓服務合約，然後使用該介面產生 proxy 類別，用於與服務互動的介面。


### 建立介面

我們會先建立做為具狀態服務與其用戶端之間合約的介面，包括 ASP.NET 5 專案。

1. 在 [方案總管] 中，以滑鼠右鍵按一下您的方案並選擇 [加入] > [新專案]****。

2. 在左側導覽窗格中選擇 Visual C# 項目，然後選取 [類別庫]**** 範本。 確定 .NET Framework 版本已設定為 4.5.1。

    ![為具狀態服務建立介面專案][vs-add-class-library-project]

3. 介面可供順序 `ServiceProxy`, ，它必須衍生自 IService 介面，其中包含在其中一個服務網狀架構 NuGet 封裝。 若要新增封裝，請以滑鼠右鍵按一下新的類別庫專案，然後選擇 [管理 NuGet 封裝]****。

4. 確定已選取 [包含發行前版本]**** 核取方塊，然後搜尋 **Microsoft.ServiceFabric.Services** 封裝並加以安裝。

    ![新增服務 NuGet 封裝][vs-services-nuget-package]

5. 在類別庫中，建立介面具有單一方法， `GetCountAsync`, ，並從 IService 延伸介面。

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### 在具狀態服務中實作介面

既然我們已定義介面，我們必須在具狀態服務中實作該介面。

1. 在具狀態服務中，新增含有此介面之類別庫專案的參考。

    ![新增具狀態服務中的類別庫專案的參考][vs-add-class-library-reference]

2. 找出類別繼承自 `StatefulService`, ，例如 `MyStatefulService`, ，並將它實作延伸 `ICounter` 介面。

    ```c#
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 定義單一方法的實作了 `ICounter` 介面， `GetCountAsync`。

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### 使用 ServiceRemotingListener 公開具狀態服務

使用 `ICounter` 實作的介面，讓可設定狀態的服務可以呼叫其他服務的最後一個步驟是開啟的通訊通道。 具狀態服務，Service Fabric 會提供呼叫可覆寫方法 `CreateServiceReplicaListeners` ，您可以指定一或多個您想要啟用您的服務通訊的類型為基礎的通訊接聽程式。
>[AZURE.NOTE] 開啟無狀態服務的通訊通道的對應方法會呼叫 `CreateServiceInstanceListeners`。

在此情況下，我們將提供 `ServiceRemotingListener`, ，它會建立 RPC 結束點呼叫從用戶端使用 `ServiceProxy`。

```c#
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceRemotingListener<ICounter>(initParams, this))
    };
}
```


### 使用 ServiceProxy 來與服務互動

我們的具狀態服務現在已準備好接收來自其他服務的流量，所以剩餘的工作就是新增程式碼以便從 ASP.NET Web 服務與之通訊。

1. 在 ASP.NET 專案中，加入到包含類別程式庫的參考 `ICounter` 介面。

2. 將 Microsoft.ServiceFabric.Services 封裝新增至 ASP.NET 專案，就如同先前對類別庫專案所做的一樣。 這會提供 `ServiceProxy` 類別。

3. 在 controllers 資料夾中，開啟 `ValuesController` 類別。 請注意， `取得` 方法目前只會傳回"value1"和"value2"，用來比對我們稍早在瀏覽器中看到的硬式編碼的字串陣列。 使用下列程式碼來取代此實作：

    ```c#
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    第一行程式碼是關鍵程式碼。 若要建立具狀態服務的 ICounter Proxy，您必須提供兩項資訊：資料分割識別碼和服務名稱。

    資料分割可讓您根據您定義的索引鍵 (例如客戶識別碼或郵遞區號) 將服務狀態劃分為不同的值區，藉此調整具狀態服務。 在我們的簡單應用程式中，具狀態服務只有一個資料分割，所以索引鍵並不重要 - 您所提供的任何索引鍵將會造成相同的資料分割。 若要深入了解分割服務，請參閱 [如何分割 Service Fabric 可靠服務](service-fabric-concepts-partitioning)。

    服務名稱是 URI 形式網狀架構: / < application_name > / < 服務名稱 >。

    利用這兩項資訊，Service Fabric 即可唯一識別要求應傳送至的電腦。  `ServiceProxy` 並且順暢地處理裝載我們可設定狀態服務分割區的電腦失敗的且必須升級另一部電腦，來取代它的位置。 此概念讓撰寫用戶端程式碼來處理其他服務變得簡單許多。

    一旦我們擁有的 proxy，我們只叫用 `GetCountAsync` 方法，並傳回其結果。

4. 再次按 F5 以執行修改過的應用程式。 像之前一樣，Visual Studio 會自動啟動瀏覽器並瀏覽至 Web 專案的根目錄。 新增 "api/values" 路徑，您應該會看到傳回的目前計數器值。

    ![在瀏覽器中顯示的具狀態計數器值][browser-aspnet-counter-value]

    定期重新整理瀏覽器，以查看計數器值更新。


## 動作項目呢？

本教學課程著重於新增會與具狀態服務通訊的 Web 前端，但是您可以依照非常類似的模型來與動作項目交談。 事實上，這比較簡單。

當您建立動作項目專案時，Visual Studio 自動替您產生介面專案。 您可以使用該介面在 Web 專案中產生動作項目 Proxy 來與動作項目進行通訊。 因此您不需要執行任何動作相當於建立通訊通道會自動地提供 `ServiceRemotingListener` 您在本教學課程對具狀態服務。

## 在本機叢集上執行 Web 服務

一般而言，您可以將完全相同的 Service Fabric 應用程式部署到您在本機叢集上部署之多部電腦的叢集，而且極度相信它會如預期般運作，因為您的本機叢集只是摺疊成單一機器的五個節點的組態。

不過，提到 Web 服務，有一個重要細微差別。 當您的叢集位於負載平衡器後方時，如同在 Azure 中一樣，您必須確定 Web 服務已部署在每一部機器上，因為負載平衡器只會將流量循環配置於各電腦。 這可以經由設定 `InstanceCount` 特殊的值為-1 的服務。 相反地，在本機執行時，您需要確保只有一個服務執行個體正在執行。否則，您將會遇到多個程序同時在相同的路徑和連接埠上接聽的衝突。 因此，本機部署的 Web 服務執行個體計數應該設為 1。

若要了解如何設定不同的值不同的環境，請參閱 [管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)。

## 後續步驟

- [在 Azure 雲端應用程式部署中建立叢集](service-fabric-cluster-creation-via-portal.md)
- [深入了解與服務進行通訊](service-fabric-connect-and-communicate-with-services.md)
- [深入了解資料分割可設定狀態服務](service-fabric-concepts-partitioning.md)




[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png 
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png 
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png 
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png 
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png 
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png 
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png 
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png 

