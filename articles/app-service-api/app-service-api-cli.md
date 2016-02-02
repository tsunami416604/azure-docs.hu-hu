<properties
    pageTitle="Azure CLI 與 API Apps | Microsoft Azure"
    description="如何使用適用於 Mac、Linux 和 Windows 的 Microsoft Azure 命令列介面 (CLI) 搭配 Azure API Apps。"
    editor="jimbe"
    manager="wpickett"
    documentationCenter=""
    authors="tdykstra"
    services="app-service\api"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="tdykstra"/>


# Azure 命令列介面 (CLI) 與 API 應用程式

本文說明如何使用適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (CLI) 在 Azure App Service 中建立、管理及刪除 API 應用程式。

## 必要條件

本文假設您已安裝 Azure CLI 並且知道如何執行基本工作。 CLI 的簡介，請參閱 [安裝和設定 Azure CLI](../xplat-cli-install.md)。
> [AZURE.NOTE] 指示 [連接到 Azure 訂用帳戶 ](../xplat-cli-connect.md) 提供兩個替代方案: 使用工作或學校的帳戶，登入，並下載 *.publishsettings* 檔案。 如果是 API 應用程式，*.publishsettings* 檔案驗證方法將無法運作。 這是因為您必須使用「資源管理」模式 (在下一節介紹) 才能處理 API 應用程式，*.publishsettings* 檔案驗證方法無法與資源管理員搭配運作。 

## 啟用資源管理模式

可用於 CLI [服務管理 (asm)](../virtual-machines/virtual-machines-command-line-tools.md) 模式或 [資源管理 (arm)](../xplat-cli-azure-resource-manager.md) 模式。 如果是 API 應用程式，您必須使用「資源管理」模式。 因為 `arm` 預設不啟用模式，請使用 `組態模式 arm` 命令來啟用它。

    azure config mode arm

## 列出可用來處理 API 應用程式的命令

若要查看目前可供使用的 API 應用程式的所有命令，執行 `apiapp` 命令。

    azure apiapp

## 列出訂用帳戶或資源群組中所有的 API 應用程式

若要列出您已在訂閱中的所有 API 應用程式，請執行 `apiapp 都清單` 命令搭配任何參數。

    azure apiapp list

此清單會顯示每個 API 應用程式的資源群組、API 應用程式名稱、封裝識別碼和 URL。

    info:    Executing command apiapp list
    info:    Listing ApiApps
    data:    Resource Group  Name           Package Id        Url                                                                       
    data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
    data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
    info:    apiapp list command OK

若要限制為指定的資源群組清單，將群組加入 (`-g`) 參數。

    azure apiapp list -g <resource group name>

例如：

    azure apiapp list -g mygroup

