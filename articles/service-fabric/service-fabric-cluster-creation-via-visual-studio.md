<properties
   pageTitle="使用 Visual Studio 設定 Service Fabric 叢集 | Microsoft Azure"
   description="說明如何使用 Azure 資源群組專案在 Visual Studio 中建立的 Azure 資源管理員 (ARM) 範本設定 Service Fabric 叢集"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="karolz@microsoft.com"/>

# 使用 Visual Studio 設定 Service Fabric 叢集
本文說明如何設定 [**Service Fabric**](http://azure.microsoft.com/documentation/services/service-fabric/) 叢集使用 **Visual Studio** 和 [**Azure 資源管理員 (ARM)**](https://azure.microsoft.com/documentation/articles/resource-group-overview/) 範本。 我們將使用 Visual Studio **Azure 資源群組專案** 來建立範本。 在建立範本，它可以直接部署至 Azure 從 Visual Studio 中，但它也可從指令碼，或做為一部分後 **連續整合 (CI)** 設備。 

## 使用 Azure 資源群組專案建立 Service Fabric 叢集範本
若要開始進行，請開啟 Visual Studio 並建立 Azure 資源群組專案 (可在 "Cloud" 資料夾下取得)：

![新增專案] 對話方塊選取的 Azure 資源群組專案][] 1

您可以為此專案建立新的 Visual Studio 方案，或將它加入至現有的方案。

>[AZURE.NOTE] 如果看不到雲端] 節點下的 Azure 資源群組專案，您並沒有安裝 Azure SDK。 啟動 Web Platform Installer ([從這裡安裝](http://www.microsoft.com/web/downloads/platform.aspx) 如果您尚未這樣做)，然後搜尋 「 Azure.NET SDK 」，並安裝 Visual Studio 版本相容的版本。

按 [確定] 按鈕之後，Visual Studio 會要求您選取要建立的 ARM 範本：

![選取 Azure 範本] 對話方塊選取 Service Fabric 叢集範本][] 2

選取 [Service Fabric 叢集] 範本，然後再按一次 [確定] 按鈕。 隨即會建立專案和 ARM 範本。

## 準備範本以供部署
在部署範本以建立叢集之前，您必須提供必要的範本參數值。 這些參數值讀取自 `ServiceFabricCluster.param.dev.json` 檔案，該檔案位於資源群組專案的 `Templates` 資料夾下。 開啟該檔案並提供下列值：

|參數名稱           |說明|
|-----------------------  |--------------------------|
|clusterLocation          |名稱 **Azure 區域** 即將放置 Service Fabric 叢集。 例如「美國東部」。|
|clusterName              |範本將會建立之 Service Fabric 叢集的 (DNS) 名稱。 <br /><br /> 例如，如果您將此參數設定為 'myBigCluster'，並將 `clusterLocation` 參數設定為 [美國東部]，則叢集的名稱會是 `myBigCluster.eastus.cloudapp.azure.com`。|
|certificateThumbprint    |將保護叢集安全的憑證指紋。|
|sourceVaultValue         | *資源識別碼* 儲存憑證來保護叢集的金鑰保存庫。|
|certificateUrlValue      |叢集安全性憑證的 URL。|

Visual Studio Service Fabric ARM 範本會建立一個安全的叢集 (受憑證保護的叢集)。 此憑證由最後三個樣板參數 (`certificateThumbprint`, ，`sourceVaultValue` 和 `certificateUrlValue`) 且必須存在於 **Azure 金鑰保存庫**。 如需如何建立叢集的安全性憑證的詳細資訊請參閱 [如何安全使用憑證的 Service Fabric 叢集](service-fabric-cluster-security.md) 文件。

## 選擇性：新增公用應用程式連接埠
您可能想要變更部署前先加以範本的另一個層面是 **公用應用程式連接埠** 叢集。 根據預設，範本只會開啟兩個公用 TCP 連接埠 (80 和 8081)；如果您的應用程式需要更多連接埠，您必須修改範本中的 Azure 負載平衡器定義。 此定義儲存在主要範本檔案 (`SecureFabricCluster.json`) 中。 開啟該檔案並搜尋 "loadBalancedAppPort"。 您會注意到每個連接埠會與三個成品相關聯：

1. 樣板參數，定義連接埠的 TCP 連接埠值:
    ```json
    "loadBalancedAppPort1": {
        "type": "int",
        "defaultValue": 80
    }
    ```

2. A *探查* 定義頻率和時間的 Azure 負載平衡器會嘗試使用特定的 Service Fabric 節點，再進行容錯移轉至另一個。 探查是負載平衡器資源的一部分。 以下是第一個預設應用程式連接埠的探查定義:
    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
    ```

3. A *負載平衡規則* ，繫結在一起的連接埠和探查和啟用負載平衡集合中的 Service Fabric 叢集節點:
    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[parameters('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[parameters('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "tcp"
        }
    }
    ```
如果您打算部署至叢集的應用程式需要更多連接埠，您必須將它們加入藉由建立額外的探查和負載平衡規則定義。 如需有關如何使用 Azure 負載平衡器，透過 ARM 範本請 [開始設定內部負載平衡器搭配使用 Azure 資源管理員](https://azure.microsoft.com/documentation/articles/load-balancer-internal-arm-powershell/) 文件。

## 使用 Visual Studio 部署範本
在 `ServiceFabricCluster.param.dev.json` 中儲存所有必要的參數值後，您就可以部署範本和建立 Service Fabric 叢集。 在 Visual Studio 方案總管中以滑鼠右鍵按一下 [資源群組] 專案，然後選擇 [部署...]。 Visual Studio 會顯示 [部署到資源群組] 對話方塊，要求您視需要向 Azure 進行驗證：

![部署至資源群組] 對話方塊][] 3

此對話方塊可讓您選擇叢集的現有 Azure RM 資源群組，並提供給您建立新資源群組的選項。 通常對 Service Fabric 叢集使用個別的資源群組是很合理的。

一旦按 [部署] 按鈕，Visual Studio 就會提示您確認範本參數值。 按 [儲存] 按鈕。 有一個參數沒有持續值，這就是叢集的系統管理帳戶密碼；請在 Visual Studio 要求密碼值時提供一個密碼值。

>[AZURE.NOTE] 如果永遠不會使用 PowerShell 管理 Azure 從您目前使用的電腦，則必須執行一些維護:
>1. 啟用 PowerShell 指令碼執行 [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) 命令。 開發電腦通常可接受「不受限制」原則。
>2. 決定是否要從 Azure PowerShell 命令的診斷資料集合，以及執行 [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) 或 [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) 視。 如果您使用 Azure PowerShell 版本 0.9.8 或更舊版本，這些命令分別會命名為 `Enable-AzureDataCollection` 和 `Discable-AzureDataCollection`。 這可在範本部署期間避免不必要的提示。

您可以在 Visual Studio 的 [輸出] 視窗中監視部署程序的進度。 一旦完成範本部署，即可使用您的新叢集！ 

如果有任何錯誤，請移至 [Azure 入口網站](https://portal.azure.com/) 並檢查通知。 資源群組部署失敗會在通知中留下詳細的診斷資訊。 

## 後續步驟
- [了解如何使用 Azure 入口網站設定 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)
- [了解如何使用 Visual Studio 管理和部署 Service Fabric 應用程式](service-fabric-manage-applications-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

