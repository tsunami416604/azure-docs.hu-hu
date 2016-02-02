<properties
    pageTitle="設計 Azure 資源管理員範本的最佳做法"
    description="顯示 Azure 資源管理員範本的設計模式"
    services="azure-resource-manager"
    documentationCenter=""
    authors="mmercuri"
    manager="georgem"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/13/2015"
    ms.author="mmercuri"/>


# 設計 Azure 資源管理員範本的最佳做法

 
這些部署需要受到支援、依照經過實證的做法執行，並遵循所識別的原則。 
 


本主題是較大份白皮書的一部分。

範本會結合基礎 Azure 資源管理員的優點，以及 JavaScript 物件標記法 (JSON) 的適應性和可讀性。 使用 


- 以一致的方式部署拓撲及其工作負載。
- 使用資源群組，一併管理應用程式中的所有資源。
- 套用角色型存取控制 (RBAC)，以授與使用者、群組和服務適當的存取權限。
- 使用標記關聯來簡化工作，例如計費彙總套件。

 
 
 
 


本文將分享這些經過實證的最佳做法，以協助您設計世界級 Azure 資源管理員範本。

透過與客戶合作，我們已在企業、系統整合者和 CSV 之間識別出許多資源管理員範本取用體驗。 
下列各節提供適用於不同客戶類型的常見案例和模式的整體概觀。

## 企業和系統整合者

在大型組織中，我們常看到兩種 ARM 範本的取用者：內部軟體開發團隊和公司 IT。 


### 內部軟體開發團隊

如果您的團隊會開發軟體來支援企業，則範本讓您能夠輕鬆快速地部署可用於企業特定解決方案的技術。 
您也可以使用範本快速建立訓練環境，讓團隊成員能夠取得所需的技巧。

您可以依原樣使用範本，或者擴充或撰寫它們來符合您的需求。 


 
 


### 公司 IT

公司 IT 組織通常會使用範本來傳遞雲端容量和雲端裝載功能。

#### 雲端容量

 
 
 


例如，您可能需要提供開發、測試或實際執行環境，讓軟體開發團隊可以在其中部署他們的解決方案。 
 
您也可以針對這些環境，使用不同的環境存取權限來設定組織特定的角色。

#### 雲端裝載功能

 
 


雲端裝載功能會受到建置它們的雲端容量供應項目所建立的安全性和角色考量所影響，如上所述。
這些功能會依原樣提供，或提供來做為受管理的服務。 


## 雲端服務廠商

在討論許多 CSV 之後，我們找出了多個方法，讓您能夠用來針對客戶及相關聯需求部署服務。

### CSV 裝載的供應項目

 


- **針對每位客戶進行不同的部署。**針對每位客戶進行不同的部署，需要具備不同已知組態的固定拓撲。 
部署的標記可用來彙總每位客戶的帳單。 
系統可能會啟用 RBAC，以允許客戶存取其雲端環境的各個層面。
- **共用多租用戶環境中的縮放單位。**範本可以代表多租用戶環境的縮放單位。 
 
這些縮放單位會隨著需求增加或減少。

### CSV 供應項目會插入至客戶訂用帳戶

您可能想要將軟體部署到客戶所擁有的訂用帳戶。 您可以使用範本，部署不同的部署到客戶的 Azure 訂用帳戶。

這些部署會使用 RBAC，因此，您可以在客戶的帳戶內更新和管理部署。

### Azure Marketplace

 
 
 


## OSS 專案

在開放原始碼專案中，資源管理員範本讓社群能夠使用經過證實的做法，快速部署解決方案。 
使用者接著可在自己的 Azure 訂用帳戶中部署這些範本。

下列各節將識別您在設計解決方案之前需考慮的事項。

## 識別 VM 內部和外部的功能

設計範本時，最好考量虛擬機器 (VM) 內部與外部的相關功能需求：

- 外部表示您部署的 VM 和其他資源，例如，網路拓樸、標記、憑證/密碼的參考，以及角色型存取控制。 所有的這些資源都是您範本的一部分。
- 內部表示已安裝的軟體和整體的期望狀態組態。 其他機制 (例如 VM 延伸模組或指令碼) 可完整使用或部分使用。 範本或許能夠識別和執行這些機制，但這些機制並不存在於範本中。

您可以在「箱子內部」進行的常見活動範例包括如下：

- 安裝或移除伺服器角色和功能
- 在節點或叢集層級安裝和設定軟體
- 在 Web 伺服器上部署網站
- 部署資料庫結構描述
- 管理登錄或其他類型的組態設定
- 管理檔案和目錄
- 啟動、停止及管理處理程序和服務
- 管理本機群組和使用者帳戶
- 安裝和管理封裝 (.msi、.exe、yum 等等)
- 管理環境變數
- 執行原生指令碼 (Windows PowerShell、bash 等)

