<properties 
    pageTitle="如何調整 Azure Redis 快取" 
    description="了解如何調整 Azure Redis 快取執行個體" 
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
    ms.date="12/11/2015" 
    ms.author="sdanie"/>

# 如何調整 Azure Redis 快取

>[AZURE.NOTE] Azure Redis 快取調整功能目前為預覽狀態。 在預覽期間，您無法向上調整為高階層次快取，或無法由此向下調整，但是您可以變更高階快取內的定價層。

Azure Redis 快取都有不同的快取提供項目，以提供選擇快取大小和功能的彈性。 如果建立快取之後，就會變更您的應用程式的需求，您可以調整大小的快取使用 **變更定價層** 刀鋒視窗中的 [Azure 入口網站](https://portal.azure.com)。

## 調整時機

您可以使用 [監視](cache-how-to-monitor.md) Azure Redis 快取的監視健全狀況和快取應用程式的效能，並協助判斷是否需要調整快取功能。 

您可以監視下列度量，以協助判斷是否需要調整。

-   Redis 伺服器負載
-   記憶體使用量
-   網路頻寬
-   CPU 使用率

如果您判斷您的快取不再符合您應用程式的需求，則可以變更為您應用程式適用的較大或較小快取定價層。 如需有關如何判斷定價層来使用之快取的詳細資訊，請參閱 [哪些 Redis 快取提供項目和大小應該使用](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)。

## 調整快取
若要調整您的快取 [瀏覽至快取](cache-configure.md#configure-redis-cache-settings) 中 [Azure 入口網站](https://portal.azure.com) 按一下 **設定**, ，**定價層**。

您也可以按一下 **標準層** 或 **基本層** 部分 **Redis 快取** 刀鋒視窗。

![定價層][redis-cache-pricing-tier-part]

選取想要的定價層從 **定價層** 分頁，然後按一下 **選取**。

![定價層][redis-cache-pricing-tier-blade]

>[AZURE.NOTE] 您可以調整到不同的定價層，但有下列限制。
>
>-  您無法擴充或從 **高階** 快取。
>-  您無法擴充 **標準** 會快取 **基本** 快取。
>-  您可以擴充 **基本** 會快取 **標準** 快取，但是您無法同時變更大小。 如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。
>-  您無法從較大的向下調整 **C0 (250 MB)** 大小。

快取調整為新的定價層時 **調整** 狀態會顯示在 **Redis 快取** 刀鋒視窗。

![調整大小][redis-cache-scaling]

調整完成時，狀態會變更從 **調整** 到 **執行**。

## 如何自動化調整作業

除了調整 Azure Redis 快取執行個體在 Azure 入口網站中您可以調整使用 [Microsoft Azure 管理程式庫 (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)。 若要調整您的快取，請呼叫 `IRedisOperations.CreateOrUpdate` 方法，並傳入 `RedisProperties.SKU.Capacity` 的新大小。

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

如需詳細資訊，請參閱 [管理 Redis 快取使用 MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) 範例。

## 調整常見問題集

下列清單包含 Azure Redis 快取調整常見問題的解答。

## 可以向上調整為 Premium 快取，或在其中調整、向下調整嗎？

-   您無法擴充至 **高階** 快取定價層從 **基本** 或 **標準** 定價層。
-   您無法擴充 **高階** 會快取 **基本** 或 **標準** 定價層。
-   您可以擴充一 **高階** 快取定價層到另一個。
-   如果您已啟用叢集在建立時您 **高階** 快取中，您可以調整分區計數，向上或向下。

如需詳細資訊，請參閱 [如何設定高階 Azure Redis 快取叢集](cache-how-to-premium-clustering.md)。

## 調整之後，是否必須變更我的快取名稱或存取金鑰？

否，在調整作業期間，您的快取名稱和金鑰不會變更。

## 調整運作方式

當 **基本** 快取調整不同的大小，在關閉新的快取已佈建您使用新的大小。 在此期間，快取無法使用，而且快取中的所有資料都會遺失。

當 **基本** 快取調整 **標準** 快取中，複本快取會佈建，資料會從主要快取複製到複本快取。 調整程序期間仍可使用快取。

當 **標準** 快取調整不同的大小，其中一個複本已關閉並重新佈建到新的大小和傳輸資料，和另一個複本然後執行容錯移轉，再進行重新佈建，其中一個快取節點失敗期間發生的程序類似。

## 我是否會在調整期間遺失快取中的資料

當 **基本** 快取在調整到新的大小所有資料都都會遺失，並在快取在調整作業期間都已無法使用。

當 **基本** 快取調整 **標準** 通常會保留快取，快取中的資料。

當 **標準** 快取調整為較大大小時，通常會保留所有資料。 當調整 **標準** 快取縮小為較小的小時，資料可能會根據調整時，與新大小相關的快取中多少資料遺失。 如果遺失資料向下調整時，則會收回金鑰使用 [allkeys lru](http://redis.io/topics/lru-cache) 收回原則。 

請注意，標準和進階快取有 99.9% SLA 的可用性時，則資料遺失沒有 SLA。

## 是否可以在調整期間使用我的快取

**標準** 快取在調整作業期間保持可用。

**基本** 快取調整到不同的大小，作業期間會離線，但仍可從調整 **基本** 到 **標準**。

## 不支援的作業

您無法擴充或從 **高階** 快取。

您無法從變更 **標準** 至 **基本** 快取。

您可以擴充 **基本** 會快取 **標準** 快取，但是您無法同時變更大小。 如果您需要不同的大小，您可以進行後續調整作業，調整到您需要的大小。

您可以從向上延展 **C0** (250 MB) 快取較大的大小，但您無法調整規模較大的大小縮小為 **C0** 快取。

如果調整作業失敗，服務會嘗試還原作業，而且快取會還原成原始大小。

## 調整需要多長的時間

根據快取中的資料量，調整大約需要 20 分鐘。

## 如何分辨調整何時完成

在 Azure 入口網站中，您可以看到調整作業進行中。 調整完成時，快取的狀態變更為 **執行**。

## 為什麼這項功能只能預覽

我們發行這項功能來收到意見。 根據意見，我們即將正式發行這項功能。





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png





