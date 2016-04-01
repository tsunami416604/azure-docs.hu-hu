<properties 
    pageTitle="Azure Redis Cache 高階層簡介" 
    description="了解如何建立和管理高階層 Azure Redis Cache 執行個體的 Redis 永續性、Redis 叢集和 VNET 支援" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/02/2015" 
    ms.author="sdanie"/>

# Azure Redis Cache 高階層簡介
Azure Redis Cache 是一種分散式受管理快取，可提供超快速的資料存取，藉此協助您建置具高度延展性且快速回應的應用程式。 

新的高階層是可供企業立即使用的層，其中包括所有標準層功能及其他優點，例如更佳的效能、更大的工作負載、嚴重損壞修復和增強的安全性。 請繼續閱讀，以深入了解高階快取層的其他功能。

## 效能優於標準或基本層。
**效能優於標準或基本層。** 高階層中的快取是部署在擁有較快處理器的硬體上，因此效能優於基本或標準層。 高階層快取的輸送量較高，延遲較低。 

**相較於標準層，高階層中相同大小的快取，其輸送量較高。** 例如 為 53 GB 快取中，第 4 頁的輸送量 (Premium) 是 250 K 每秒要求數相較於為 C6 150 K （標準）。

請參閱 [Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) 如需詳細資訊大小、 輸送量和進階版快取使用的頻寬。

## Redis 資料永續性
高階層可讓您將快取資料保存在 Azure 儲存體帳戶中。 在基本/標準快取中，所有資料都只儲存在記憶體中。 如果基礎結構發生問題，資料可能會遺失。 建議您使用高階層中的 Redis 資料永續性功能，以提高資料遺失時的復原能力。 Azure Redis 快取提供 RDB 和很少 （即將推出） 中的選項 [Redis 持續性](http://redis.io/topics/persistence)。 

如需設定持續性的指示，請參閱 [如何設定高階 Azure Redis 快取的持續性](cache-how-to-premium-persistence.md)。

##Redis 叢集
如果您想要建立大於 53 GB 的快取，或想跨多個 Redis 節點共用資料，可以使用高階層中的 Redis 叢集。 每個節點均包含一個 Azure 所管理的主要/複本快取組，可提供高可用性。 

**Redis 叢集可提供最大的擴充能力和輸送量。** 當您增加叢集中的分區 (節點) 數目時，輸送量會呈線性增加。 例如 如果您建立 10 個分區的 P4 叢集則可以使用輸送量 250k 個 * 10 = 2.5 百萬個每秒要求數。 請參閱 [Azure Redis 快取常見問題集](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) 如需詳細資訊大小、 輸送量和進階版快取使用的頻寬。

若要開始使用叢集，請參閱 [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。

##增強的安全性和隔離
您可透過公用網際網路存取基本或標準層中建立的快取。 對快取的存取權會受到存取金鑰的限制。 若使用高階層，您可以進一步確保只有指定網路中的用戶端可以存取快取。 您可以部署 Redis 快取的 [Azure 虛擬網路 (VNet)](https://azure.microsoft.com/services/virtual-network/)。 您可以使用 VNet 的所有功能，例如子網路、存取控制原則和其他功能，進一步限制對 Redis 的存取權。

如需詳細資訊，請參閱 [如何設定虛擬網路支援高階 Azure Redis 快取](cache-how-to-premium-vnet.md)。

## 後續步驟

建立快取並探索高階層的新功能。

-   [如何設定高階 Azure Redis Cache 的永續性](cache-how-to-premium-persistence.md)
-   [如何設定高階 Azure Redis 快取的虛擬網路支援](cache-how-to-premium-vnet.md)
-   [如何設定高階 Azure Redis Cache 的叢集](cache-how-to-premium-clustering.md)
  