### 期望狀態組態 (DSC)

 
 
 


從安全性觀點來看，除了您的內部員工，期望狀態組態也很重要。 
如果成功，通常就會安裝檔案，否則會變更遭入侵的系統狀態。 


針對適用於 DSC 的最熱門機制有一些資源擴充功能可供使用 - PowerShell DSC、Chef 及 Puppet。 


## 通用範本範圍

在我們的經驗中，我們看到了範圍所浮現的三個主要解決方案範本。 


### 容量範圍

容量範圍可在標準拓樸中提供一組預先設定來遵循法規和原則的資源。 


### 功能範圍

功能範圍著重於部署和設定特定技術的拓撲。 


### 端對端解決方案範圍

端對端解決方案範圍的目標已超越單一功能，並改為將重點放在提供由多個功能所組成的端對端解決方案。

已設定解決方案範圍的範本範圍會將其本身顯示為一組具有一或多個已設定功能範圍的範本，其中含有解決方案特定的資源、邏輯和期望狀態。 


## 選擇自由格式與已知的組態

 
 


### 自由格式組態

表面上，自由格式組態聽起來很實用。 
 


 
 
例如，共用 16 個磁碟，即可提供 8,000 個 IOPS。 


 


通常，部署可能會有多種類型的節點 (例如主要和資料節點)，因此，經常會針對每個節點類型提供此彈性。

當您開始部署具有任何重要性的叢集時，就會開始使用這所有數目的倍數。 
 


 
 
 


在企業 IT 和 SI 案例中，有人必須維護範本，並提供已針對一或多個組織部署之拓撲的支援。 
這個額外負擔 (每位客戶都有不同的組態和範本) 並不盡理想。

 
 
 


此外，您無法透過 API 呼叫來建立訂用帳戶，但必須透過入口網站手動執行此動作。 
 


考慮這所有因素，真正的自由格式組態乍看之下比較不具吸引力。

### 已知組態 - T 恤尺寸調整法

 
 
在其他情況下，這可能是某種技術的工作負載特定組態， 例如，對應減少或沒有 SQL。

 


這種方法可針對預先為客戶設定好的各種大小提供良好且已知的組態。 
 
 
 


著重於 T 恤尺寸的已知組態方法在某個尺寸內可能也會擁有各種節點數目。 
 


 


 


您也可以使用以 JSON 為基礎的範本，利用獨特的變體來配合客戶。 


 


## 已設定容量和功能範圍的解決方案範本

分解可提供範本開發的模組化方法，以支援重複使用、擴充性、測試和工具設定。 


 
參數、靜態變數和產生的變數可用來提供進出連結範本的值。

![範本參數](./media/best-practices-resource-manager-design-templates/template-parameters.png)

**參數會傳遞到主要的範本，然後傳遞到連結的範本**

 
此圖中的每個範本和指令碼類型都會使用範例來說明。 


### 範本中繼資料

範本中繼資料 (metadata.json 檔案) 包含使用 JSON 說明範本的機碼/值組，讓人類和軟體系統都能讀取該範本。

![範本中繼資料](./media/best-practices-resource-manager-design-templates/template-metadata.png)

**範本中繼資料的說明位於 metadata.json 檔案中**

軟體代理程式可以擷取 metadata.json 檔案，並將資訊和連結發佈到網頁或目錄中的範本。 


以下顯示完整的範例檔案。

    {
        "itemDisplayName": "PostgreSQL 9.3 on Ubuntu VMs",
        "description": "This template creates a PostgreSQL streaming-replication between a master and one or more slave servers each with 2 striped data disks. The database servers are deployed into a private-only subnet with one publicly accessible jumpbox VM in a DMZ subnet with public IP.",
        "summary": "PostgreSQL stream-replication with multiple slave servers and a publicly accessible jumpbox VM",
        "githubUsername": "arsenvlad",
        "dateUpdated": "2015-04-24"
    }

### 主要的範本

主要的範本 (azuredeploy.json 檔案) 是由使用者呼叫，而且是透過它提供一組使用者定義參數的範本。

![主要的範本](./media/best-practices-resource-manager-design-templates/main-template.png)

**主要的範本會接收來自使用者的參數**

 


 
實際上，您可以多種方式來使用此參數。 如需詳細資訊，請參閱本文件稍後的＜已知組態資源範本＞。

無論使用者參數所指定的已知組態為何，都會部署某些資源。 


不論指定的已知組態為何，都會選擇性地部署某些資源。

### 共用的資源範本

此範本會提供所有已知組態上通用的資源。 


![範本資源](./media/best-practices-resource-manager-design-templates/template-resources.png)

**共用的資源範本**

資源名稱 (例如虛擬網路名稱) 會以主要的範本為根據。 


### 選擇性資源範本