若要將 API 應用程式版本和存取層級資訊加入至清單中，加入 [詳細資料 (`-d`) 參數。

    azure apiapp list -d

`清單` 與其他欄位的輸出看起來會如下列範例:

    info:    Executing command apiapp list
    info:    Listing ApiApps
    data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
    data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
    data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
    info:    apiapp list command OK

### 列出 API 應用程式的詳細資料

一個 API 應用程式，使用的清單詳細資料 `apiapp 顯示` 命令與群組 (`-g`) 和 API 應用程式名稱 (`-n`) 參數。

    azure apiapp show -g <resource group name> -n <API app name>

例如：

    azure apiapp show -g mygroup -n SimpleDropbox

輸出如下所示：

    info:    Executing command apiapp show
    info:    Getting ApiApp
    data:    Name:              SimpleDropbox
    data:    Location:          West US
    data:    Resource Group:    mygroup
    data:    Package Id:        SimpleDropbox
    data:    Package Version:   1.0.0
    data:    Update Policy:     Disabled
    data:    Access Level:      PublicAuthenticated
    data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
    data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
    info:    apiapp show command OK

## 建立 API 應用程式

有兩種方式可以建立 API 應用程式。 您可以使用命令式 CLI 命令個別建立 Azure 資源，或者可以在範本中使用宣告式語法一起定義所有必要的資源，然後使用 CLI 命令部署該範本。 宣告式方法，請參閱 [後續步驟](#next-steps)。

若要使用命令式方式建立 API 應用程式，請執行下列步驟：

1. 選擇有效的位置
1. 建立或尋找要使用的資源群組
2. 建立或尋找要使用的 App Service 方案
4. 建立 API 應用程式

### 選擇有效的位置

建立資源群組時，必須指定位置。 以下是一些適用於 API 應用程式的位置。

* 美國東部
* 美國西部
* 美國中南部
* 北歐
* 東亞
* 日本東部
* 西歐
* 東南亞
* 日本西部
* 美國中北部
* 美國中部
* 巴西南部
* 美國東部 2

若要取得完整且最新的位置清單，請使用 `位置清單` 命令，然後查看 `Microsoft.appservice` 資源提供者列。

    azure location list

以下是範例輸出 `位置清單` 命令。

    info:    Executing command location list
    info:    Getting Resource Providers
    data:    Name                                                                Location                                                                                                                                                   
    data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
    data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
    data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
    data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
    info:    location list command OK

### 建立或尋找要使用的資源群組

若要建立資源群組使用 `群組建立` 命令名稱 (`n`) 和位置 (`l`) 參數。

    azure group create -n <name> -l <location>

例如：

    azure group create -n "mygroup" -l "West US"

若要尋找現有的資源群組，請使用 `群組清單` 命令，並選擇資源群組中適用於 API 應用程式的位置。

    azure group list

### 建立或尋找要使用的 App Service 方案

若要建立應用程式服務方案，請使用 `資源建立` 命令，並使用 [資源類型參數 (`-r`) 來指定您想要建立的資源的類型是應用程式服務方案。

    azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{\"sku\": {\"tier\": \"<pricing tier>\"}, \"numberOfWorkers\" : <number of workers>, \"workerSize\": \"<worker size>\"}"

例如：

    azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{\"sku\": {\"tier\": \"Standard\"}, \"numberOfWorkers\" : 1, \"workerSize\": \"Small\"}"

JSON 字串 `屬性` (`-p`) 是由於最近有一些變更 REST API 中，在未來的必要參數 `-p` 將變成選用的參數。

範例命令指定了撰寫本文當日最新的 API 版本。 若要檢查更新的版本是否可用，請使用 `提供者顯示` 命令，請參閱 `apiVersions` 陣列 `網站` 物件存放至 `resourceTypes` 陣列。

    azure provider show -n Microsoft.Web --json

以下是範例 `網站` 命令輸出中的物件。

    {
      "resourceTypes": [
        {
          "apiVersions": [
            "2015-06-01",
            "2015-05-01",
            "2015-04-01",
            "2014-04-01"
          ],
          "locations": [
            "East Asia",
            "East US",
            "Japan East",
            "Japan West",
            "North Europe",
            "West Europe",
            "West US",
            "Southeast Asia",
            "Central US",
            "East US 2"
          ],
          "properties": {},
          "name": "sites"
        }

若要列出現有的應用程式服務方案，請使用 `資源清單` 命令並指定做為資源類型的應用程式服務方案使用 `-r` 參數。

    azure resource list -r Microsoft.Web/serverfarms

輸出如下所示：

    info:    Executing command resource list
    info:    Listing resources
    data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
    data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
    data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
    info:    resource list command OK

### 建立空的 (自訂) API 應用程式

若要建立空的 API 應用程式 (一個將自行撰寫程式碼)，請使用 `apiapp 建立` 命令並指定 `Microsoft.ApiApp` Nuget 封裝 (`-u` 參數)。

    azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

例如：

    azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

輸出會在 API 應用程式建立時報告進度。

    info:    Executing command apiapp create
    info:    Checking resource group and app service plan
    info:    Getting package metadata
    info:    Creating deployment
    info:    Waiting for deployment completion
    info:    Deployment started:
    data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
    data:    Resource Group:  mygroup
    data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
                              d515dd2eb903
    data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
    data:    Timestamp:       2015-07-21T23:20:12.8858274Z
    data:    
    data:    Operation           State         Status        Resource                                          
    data:    ------------------- ------------- ------------- --------------------------------------------------
    data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
    data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
    data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
    data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
    data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
    info:    Deployment complete with status: Succeeded
    info:    apiapp create command OK

#### 從 Marketplace 建立 API 應用程式

若要在 Marketplace 中取得可用的 API 應用程式封裝的清單，請使用 `群組範本清單` 命令，並使用類別來指定 API 應用程式 (`-c`) 參數。

    azure group template list -c apiapps

輸出看起來會像這個範例：

    info:    Executing command group template list
    info:    Listing gallery resource group templates
    data:    Publisher      Name                                                  
    data:    -------------  ------------------------------------------------------
    data:    Microsoft      Microsoft.Template.1.0.1                              
    data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
    data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
    info:    group template list command OK

若要從 Marketplace 使用建立 API 應用程式 `apiapap 建立` 命令並指定您想要使用 NuGet 封裝所建立的 API 應用程式名稱 (`-u`) 參數。

    azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

要用於值 `-u` 參數是位於 marketplace 名稱的中間區段。 例如，如果是 microsoft_com.MicrosoftSqlConnector.0.2.2，命令看起來會像這樣：

    azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector

如果需要任何 API 應用程式範本參數，例如伺服器名稱和 SQL 連接器的連接字串，系統將會提示您輸入必要的資料。 您可以使用 `-參數` 或 `-參數檔案` 傳入參數值。 如需有關參數檔案的詳細資訊，請參閱 [佈建 API 應用程式以新的閘道](app-service-api-arm-new-gateway-provision.md)。

## 後續步驟

本篇文章已示範如何使用個別的 Azure CLI 來處理自訂的 API 應用程式或是您從 Marketplace 範本建立的 API 應用程式。 如需如何使用自訂範本來自動化 API 應用程式建立的資訊，請參閱這些資源：

* [佈建 API 應用程式與現有的閘道器](app-service-api-arm-existing-gateway-provision.md)
* [佈建 API 應用程式以新的閘道](app-service-api-arm-new-gateway-provision.md)

如需如何使用 Azure 命令列公用程式搭配 Azure 資源管理員的詳細資訊，請參閱下列資源：

* [使用的 Azure CLI for Mac、 Linux 和 Windows 搭配 Azure 資源管理](../xplat-cli-azure-resource-manager.md)。
* [使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)






