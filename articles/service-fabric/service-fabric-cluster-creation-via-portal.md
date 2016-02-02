<properties
   pageTitle="從 Azure 入口網站設定 Service Fabric 叢集 | Microsoft Azure"
   description="從 Azure 入口網站設定 Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="chackdan"/>


# 從 Azure 入口網站設定 Service Fabric 叢集

本頁面協助您設定 Service Fabric 叢集。 假設您的訂用帳戶有足夠的核心可部署將要構成此叢集的 IaaS VM


## 建立叢集

1. 登入 Azure 入口網站 [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)。

2. 按一下 [+新增]**** 加入新的資源範本。 搜尋為我們下所有項目-Marketplace 中的範本進行呼叫 **Service Fabric 叢集**
(您可以瀏覽至 **一切** 按一下最上層類別的服務商場 > 然後搜尋 「 結構 」 下的 [全部內容並按下 enter 鍵-有時自動篩選無法運作，因此請務必 **按下 enter 鍵**)  
    ![SearchforServiceFabricClusterTemplate][searchforservicefabricclustertemplate]

3. 從清單中選取 [Service Fabric 叢集]
4. 瀏覽至 [Service Fabric 叢集] 刀鋒視窗，然後按一下 [建立]**** 並提供叢集的詳細資料
5. 建立 **新資源群組 (RG)** -讓它與叢集名稱相同的最好稍後尋找它們。 當您嘗試變更您的部署和/或刪除您的叢集，它是特別有用。

    注意：雖然您可以決定使用現有的資源群組，但最好是建立新的資源群組。 這可讓刪除您不需要的叢集變得非常簡單

     ![CreateRG][createrg]

6. 請務必選取您要部署叢集的 [訂用帳戶]**** (尤其在您有多個訂用帳戶時)。

7. 從下拉式清單中選取**位置** (如果您想要建立於其他位置，否則會預設為 [美國西部])

8. 設定您的 [節點類型]****。 節點類型可以視為雲端服務中的「角色」對等項目。 其可定義 VM 大小、VM 數目及其屬性。 您的叢集可以有一個以上的節點類型。 唯一的條件約束是您需至少有一個節點類型 (您在入口網站中定義的主要或第一個節點類型) 有至少 5 部 VM。
    1. 選取您需要的 VM 大小 /Pricing 層。 (預設值為 D4 標準，如果您只要用於此叢集測試您的應用程式，可以選取 D2 或任何較小的 VM 大小)
    2. 選擇 VM 數目。您稍後可以相應增加或減少節點類型中的 VM 數目，但主要或第一個節點類型必須至少有 5 部 VM
    3. 選擇您的節點類型 (1 到 12 個字元的長度包含唯一的字母和數字) 的名稱
    4. 選擇 VM 遠端桌面的使用者名稱和密碼
    5. **您有多個節點類型時的節點類型考量**。 如果您打算部署單一節點類型叢集，則跳到下一個步驟。

        - 讓我們舉例說明這個概念。 如果您想要部署應用程式，其中包含 「 前端 」 服務和 「 後端 」 服務，而且您想要放置開啟到網際網路的 「 前端 」 上的服務連接埠的小型 Vm (A2 D2 等類似的 VM 大小) 和 「 後端 」 服務所計算 (使用類似 D4，D6 VM 大小較大的 Vm 上的密集D12 等) 不是網際網路面向。
        - 雖然您可以將這兩個服務放在一個節點類型上，但建議您將它們放在有兩個節點類型的叢集中中，而每個節點類型可以有不同的屬性，如網際網路連線功能和 VM 大小以及可獨立調整的 VM 數目。
        - 先定義最終會有至少 5 部 VM 的節點類型。 其他節點類型可以有 1 部 VM 的下限。

    ![CreateNodeType][createnodetype]

9. **應用程式連接埠** - 如果您打算將應用程式立即部署到叢集，則新增您要對此節點類型 (或您已建立的 nodeType) 上的應用程式開放的連接埠。 您可以新增連接埠的節點類型稍後修改這個節點型別 (您需要新增探查，然後將探查新增負載 blanancer 規則) 與負載平衡器關聯。 立即這麼做，因為入口網站自動化功能會將所需的探查和規則新增至 LB，所以比較容易一些。
    1. 您可以在服務資訊清單中尋找屬於應用程式封裝的應用程式連接埠。 請移至每個應用程式、 開啟服務資訊清單，並記下您的應用程式需要與外界溝通的 「 輸入 」 端點。
    2. 加入所有的連接埠，逗號分隔的 「 應用程式的輸入的端點 」 欄位中。 TCP 用戶端連線結束點-根據預設，因此您不需要指定這些 19000。 比方說，我的應用程式需要開放連接埠 "83"。 您將在 servicemanifest.xml 中找到此 (可能有一個以上的 servicemanifest.xml) 的應用程式封裝中。

  大部分的範例應用程式使用連接埠 80 和 8081，所以如果您打算將範例部署到此叢集，則予以新增。

  ![連接埠][ports]

10. **選擇性: 放置屬性**-您不需要新增任何的設定，「 NodeTypeName 」 的預設位置屬性加入系統。 如果您的應用程式需要更多屬性，則可選擇加入更多屬性。


## 安全性組態

此時，Service Fabric 只支援透過 x509 憑證保護叢集。 開始此程序前，您必須將憑證上傳至 KeyVault。 請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 如需有關如何。

您可選擇是否保護您的叢集，但強烈建議您這麼做。 如果您選擇不要保護叢集，您需要將 [安全性模式] 切換為 [無]。

有關安全性考量，以及如何記載於 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)

