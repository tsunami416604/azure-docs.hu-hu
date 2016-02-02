<properties
   pageTitle="Service Fabric 應用程式升級教學課程 | Microsoft Azure"
   description="本文會逐步解說使用 Visual Studio 來部署 Service Fabric 應用程式、變更程式碼及執行升級的體驗。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>




# 使用 Visual Studio 進行 Service Fabric 應用程式升級的教學課程

Service Fabric 藉由確保只升級已變更的服務，並且在整個升級程序中監視應用程式健康情況，以及在應用程式發生任何問題時自動復原到舊版，簡化雲端應用程式的升級程序。 「Service Fabric 應用程式升級」並「不需要停機」**，因為可以在不停機的情況下升級應用程式。 本教學課程涵蓋如何從 Visual Studio 完成簡單的輪流升級。


## 步驟 1：建置和發行視覺物件範例

若要執行這些步驟，請從 github 下載應用程式，然後依範例的讀我檔案所述，將 **webgl-utils.js** 和 **gl-matrix-min.js** 檔案新增至專案。 不這麼做的話，應用程式將無法運作。 將這些檔案新增至專案之後，在應用程式專案 **VisualObjectsApplication** 上按一下滑鼠右鍵，然後選取 Service Fabric 功能表項目中的 [發行] 命令來建置和發行應用程式，如以下所示。

![Service Fabric 應用程式的操作功能表][image1]

這會帶出另一個快顯視窗，而您可以將 [連接端點]**** 設定成 [本機叢集]****。 在您按下 [發行]**** 之前，視窗應該看起來如下。

![發行 Service Fabric 應用程式][image2]

現在，您可以按下對話方塊上的 [發行]。 您可以使用 [Service Fabric 總管來檢視叢集與應用程式](service-fabric-visualizing-your-cluster.md)。 視覺物件的應用程式有 web 服務，可以瀏覽至您的瀏覽器中輸入 [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) 網址列中。 您應該會看到 10 個浮動的視覺物件在畫面中四處移動。

## 步驟 2：更新視覺物件範例

您可能會注意到在步驟 1 中已部署的版本，視覺物件不會旋轉。 讓我們將這個應用程式升級到其中的視覺物件也會旋轉的版本。

選取 VisualObjects 解決方案內的 VisualObjects.ActorService 專案，然後開啟 StatefulVisualObjectActor.cs 檔案。 在該檔案內瀏覽至方法 `MoveObject`, ，並註解化 `這。State.Move()` 並取消註解 `這。State.Move(true)`。 這項變更會使得物件在服務升級後能夠旋轉。 現在，您可以建置解決方案 (不是重建)，這會建置已修改的專案 (如果您選取 [全部重建]，則必須更新所有專案的版本)。

我們也需要設定應用程式的版本。 您可以在對解決方案按一下滑鼠右鍵之後，使用 Visual Studio [編輯資訊清單檔案]**** 選項來進行版本變更。 這會帶出可編輯版本的對話方塊，如以下所示：

![版本設定對話方塊][image3]

請選取 [編輯資訊清單版本]**** 索引標籤，然後將已修改之專案及其程式碼封裝連同應用程式的版本一起更新成 2.0.0。 進行變更之後，資訊清單應該會看起來如下 (粗體部分即為所做的變更)：

![更新版本][image4]

Visual Studio 工具可以執行自動彙總套件的版本 (藉由選取 **自動更新應用程式和服務版本**) 如果您使用 [SemVer](http://www.semver.org) -您需要更新的程式碼和/或設定封裝版本如果該選項。 
儲存變更，然後立即核取 [升級應用程式]**** 方塊。


## 步驟 3: 升級您的應用程式

請讓您熟悉 [應用程式升級參數](service-fabric-application-upgrade-parameters.md) 和 [升級程序](service-fabric-application-upgrade.md) 以充分了解各種升級參數、 逾時和健康狀態準則套用。 針對此逐步解說，我們會將服務健康情況評估準則保留在預設值 (UnMonitored 模式)。 您可以選取 [設定升級設定]**** 並視需要修改參數，來設定這些設定。

現在，我們已經準備好選取 [發行]**** 來開始應用程式升級： 這會將您的應用程式升級到其中物件會旋轉的版本 2.0.0。 您將發現 Service Fabric 會一次升級一個升級網域 (某些物件會先更新，其他物件再接著更新)，並且在此期間可透過您的用戶端 (瀏覽器) 存取服務。


現在，隨著應用程式繼續進行升級，您可以使用「Service Fabric 總管」(應用程式底下的 [正在進行升級]**** 索引標籤) 來監視應用程式。

幾分鐘後，應該就已升級 (完成) 所有升級網域，而 Visual Studio 的輸出視窗應該也會指出升級已完成。 而且，您應該會發現瀏覽器視窗中的「所有」**視覺物件現在都已開始旋轉！

您可能會想要嘗試變更版本，從版本 2.0.0 移到版本 3.0.0 來做為練習，或甚至是從版本 2.0.0 移回版本 1.0.0。 練習逾時和健康狀態原則，讓自己更熟練。 當您部署至 Azure 叢集時，所使用的參數將會不同於部署至本機叢集時使用的參數 - 建議保守地設定逾時。


## 後續步驟

[都為您的應用程式使用 Powershell](service-fabric-application-upgrade-tutorial-powershell.md) 逐步引導您完成使用 PowerShell 的應用程式升級。

控制您的應用程式所使用的升級方式 [升級參數](service-fabric-application-upgrade-parameters.md)。

學習如何使用，讓應用程式升級相容 [資料序列化](service-fabric-application-upgrade-data-serialization.md)。

了解如何使用進階的功能，同時升級您的應用程式藉由參考 [進階主題](service-fabric-application-upgrade-advanced.md)。

藉由參考中的步驟修正應用程式升級的一般問題 [疑難排解應用程式升級 ](service-fabric-application-upgrade-troubleshooting.md)。




[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png 
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png 
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png 
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png 

