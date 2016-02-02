<properties
   pageTitle="資源管理員支援的服務和支援的區域 |Microsoft Azure"
   description="說明支援資源管理員的資源提供者以及可裝載資源的區域。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/04/2015"
   ms.author="tomfitz"/>


# 資源管理員對於服務、區域和 API 版本的支援

Azure 資源管理員提供一種新方式來部署和管理組成應用程式的服務。 
大部分但並非所有的服務都支援資源管理員，有些服務僅部分支援資源管理員。 
本主題提供支援 Azure 資源管理員的資源提供者清單。

部署資源時，您也需要知道哪些區域支援這些資源，以及哪些 API 版本適用於資源。

下表列出哪些服務可透過資源管理員支援部署和管理，哪些則否。 



## 計算

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------------------------ | -------------- | -------------- |-------- | ------ |
| 虛擬機器| 是| 是，許多選項| 否| | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Compute.json)|
| 批次| 是| | 是| | |
| Dynamics 週期服務| 是| 否| | | |
| 虛擬機器 (傳統)| 限制| 是，許多選項| 部分 (請參閱下文)| -| -|
| | 否| 否| -| -| -|
| | 否| 否| -| -| -|

虛擬機器 (傳統) 是指已透過傳統部署模型部署的資源，而不是透過資源管理員部署模型部署的資源。 


虛擬機器 (傳統) 資源可以移至新的資源群組，但不能移至新的訂用帳戶。

## 網路

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | -------- | -------------- | -------- | ------ |
| 應用程式閘道| 是| | | | |
| DNS| 是| | | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json)|
| 負載平衡器| 是| | | | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json)|
| 虛擬網路| 是| | 否| | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Network.json)|
| 流量管理員| 是| 否| | | |
| ExpressRoute| 是| 否| 否| | |

## 資料與儲存體

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | ------- | -------------- | -------- | ------ |
| DocumentDB| 是| | 是| | |
| 儲存體| 是| | 否| | |
| Redis 快取| 是| | 是| | |
| SQL Database| 是| | 是| | |
| 搜尋| 是| | 是| | |
| SQL 資料倉儲| 是| | | | |
| StorSimple| 否| 否| -| -| -|
| | 否| 否| -| -| -|

## Web 與行動

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | -------- | -------------- | -------- | ------ |
| API 管理| 是| 否| 是| | |
| API 應用程式| 是| | | | |
| Web Apps| 是| | 是，但有限制 (請參閱下文)| | [2015-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-08-01/Microsoft.Web.json)|
| 通知中樞| 是| | 是| | [2015-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2015-04-01/Microsoft.NotificationHubs.json)|
| Logic Apps| 是| | 是| | |
| Mobile Engagement| 是| 否| 是| | |

使用 Web 應用程式時，您無法只移動 App Service 方案。 若要移動 Web 應用程式，您的選項如下：

- 如果目的地資源群組還沒有 Microsoft.Web 資源，將所有的資源從某個資源群組移動到不同的資源群組。
- 將 Web 應用程式移動到不同的資源群組，但在原始的資源群組中保留 App Service 方案。

## Analytics

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 事件中心| 是| 否| | | |
| 串流分析| 是| | | | |
| HDInsights| 是| | 是| | |
| Data Factory| 是| | 是| | |
| 機器學習服務| 否| 否| -| -| -|
| | 否| 否| -| -| -|

## 媒體與 CDN

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | -------- | -------------- | -------- | ------ |
| CDN| 是| | | | |
| 媒體服務| 否| 否| | | |


## 混合式整合

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| BizTalk 服務| 是| 否| | | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.BizTalkServices.json)|
| 服務匯流排| 是| 否| | | |
| 備份| 否| 否| -| -| -|
| 站台復原| 否| 否| -| -| -|

## 身分識別與存取管理

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | -------------- | -------------- | -------- | ------ |
| Azure Active Directory| 否| 否| -| -| -|
| | 否| 否| -| -| -|
| Multi-Factor Authentication| 否| 否| -| -| -|

## 開發人員服務

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | ---------- | -------------- | -------- | ------ |
| Application Insights| 是| | 否| | [2014-04-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-04-01/Microsoft.Insights.json)|
| Bing 地圖| 是| | | | |
| Visual Studio 帳戶| 是| | | | [2014-02-26](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-02-26/microsoft.visualstudio.json)|

## 管理

| 服務| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | --------- | -------------- | -------- | ------ |
| 自動化| 是| | 是| | |
| 金鑰保存庫| 是| 否| 是| | |
| 排程器| 是| 否| | | [2014-08-01](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2014-08-01/Microsoft.Scheduler.json)|
| Operational Insights| 是| 否| 是| | |
| IoTHubs| 是| | | | |

## 資源管理員

| 功能| 已啟用資源管理員| 入口網站| 移動資源| REST API| 結構描述|
| ------- | ------- | -------- | -------------- | -------- | ------ |
| Authorization| 是| N/A| N/A| | |
| 資源| 是| N/A| N/A| | |


## 支援的區域

部署資源時，通常需要指定資源的區域。 所有區域都支援資源管理員，但您部署的資源可能無法在所有區域中受到支援。 
 


您可以藉由執行下列其中一個命令，來判斷您的訂用帳戶支援之特定資源類型的區域。

### REST API



### PowerShell

下列範例示範如何使用 Azure PowerShell 1.0 版來取得支援網站的區域。

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

輸出將類似於：

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia
    Central US
    East US 2

Azure PowerShell 0.9.8 請使用下列命令：

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

### Azure CLI

下列範例會針對每個資源類型傳回所有支援的位置。

    azure location list

您也可以使用 **jq** 之類的工具來篩選位置結果。

    azure location list --json | jq '.[] | select(.name == "Microsoft.Web/sites")'

它會傳回：

    {
      "name": "Microsoft.Web/sites",
      "location": "Brazil South,East Asia,East US,Japan East,Japan West,North Central US,
            North Europe,South Central US,West Europe,West US,Southeast Asia,Central US,East US 2"
    }

## 支援的 API 版本

當您部署範本時，您必須指定要用來建立每個資源的 API 版本。 API 版本會對應至資源提供者所發行的 REST API 作業版本。 
當資源提供者啟用新功能時，它會發行新版本的 REST API。 
一般而言，您會想要在建立新範本時選取最新的 API 版本。 對於現有的範本，您可以決定是否繼續使用舊的 API 版本，或更新您的範本以便最新版本利用新功能。

### REST API



### PowerShell

下列範例示範如何使用 Azure PowerShell 1.0 版來取得特定資源類型可用的 API 版本。

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

輸出將類似於：

    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Azure PowerShell 0.9.8 請使用：

    PS C:\> ((Get-AzureProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

您可以使用下列命令，將資源提供者的資訊 (包括可用的 API 版本) 儲存至檔案。

    azure provider show Microsoft.Web -vv --json > c:\temp.json

您可以開啟該檔案，然後尋找 **apiVersions** 元素

## 後續步驟

- 
- 