![SecurityConfigs][securityconfigs]



## 選擇性：設定診斷功能

預設會在您的叢集上啟用診斷功能，以協助排解疑難問題。 如果您想要停用診斷功能：

1. 瀏覽至 [診斷組態] 刀鋒視窗。

2. 將 [狀態] 切換為 [關閉]。

## 選擇性：Fabric 設定

這是一個進階的選項，可讓您變更 service fabric 叢集的預設設定。 建議您 **變更** 預設值除非您確定您的應用程式和/或叢集必須有它。

## 完成叢集建立

按一下 [摘要]****，可以查看您所提供的組態或下載將用來部署叢集的 ARM 範本。 在您提供必要的設定之後，將會啟用 [建立] 按鈕，您即可啟動叢集建立程序。


您可以在 [通知] 中看到進度 (按一下螢幕右側狀態列附近的「鈴鐺」圖示)。 如果您已在建立叢集時按一下 [釘選到「開始面板」]，您會看到 [部署 Service Fabric 叢集] 釘選到開始面板

![通知][notifications]

## 檢視叢集狀態

部署完成後，您可以在入口網站中檢查您的叢集。

1. 移至 **瀏覽** ，然後按一下資源- **Service Fabric 叢集**。

2. 找出您的叢集並按一下它。

  ![BrowseCluster][browsecluster]

3. 您現在可以在儀表板中查看叢集的詳細資料，包括叢集的公用 IP 位址。 請注意，將滑鼠移至 [叢集公用 IP 位址]**** 上將會顯示您可按一下加以複製的剪貼簿。

  ![ClusterDashboard][clusterdashboard]

  叢集儀表板] 分頁中，節點監視組件表示是 [狀況良好的 vs 非狀況良好的 Vm 的數目。 您可以一節中找到的健全狀況狀態的詳細 [服務網狀架構健全狀況模型簡介](service-fabric-health-introduction.md) 文件


## 連接到叢集和部署應用程式

設定好叢集，您現在可以連接並開始部署應用程式。

1. 在已安裝 Service Fabric SDK 的電腦上，啟動 Windows PowerShell。

2. 視您建立的是安全或不安全的叢集，執行下列其中一組 PS 命令。

- 選項 1 **連接到不安全的叢集**。

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

執行下列命令來部署應用程式，以您電腦上的適當路徑取代所示的路徑。

 - 選項 2 **連接到安全的叢集**

執行下列命令，在您即將用來執行 "Connect-serviceFabricCluster" PS 命令的電腦上設定憑證

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

執行下列 PS 可立即連接到安全的叢集。 憑證詳細資料與您在入口網站上提供的資料一樣

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
例如，上述 PS 命令應該會類似於下列內容。

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


執行下列命令來部署應用程式，以您電腦上的適當路徑取代所示的路徑。 下列範例正在部署 WordCount 範例應用程式。

將封裝複製到您在上一個步驟中連接的叢集。


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
```
透過 Service Fabric 註冊您的應用程式類型。

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
```

在您剛才註冊的應用程式類型上建立新的執行個體。

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
```

現在開啟您所選的瀏覽器並連接到應用程式正在接聽的端點。 對我的範例應用程式 count 而言，URL 如下所示。

http://sfcluster4doc.westus.cloudapp.azure.com:31000




## 後續步驟

- [管理 Service Fabric 應用程式在 Visual Studio](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
- [服務網狀架構健全狀況模型簡介](service-fabric-health-introduction.md)



[searchforservicefabricclustertemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png 
[createrg]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png 
[createnodetype]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png 
[ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png 
[sfconfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png 
[securityconfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png 
[notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png 
[browsecluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png 
[clusterdashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png 
[secureconnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png 

