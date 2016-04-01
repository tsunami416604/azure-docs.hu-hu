<properties
    pageTitle="使用 Visual Studio Team Services 對應用程式執行負載測試 | Microsoft Azure"
    description="了解如何使用 Visual Studio Team Services 對您的 Azure Service Fabric 應用程式執行壓力測試。"
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# 使用 Visual Studio Team Services 對您的應用程式執行負載測試

本文說明如何使用 Visual Studio 負載測試功能對應用程式進行壓力測試。 它會使用 Service Fabric 具狀態服務後端和無狀態服務 Web 前端。 以下使用的範例應用程式是飛機位置模擬器。 您提供飛機識別碼、起飛和目的地位置。 應用程式的後端會處理要求，前端會顯示地圖上與準則相符的飛機。

下圖說明您測試的 Service Fabric 應用程式。

![][0]

## 必要條件
開始之前，您需要執行下列動作。

- 取得 Visual Studio Team Services (VSTS) 帳戶。 您可以取得一個免費在 [Visual Studio Team Services](https://www.visualstudio.com)。
- 取得並安裝 Visual Studio 2013 或 Visual Studio 2015。 本文使用 Visual Studio 2015 Enterprise 版本，但是 Visual Studio 2013 和其他版本應該也同樣可以運作。
- 將您的應用程式部署至預備環境。 請參閱 [如何部署應用程式至遠端叢集使用 Visual Studio](service-fabric-publish-app-remote-cluster.md) 資訊。
- 了解您的應用程式的使用模式。 這項資訊是用來模擬負載模式。
- 了解您的負載測試的目標。 這可協助您解譯和分析負載測試結果。

## 建立及執行 Web 效能和負載測試專案

### 建立 Web 效能和負載測試專案

1. 開啟 Visual Studio 2015。 選擇 **檔案** > **新增** > **專案** 若要開啟的功能表列上 **新的專案** 對話方塊。

1. 展開 **Visual C#** 節點，然後選擇 **測試** > **Web 效能和負載測試專案**。 指定專案的名稱，然後選擇 [ **確定** ] 按鈕。

    ![][1]

    您應該會看到新的 Web 效能和負載測試專案。

    ![][2]

### 記錄 Web 效能測試

1. 開啟 .webtest 專案。

1. 選擇 **加入錄製** 圖示，以啟動瀏覽器中的錄製工作階段。

    ![][3]

    ![][4]

1. 瀏覽至 Service Fabric 應用程式。 [錄製] 面板應該會顯示 Web 要求。

    ![][5]

1. 執行您預期使用者執行的一系列動作。 這些動作會當做產生負載的模式。

1. 當您完成時，選擇 [ **停止** 停止錄製] 按鈕。

    ![][6]

    Visual Studio 中的 .webtest 專案應該已擷取一系列的要求。 會自動取代動態參數。 此時，您可以刪除不屬於您的測試案例的任何額外、重複相依性要求。

1. 儲存專案，然後選擇 [ **執行測試** 命令在本機執行 web 效能測試，以確保一切運作正常。

    ![][7]

### 參數化 Web 效能測試

您可以將 Web 效能測試轉換成 Web 效能測試程式碼，然後編輯程式碼，以參數化 Web 效能測試。 或者，您可以將 Web 效能測試繫結至資料清單，以便測試逐一查看資料。 請參閱 [產生和執行自動程式碼的 web 效能測試](https://msdn.microsoft.com/library/ms182552.aspx) 的詳細資料如何轉換 web 效能測試來測試程式碼和 [將資料來源加入至 web 效能測試](https://msdn.microsoft.com/library/ms243142.aspx) 如需如何將資料繫結至 web 效能測試。

對於此範例，我們會將 Web 效能測試轉換成程式碼測試，您就可以使用產生的 GUID 取代飛機識別碼，並且加入更多要求以將航班傳送至不同位置。

### 建立負載測試專案

負載測試專案是由 Web 效能測試和單位測試所描述的一或多個案例，以及其他指定的負載測試設定組成。 下列步驟說明如何建立負載測試專案。

1. 在您的 Web 效能和負載測試專案的捷徑功能表，選擇 [ **新增** > **負載測試**。 在 **負載測試** 精靈] 中，選擇 [ **下一步** ] 按鈕以設定測試設定。

1. 在 **負載模式** 區段中，選擇是否要常數負載或逐步執行負載，一開始數個使用者，會隨著時間增加使用者。

    如果您的使用者負載量有良好的評估，而且想要查看目前系統的運作方式，選擇 [ **常數負載**。 如果您的目標是要了解是否在系統執行一致的方式不同數量的負載下，選擇 [ **逐步執行負載**。

1. 在 **測試混合** 區段中，選擇 **新增** 按鈕，然後選取您想要包含在負載測試的測試。 您可以使用 **發佈** 資料行，指定執行每個測試的測試總計的百分比。

1. 在 **回合設定** 區段中，指定負載測試持續期間。
    >[AZURE.NOTE]  **測試反覆項目** 會提供選項只有在您執行負載測試使用 Visual Studio 在本機。

1. 在 **位置** 區段 **回合設定**, ，指定負載測試要求所產生的位置。 此精靈可能會提示您登入您的 Team Services 帳戶。 使用您的 Team Services 帳戶登入，然後選擇地理位置。 當您完成時，選擇 [ **完成** ] 按鈕。

1. 建立負載測試之後，開啟.loadtest 專案，然後選擇 [執行設定，例如目前 **回合設定** > **回合設定 1 [作用中]**。 這會開啟中的執行的設定 **屬性** 視窗。

1. 在 **結果** 區段 **回合設定** 屬性] 視窗中， **計時詳細資料儲存區** 設定應有 **無** 做為其預設值。 變更此值可 **所有個別細節** 以取得詳細資訊，在負載測試結果。 請參閱 [載入測試](https://www.visualstudio.com/load-testing.aspx) 如需有關如何連接至 Visual Studio Team Services，以及執行負載測試。

### 使用 Visual Studio Team Services 執行負載測試

選擇 **執行負載測試** 命令來啟動測試回合。

![][8]

## 檢視及分析負載測試結果

隨著負載測試進行，效能資訊會圖表化。 您應該會看到類似下面的圖形。

![][9]

1. 選擇 **下載報表** 頁面頂端附近的連結。 下載報表之後，請選擇 **檢視報表** ] 按鈕。

    在 **圖形** ] 索引標籤，您可以看到各種效能計數器的圖形。 在 **摘要** ] 索引標籤，整體測試結果會出現。 在 **資料表** 索引標籤上，成功和失敗的負載測試的總數會出現。

1. 選擇數字的連結上 **測試** > **失敗** 和 **錯誤** > **計數** 欄位以查看錯誤詳細資料。

     **詳細** ] 索引標籤會顯示失敗的要求的虛擬使用者和測試案例資訊。 如果負載測試包含多個案例，此資料相當有用。

請參閱 [分析負載測試結果中的 [負載測試分析器] 的 [圖形] 檢視](https://www.visualstudio.com/load-testing.aspx) 如需有關檢視負載測試結果。

## 自動化您的負載測試

Visual Studio Team Services 負載測試提供的 API 可讓您管理負載測試及分析 Team Services 帳戶中的結果。 請參閱 [雲端負載測試 Rest Api](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) 如需詳細資訊。

## 後續步驟
- [監視和診斷本機開發設定中的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png