選擇性資源範本包含以參數或變數的值為根據且利用程式設計方式部署的資源。

![選擇性資源](./media/best-practices-resource-manager-design-templates/optional-resources.png)

**選擇性資源範本**

 
網際網路。 
範本連結會使用產生的變數來安裝 Jumpbox。

您可以從多個位置連結選擇性資源範本：

-   若適用於每個部署，即可從共用的資源範本建立參數導向的連結。
-    


指定的資源是否為選擇性，可能不是由範本取用者所驅動，而是由範本提供者所驅動。 
 


### 已知組態資源範本

在主要的範本中，參數會公開，讓範本取用者能夠指定要部署的期望已知組態。 


![已知組態資源](./media/best-practices-resource-manager-design-templates/known-config.png)

**已知組態資源範本**

通常會使用 T 恤尺寸方法，但參數可代表任何已知組態的組合。 
 


如同共用的資源範本，變數會從下列任一處傳遞到已知組態範本：

-   使用者 - 也就是傳送到主要範本的參數。
-   組織 - 也就是主要範本中代表內部需求或原則的變數。

### 成員資源範本

在已知組態中，通常會包含一或多個成員節點類型。 例如，使用 Hadoop，您會有主要節點和資料節點。 
如果正在安裝 MongoDB，則會有資料節點和仲裁程式。 


![成員資源](./media/best-practices-resource-manager-design-templates/member-resources.png)

**成員資源範本**

 
 


針對 VM，通常會使用兩種類型的指令碼：廣泛可重複使用和自訂的指令碼。

### 廣泛可重複使用的指令碼

廣泛可重複使用的指令碼可以在多種類型的範本上使用。 
不論 VM 中安裝了哪種軟體，此指令碼都會針對常見案例重複使用經過證實的做法。

![可重複使用的指令碼](./media/best-practices-resource-manager-design-templates/reusable-scripts.png)

**成員資源範本能夠呼叫廣泛可重複使用的指令碼**

### 自訂指令碼

範本通常會呼叫一或多個指令碼，在 VM 內安裝和設定軟體。 
 


![自訂指令碼](./media/best-practices-resource-manager-design-templates/custom-scripts.png)

**成員資源範本可基於特定目的呼叫指定碼，例如 VM 組態**

## 已設定功能範圍的解決方案範本範例 - Redis

 


針對部署，將會有一組共用資源 (虛擬網路、儲存體帳戶、可用性設定組) 和一個選擇性資源 (Jumpbox)。 
 


### 建立範本檔案

您會建立名為 azuredeploy.json 的主要範本。

您會建立名為 shared-resources.json 的共用資源範本

您會建立選擇性資源範本來啟用 Jumpbox 的部署 (名為 jumpbox_enabled.json)

Redis 只會使用單一節點類型，因此您將建立名為 node-resources.json 的單一成員資源範本。

使用 Redis，您會想要安裝每個個別的節點，接著，並且在安裝所有節點之後，會想要設定叢集。 


### 連結範本

使用範本連結時，主要的範本會向外連結到共用的資源範本，以建立虛擬網路。

邏輯會新增到主要的範本內，讓範本的取用者能夠指定是否應該部署 Jumpbox。 
 


 


拓撲會類似下圖。

![Redis 範本](./media/best-practices-resource-manager-design-templates/redis-template.png)

**Redis 範本的範本結構**

### 設定狀態

 


### 支援不同大小的部署

在變數內部，T 恤尺寸的範本會針對特定大小 (*large*) 指定每個要部署類型的節點數目。 
 


## 分解和已設定端對端解決方案範圍的範本

具有端對端解決方案範圍的解決方案範本會將重點放在提供端對端解決方案。 


如下圖中反白顯示的，同樣適用於已設定功能範圍之範本的模型，會針對具有端對端解決方案範圍的範本加以擴充。

 


 


 


 


![端對端](./media/best-practices-resource-manager-design-templates/end-to-end.png)

**適用於已設定容量或功能範圍之解決方案範本的模型可針對端對端解決方案範本範圍輕易地進行擴充**

## 準備適用於 Marketplace 的範本

 


另一個所需的案例是透過 Marketplace 來部署範本。 這個分解方法也適用於 Marketplace，但會有些微變更。

如先前所述，範本可用來提供不同的部署類型，以便在 Marketplace 中進行銷售。 


如下所示，您可以輕鬆地利用現有的端對端解決方案或已設定功能範圍的範本，在 Marketplace 中列出不同的已知組態。

主要範本的參數會先加以修改，以移除名為 tshirtSize 的輸入參數。

 


 


![Marketplace](./media/best-practices-resource-manager-design-templates/marketplace.png)

**針對 Marketplace 調整已設定解決方案範圍的範本**

## 後續步驟

- 
- 
- 





