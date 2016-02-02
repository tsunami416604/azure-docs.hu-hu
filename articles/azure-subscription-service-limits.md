<properties
    pageTitle="Microsoft Azure 訂用帳戶及服務限制、配額與限制"
    description="提供通用的 Azure 訂用帳戶和服務限制、配額和條件約束的清單。這包括如何增加限制和最大值的詳細資訊。"
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor="monicar"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="jroth"/>


# Azure 訂閱和服務限制、配額與限制

## 概觀

本文件說明一些最常見的 Microsoft Azure 限制。 請注意，這目前未涵蓋所有 Azure 服務。 這些限制將隨著時間擴展並更新以涵蓋更多平台。
> [AZURE.NOTE] 您無法將限制提升到高於下表中所示的**上限**值。 如果沒有**上限**欄，指定的資源即沒有可調整的限制。

## 限制和 Azure 資源管理員

現在您可以結合多個 Azure 中的資源到單一的 Azure 資源群組。 使用資源群組時的限制是，在全域時會使用 Azure 資源管理員在地區層級管理。

在以下的限制中，已加入了新資料表，以反映在使用 Azure 資源管理員時的限制方面的任何差異。 例如，有**訂用帳戶限制**資料表和**訂用帳戶限制 - Azure 資源管理員**資料表。 當某個限制同時適用於這兩個案例時，只會顯示在第一個資料表中。 除非另有說明，限制在所有區域中全域適用。
> [AZURE.NOTE] 請務必強調 Azure 資源群組中資源的配額是基於您的訂閱可以存取的每一區域，而不是每一訂閱 (服務管理配額則是)。 讓我們以核心配額為例。 如果您需要要求增加配額以支援核心，您必須決定您想要在哪些區域中使用多少個核心，然後提出 Azure 資源群組核心配額的特定要求，以取得您想要的數量和區域。 因此，如果您需要在西歐使用 30 個核心以在該處執行應用程式，您應該在西歐特別要求 30 個核心。 但是您在任何其他區域中的核心配額將不會增加 -- 僅西歐會有 30 個核心配額。




## 特定服務的限制

- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 
- 


### 訂閱限制

#### 訂閱限制

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### 訂用帳戶限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。 使用 Azure 資源管理員時未變更的限制不會在以下列出。 請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### 資源群組限制

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### 虛擬機器限制

#### 虛擬機器限制

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### 虛擬機器限制 - Azure 資源管理員

使用 Azure 資源管理員和 Azure 資源群組時，適用下列限制。 使用 Azure 資源管理員時未變更的限制不會在以下列出。 請參閱先前的資料表來瞭解這些限制。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### 網路限制

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### 網路限制

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### 流量管理員限制

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS 限制

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### 儲存體限制



#### 儲存體服務限制

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### 虛擬機器磁碟限制

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]



**標準儲存體帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**進階儲存體帳戶**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### 儲存體資源提供者限制

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### 雲端服務限制

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### 應用程式服務限制

下列應用程式服務限制包含 Web 應用程式、行動應用程式、API 應用程式和邏輯應用程式的限制。

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### 排程器限制

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Batch 限制

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### BizTalk 服務限制

下表顯示 Azure Biztalk 服務的限制。

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB 限制

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Mobile Engagement 限制

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### 搜尋限制

定價層會決定容量及您搜尋服務的限制。

#### 標準層級

[AZURE.INCLUDE [azure-search-limits-standard](../includes/azure-search-limits-standard.md)]

#### 共用層級 (多租用戶服務的一部分，Azure 訂閱者可免費使用)

[AZURE.INCLUDE [azure-search-limits-free](../includes/azure-search-limits-free.md)]



### 媒體服務限制

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN 限制

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### 行動服務限制

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### 通知中樞服務限制

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### 服務匯流排限制

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT 中樞限制

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Data Factory 限制

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]


### 串流分析限制

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Active Directory 限制

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp 限制

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple 系統限制

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights 限制

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### 備份限制

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### 網站復原限制

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights 限制

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API 管理限制

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis 快取限制

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### 金鑰保存庫限制

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication

[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### SQL Database 限制



## 另請參閱









