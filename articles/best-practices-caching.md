<properties
   pageTitle="快取指引 | Microsoft Azure"
   description="用來改善效能和延展性的快取指引。"
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>


# 快取指引

![](media/best-practices-caching/pnp-logo.png)












## 分散式應用程式中的快取




- 使用私人快取，其中資料保留在執行應用程式或服務執行個體的本機電腦上。
- 使用共用快取，作為可由多個處理程序和/或電腦存取的常見來源。






### 私人快取






如果您















![在不同應用程式執行個體中使用記憶體中快取](media/best-practices-caching/Figure1.png)



### 共用快取


共用




![使用共用快取_](media/best-practices-caching/Figure2.png)






「










## 使用快取的考量




### 何時應該快取資料？

快取可大幅改善效能、延展性和可用性。















資訊。

### 資料類型和快取母體擴展策略

















































運作。










### 快取高動態資料








如果



### 管理快取中的資料到期















> [AZURE.NOTE] 仔細思考快取的到期時間和其包含的物件。 如果設定的時間太短，則物件會太快過期，而且會減少您使用快取的優點。 如果設定的時間太長，則您需負擔資料過時的風險。










某些快取實作可能會提供其他收回原則。 這





### 用戶端快取中的失效資料

















## 管理快取中的並行存取


重新接收。









- __開放式。__應用程式會檢查快取中的資料自擷取之後，以及正要更新之前是否已變更。 如果資料仍然相同，則可以進行變更。 否則，應用程式必須決定是否要進行更新 (驅動這項決策的商務邏輯屬於應用程式特定)。 這種方法適合不常更新，或其中不太可能發生衝突的情況。
- __封閉式。__應用程式擷取快取中的資料時會鎖定資料，以避免另一個執行個體變更資料。 此程序可確保不會發生衝突，但可能會封鎖其他需要處理相同資料的執行個體。 封閉式並行存取可能會影響解決方案的延展性，而且僅應用於短期的作業。 這種方法可能適用於很可能發生衝突的情況，特別是當應用程式更新快取中的多個項目，且必須確保這些變更會一致套用的情況。

### 實作高可用性和延展性，並改善效能


Auch die Eigenschaften









Auch die Eigenschaften
Auch die Eigenschaften

















圖 3


![使用本機、私人快取搭配共用快取_](media/best-practices-caching/Caching3.png)














































## 快取和最終一致性

















### 保護快取的資料



其中有兩個主要的考量：

- 快取中的資料隱私權。
- 













- 


- 
















## 使用 Microsoft Azure 實作快取的考量

Azure 提供了 Azure Redis 快取。




















> [AZURE.NOTE] Azure 也提供受管理的快取服務。 此
  此程式碼
  
  
  
  
  
  
  
>
> 此外，Azure 支援角色中快取。
  
  
  
  
  
  
  
  
  
  
  
  
  


### Redis 的功能




文件。


### Redis 作為記憶體中資料庫

Redis 同時支援讀取和寫入作業。 不同於許多快取 (這應視為暫時性資料存放區)，寫入可以透過在本機快照集檔案或僅限附加的記錄檔中定期儲存來防止系統失敗。 所有寫入皆為非同步，且不會封鎖用戶端讀取和寫入資料。 當 Redis 開始執行時，它會從快照集或記錄檔讀取資料，並使用它來建構記憶體中快取。
> [AZURE.NOTE] 
  
  
  
  
  如需詳細資訊，請參閱＜另行快取模式＞。

#### Redis 資料類型

Redis 是索引鍵值存放區，其中的值可以包含簡易類型或複雜資料結構，例如雜湊、清單，以及集合。 它針對這些資料類型支援一組不可部分完成的作業。 索引鍵可以永久存在或標記為有限的存留時間，屆時索引鍵及其對應的值會自動從快取中移除。

#### Redis 複寫和叢集

Redis 支援主要/下層複寫，以協助確保可用性並維護輸送量；Redis 主要節點的寫入作業會複寫至一個或多個下層節點，而讀取作業可由主要或任何的下層項目來提供。 至於網路磁碟分割，下層項目可以繼續提供資料，然後在重新建立連接時以透明的方式與主要節點重新同步。

Redis 也提供叢集，可讓您以透明的方式在伺服器之間將資料分割成分區並分散負載。 這項功能隨著可新增新的 Redis 伺服器可改善延展性，且增加資料可重新分割的快取大小。 此外，叢集中的每一部伺服器可以使用主要/下層複寫進行複寫，以確保整個叢集中每個節點的可用性。
> [AZURE.NOTE] Azure Redis 快取目前不支援叢集。 如果您想要建立 Redis 叢集，您可以建置自己的自訂 Redis 伺服器。 如需詳細資訊，請參閱本文件稍後的＜建置自訂的 Redis 快取＞一節。

### Redis 記憶體使用

Redis 快取具有有限的大小，取決於主機電腦上可用的資源。 當您設定的 Redis 伺服器時，您可以指定伺服器可以使用的記憶體最大數量。 Redis 快取中的索引鍵可以設定到期時間，屆時它會自動從快取中移除。 這項功能可協助避免記憶體中快取填滿老舊或過時的資料。

當記憶體填滿時，Redis 可以遵循原則數目自動收回索引鍵及其值。 預設值是 LRU (最近最少使用的)，但您也可以選取其他原則，例如，隨機收回金鑰，或完全關閉收回 (在此情況下，當快取已滿時嘗試將項目新增至快取將會失敗)。

### Redis 交易與批次

Redis 可讓用戶端應用程式提交一系列的作業，以便在快取中讀取和寫入資料以作為不可部分完成的交易。 交易中的所有命令保證會循序執行，且其他並行用戶端所發出的命令將不會在兩者之間交互編排。 不過，這些不是實際上的交易，因為關聯式資料庫會執行它們。 交易處理包含兩個階段；即命令佇列和命令執行。 在命令佇列階段期間，用戶端會提交構成交易的命令。 如果此時發生某種形式的錯誤 (例如語法錯誤，或參數數目不正確) 則 Redis 將會拒絕處理整個交易並捨棄它。 在執行階段期間，Redis 會循序執行每個佇列中的命令。 如果在這個階段期間命令失敗，Redis 將會繼續執行下一個佇列中的命令，且它不會復原任何已執行命令的效果。 這個簡化的交易形式可協助維護效能，並避免爭用所造成的效能問題。 Redis 會實作一種開放式鎖定，以便協助維護一致性。

Redis 也支援非交易式批次要求。 用戶端用來將命令傳送到 Redis 伺服器的 Redis 通訊協定，可讓用戶端將以相同要求的一部分來傳送一系列作業。 這有助於減少在網路上的封包分割。 處理批次時，系統會執行每個命令。 不同於交易，如果這些命令的任何格式不正確，則將會遭到拒絕，但會執行剩餘的命令。 另外也不保證批次中處理命令的順序。

### Redis 安全性

Redis 純粹著重於提供資料的快速存取，並設計為在受信任的環境中執行，且僅能由受信任的用戶端存取。 Redis 僅支援以密碼驗證為基礎的有限安全性模型 (可以完全移除驗證，然而不建議這樣做)。 所有已驗證的用戶端會共用相同的全域密碼，並可存取相同的資源。 如果您需要更完整的登入安全性，您必須在 Redis 伺服器之前實作自己的安全性層級，且所有用戶端要求應通過此額外的層級，Redis 不應直接向不受信任或未經驗證的用戶端公開。

您可以透過停用命令或重新命名命令 (僅提供有權限的用戶端使用新的名稱) 來限制命令的存取。

Redis 不直接支援任何形式的資料加密，因此所有編碼必須由用戶端應用程式執行。 此外，Redis 不提供任何形式的傳輸安全性，因此如果您基於資料在網路上流通而需要保護資料，您應實作 SSL Proxy。


> [AZURE.NOTE] 
  

### 使用 Azure Redis 快取

Azure Redis 快取提供 Redis 伺服器的存取權，該伺服器在 Azure 資料中心所主控的伺服器上執行；它可作為可提供存取控制與安全性的外觀。 您可以使用 Azure 管理入口網站來佈建快取。 入口網站會提供數個預先定義的組態，範圍從執行專用服務的 53GB 快取，用來支援 SSL 通訊 (適用於隱私權) 以及主要/下層複寫搭配 99.9% 可用性的 SLA，到共用硬體上所執行不含複寫 (無可用性保證) 的 250 MB 快取。

您也可以使用 Azure 管理入口網站來設定快取的收回原則，並透過將使用者新增至所提供角色；擁有者、參與者和讀取者來控制快取的存取權。 這些角色會定義成員可以執行的作業。 例如，擁有者角色成員擁有快取 (包含安全性) 及其內容的完整控制權，參與者角色成員則可以讀取和寫入快取中的資訊，而讀取者角色成員僅能從快取擷取資料。

多數管理工作可透過 Azure 管理入口網站來執行，且基於這個理由，許多 Redis 標準版中的可用系統管理命令皆無法使用，包含以程式設計方式修改組態、關閉 Redis 伺服器、設定其他從屬伺服器，或強制將資料儲存到磁碟等功能。

Azure 管理入口網站包含便利的圖形化顯示，可讓您監視快取的效能。 例如，您可以檢視建立的連接數目、執行的要求數目、讀取與寫入的資料量，以及快取命中與快取遺漏的數目。 您可以使用這項資訊來判斷快取的效率，且可視需要切換至不同的組態，或變更收回原則。 此外，如果一個或多個關鍵度量落在預期的範圍之外，您可以建立將電子郵件訊息傳送給系統管理員的警示。 例如，如果快取遺漏數目在最後一小時超過指定的值，則系統管理員可能會收到警告，表示快取可能太小或資料可能太快收回。

您也可以監視 CPU、記憶體和快取的網路使用量。
> [AZURE.NOTE] Azure Redis 快取純粹用作快取，而非資料庫。 如此一來，它目前不會實作 Redis 持續性。



## 快取工作階段狀態和 HTML 輸出

如果您建置透過使用 Azure Web 角色來執行的 ASP.NET Web 應用程式，您可以將工作階段狀態資訊和 HTML 輸出儲存在 Azure Redis 快取中。 Azure Redis 快取的工作階段狀態提供者可讓您在 ASP.NET Web 應用程式的不同執行個體之間共用工作階段資訊，並且在用戶端與伺服器親和性無法使用，且快取工作階段記憶體中資料並不適當的 Web 伺服陣列情況下相當實用。

使用工作階段狀態提供者搭配 Azure Redis 快取可提供幾項優點，包含：

- 它可以在 ASP.NET Web 應用程式的大量執行個體之間共用工作階段狀態，並提供改善的延展性，
- 它針對多個讀取者和單一寫入者的相同工作階段狀態資料支援受控制的並行存取權，以及
- 它可以使用壓縮來節省記憶體，並改善網路效能。


> [AZURE.NOTE] 請勿針對在 Azure 環境以外執行的 ASP.NET 應用程式使用 Azure Redis 快取的工作階段狀態提供者。 從 Azure 外部存取快取的延遲會消除快取資料的效能優勢。

同樣地，Azure Redis 快取的輸出快取提供者可讓您儲存透過 ASP.NET Web 應用程式所產生的 HTTP 回應。 使用輸出快取提供者搭配 Azure Redis 快取，可針對呈現複雜 HTML 輸出的應用程式改善回應時間；產生類似回應的應用程式執行個體可以使用快取中的共用輸出片段，而不會重新產生此 HTML 輸出。

## 建置自訂的 Redis 快取

Azure Redis 快取用作基礎 Redis 伺服器的外觀。 目前它支援一組固定的組態，但不提供 Redis 叢集。 如果您需要 Azure Redis 快取未涵蓋的進階組態 (例如大於 53 GB 的快取)，您可以使用 Azure 虛擬機器來建置並主控您自己的 Redis 伺服器。 因為您在想要實作複寫時可能需要建立數個 VM 作為主要和下層節點，這可能會是很複雜的程序。 此外，如果您想要建立叢集，則需要多個主要和下層伺服器；最小叢集、可提供高可用性的複寫拓撲，以及包含至少 6 個 VM 並組織成 3 個成對主要/下層伺服器 (叢集必須包含至少 3 個主要節點) 的延展性。 每個主要/下層配對應彼此鄰近以減少延遲，但如果您想要找出靠近應用程式，且該應用程式最有可能使用的快取資料，每一組配對可以在位於不同區域的不同 Azure 資料中心內執行。

請注意，如果您以這種方式實作自己的 Redis 快取，則必須負責監視、管理和保護服務。

## Redis 快取的資料分割

資料分割快取涉及多部電腦之間的分割快取。 此結構具有數個優點，可使用單一快取伺服器，包含：

- 建立比儲存在單一伺服器上更大的快取。
- 將資料分散到伺服器，並改善可用性。 如果一部伺服器失敗或變成無法存取，則僅其所保存的資料無法使用；剩餘伺服器上的資料仍然可以存取。 至於快取並不重要，因為快取資料僅是暫時性保留在資料庫的資料複本，而變成無法存取伺服器上的快取資料可以改為在不同的伺服器上進行快取。
- 在伺服器之間分配負載，藉此改善效能和延展性。
- 將資料配置在鄰近供使用者存取的地理位置以降低延遲。

至於快取，最常見的資料分割形式是分區化。 在此策略中，每個資料分割 (或分區) 是獨立存在的 Redis 快取。 資料會使用分區化邏輯導向至特定的資料分割，可以使用各種不同的方法來散發資料。

若要在 Redis 快取中實作資料分割，您可以採用下列方法其中之一：

- 








> [AZURE.IMPORTANT] Azure Redis Cache 目前不支援 Redis 叢集。


- 






- 


此






### 實作 Redis 快取用戶端應用程式

Redis 支援以多種程式設計語言撰寫的用戶端應用程式。 如果您要使用.NET Framework 來建置新的應用程式，建議的方法是使用 StackExchange.Redis 用戶端程式庫。 此程式庫會提供.NET Framework 物件模型，用來擷取詳細資料以便連接到 Redis 伺服器連接、傳送命令，以及接收回應。 它是在 Visual Studio 中作為 NuGet 封裝提供使用。 您可以使用這個相同的程式庫來連接至 Azure Redis 快取，或 VM 上所託管的自訂 Redis 快取。

這個方法建立的連接適用於整個用戶端應用程式的存留期，且相同連接可供多個並行執行緒使用；每次您執行 Redis 操作時請勿重新連接並中斷連接，因為這可能會降低效能。 您可以指定連接參數，例如 Redis 主機的位址和密碼。 如果您使用 Azure Redis 快取，則密碼可能是針對 Azure Redis 快取使用 Azure 管理入口網站所產生的主要或次要索引鍵。

在您已連線到 Redis 伺服器之後，可以在作為快取的 Redis 資料庫上取得控制代碼。

根據 Redis 伺服器的位置，許多作業可能會造成一些延遲，而要求會傳輸到伺服器，且回應會傳回給用戶端。 StackExchange 程式庫提供許多方法的非同步版本，它會進行公開以便協助用戶端應用程式保持回應狀態。



```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

如果您需要儲存.NET 物件，可以將其序列化為位元組資料流，並使用 StringSet 方法來寫入至快取。 同樣地，您可以使用 StringGet 方法，從快取中讀取物件，並將其還原序列化為.NET 物件。

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```



```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

如果用戶端應用程式傳送多個非同步要求，Redis 支援命令流水線。 Redis 可以使用相同連接來多工處理要求，而非以嚴格的順序接收和回應命令。 此方法有助於透過更有效率地使用網路來減少延遲。 下列程式碼片段顯示的範例會同時擷取兩個客戶的詳細資料。 程式碼會提交兩個要求，然後執行某些其他處理程序 (未顯示)，再等候接收結果。 快取物件的等候方法類似於 .NET Framework Task.Wait 方法：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

本指引中稍後＜Redis 快取的使用案例＞一節，提供一些更進階的技術範例，以便您套用至保留在 Redis 快取中的資料。

## Redis 快取的使用案例

這種案例即之前在本指引中的＜實作 Redis 快取用戶端應用程式＞一節所說明的情況。 您應該注意索引鍵也包含未解譯的資料，因此雖然可以使用任何二進位資訊作為索引鍵，但愈長的索引鍵，儲存也花費愈多的空間，且執行查閱作業的時間也會愈久。 如需可用性和維護的方便性，請仔細設計您的 keyspace 並使用有意義 (但非詳細資訊) 的索引鍵。 例如，使用結構化的索引鍵，像是 "customer:100" 來表示客戶識別碼為 100 的索引鍵，而非僅以 "100" 表示。 此配置可讓您輕鬆區別儲存不同資料類型之間的值。 例如，您也可以使用索引鍵 "orders:100" 來表示順序識別碼為 100 的索引鍵。

除了一維的二進位字串，Redis 索引鍵/值配對中的值也可以保留更結構化資訊，包含清單、集合 (已排序和未排序)，以及雜湊。 Redis 提供一組完整的命令集，可處理這些類型，且這些命令當中有許多可透過例如 StackExchange 的用戶端程式庫，使用於 .NET Framework 應用程式。

本節將針對這些資料類型和命令摘要出一些常用的案例。

### 執行不可部分完成的作業和批次作業

Redis 支援在字串值上進行一系列不可部分完成的取得和設定作業。  可用的作業包含：

- 


下列程式碼會


  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- Auch die Eigenschaften

方法將程式碼部署至服務。 下方的程式碼片段會顯示這個方法的範例。



  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- Auch die Eigenschaften



  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key/value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key/value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

您也可以將多項作業結合到單一 Redis 交易，如同本指引的＜Redis 交易與批次＞一節中所述。    Execute 方法所傳回的值表示是否已成功建立交易 (true) 或建立失敗 (false)。

下列程式碼片段顯示一個範例，即作為相同交易一部分的兩個遞增和遞減計數器：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

請記住，Redis 交易不同於關聯式資料庫中的交易。 Execute 方法僅是將所有命令排入佇列，其中包含要執行的交易，以及如果其中發生任何格式錯誤時會接著中止交易。 如果已成功將所有命令排入佇列，將會以非同步方式執行每個命令。 如果有任何命令失敗，則其他命令仍會繼續處理。 如果您需要驗證命令是否已順利完成，您必須使用對應工作的 Result 屬性來擷取命令的結果，如上述範例所示。 讀取 Result 屬性將會進行封鎖，直到工作已完成。



如需執行批次作業，您可以使用 StackExchange 程式庫的 IBatch 介面。 此介面提供一組如同 IDatabase 介面的類似方法，但不包含所有非同步的方法。 您可以使用 IDatabase.CreateBatch 方法來建立 IBatch 物件，然後再使用 IBatch.Execute 方法來執行批次，如下列範例所示。 這段程式碼僅會設定字串值，以及先前範例中相同計數器所使用的遞增和遞減，而顯示的結果如下：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

請務必了解這個方法不同於交易，如果因為格式不正確造成批次中的命令失敗，則其他命令仍然會執行；IBatch.Execute 方法不會傳回成功或失敗的任何指示。

### 執行「射後不理」快取作業

Redis 透過使用命令旗標支援「射後不理」作業。 在此情況下，用戶端僅初始化作業，但對於結果沒有興趣，且並不會等待命令完成。 下列範例顯示如何執行 INCR 命令作為「射後不理」作業：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### 自動到期金鑰

當您在 Redis 快取中儲存項目時，您可以指定逾時，而在指定時間之後，項目會自動從快取中移除。

下列程式碼片段顯示一個索引鍵設定為 20 秒後到期的範例，並查詢索引鍵的剩餘存留期：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

您也可以使用 StackExchange 文件庫中作為 KeyExpireAsync 方法的 EXPIRE 命令，為特定的日期和時間設定到期時間：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _秘訣：_您可以使用 DEL 命令手動從快取中移除項目，這項命令可透過 StackExchange 程式庫中作為 IDatabase.KeyDeleteAsync 方法來取得。

### 使用標記針對快取項目建立跨相互關聯

Redis 集是共用單一索引鍵的多個項目集合。 您可以使用 SADD 命令來建立集合。 您可以使用 SMEMBERS 命令來擷取集合中的項目。 StackExchange 程式庫會透過 IDatabase.SetAddAsync 方法實作 SADD 命令，並使用 IDatabase.SetMembersAsync 方法來實作 SMEMBERS 命令。 您也可以使用 SDIFF (集合差異)、SINTER (集合交集) 和 SUNION (集合聯集) 命令結合現有集合來建立新的集合。 StackExchange 文件庫會以 IDatabase.SetCombineAsync 方法整合這些作業；這個方法的第一個參數會指定要執行的設定作業。

下列程式碼片段顯示如何設定實用集合，以便用於快速儲存和擷取相關項目的集合。 此程式碼會使用＜實作 Redis 快取用戶端應用程式＞一節所說明的 BlogPost 類型。 BlogPost 物件包含四個欄位：識別碼、標題、排名分數和標記集合。 下方的第一個程式碼片段顯示用於填入 BlogPost 物件之 C# 清單的範例資料：

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

您可以在 Redis 快取中針對每個 BlogPost 物件將標記儲存成集合，並將每個集合與 BlogPost 識別碼建立關聯。 這可讓應用程式快速尋找屬於特定部落格文章的所有標記。 若要啟用反向搜尋，並尋找所有共用特定標記的部落格文章，您可以建立另一個集合，其中保存參考索引鍵中標記識別碼的部落格文章：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

這些結構可讓您以非常有效率的方式執行許多常見的查詢。 例如，您可以尋找並顯示部落格文章 1 的所有標記，就像這樣：

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

您可以執行集合交集作業，尋找部落格文章 1 和部落格文章 2 常用的所有標記，如下所示：

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

您可以尋找包含特定標記的所有部落格文章：

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### 尋找最近存取過的項目

許多應用程式所需的常見問題是尋找最近存取過的項目。 例如，部落格網站可能會想要顯示最近讀取過部落格文章的相關資訊。 您可以使用 Redis 清單來實作這項功能。 Redis 清單包含多個共用相同金鑰的項目，但清單會用作雙端的佇列。 您可以使用 LPUSH (推播至左) 和 RPUSH (推播至右) 命令將項目推播至清單兩端。 您可以使用 LPOP 和 RPOP 命令，從清單的任一端擷取項目。 您也可以使用 LRANGE 和 RRANGE 命令，以傳回一組元素。 下列程式碼片段顯示如何使用 StackExchange 程式庫來執行這些作業。 此程式碼使用先前範例中的 BlogPost 類型。 當使用者讀取部落格文章時，系統會透過使用 IDatabase.ListLeftPushAsync 方法，將部落格文章的標題推播至與 Redis 快取中索引鍵 "blog:recent_posts" 相關聯的清單：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

當讀取更多部落格文章時，其標題會推播至相同的清單。 清單是根據其新增的順序來進行排序；最近讀取過的部落格文章會靠向清單左端 (如果相同部落格文章讀取一次以上，則它會在清單中具有多個項目)。 您可以使用 IDatabase.ListRange 方法，以顯示最近讀取過文章的標題。 這個方法會採用包含清單、起點和終點的索引鍵。 下列程式碼會從清單中最左端擷取 10 個部落格文章 (項目從 0 到 9) 的標題：

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

請注意，ListRangeAsync 不會從清單中移除項目；若要這樣做，您可以使用 IDatabase.ListLeftPopAsync 和 IDatabase.ListRightPopAsync 方法。

若要防止清單無限成長，您可以透過修剪清單以定期挑選項目。 下方程式碼片段會從清單移除 5 個位於最左端以外的所有項目：

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### 實作領導者面板

依預設，集合中的項目不會以任何特定順序保留。 您可以使用 ZADD 命令 (StackExchange 的文件庫中的 IDatabase.SortedSetAdd 方法) 來建立已排序的集合。 系統會使用一個稱為分數的數值來排序項目，該分數是作為命令的參數而提供。 下列程式碼片段會將部落格文章的標題新增至已排序的清單中。 在範例中，每個部落格文章也會有包含部落格文章排名的分數欄位。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

您可以使用 IDatabase.SortedSetRangeByRankWithScores 方法，以遞增分數的順序來擷取部落格文章的標題和分數：

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] StackExchange 程式庫也會提供 IDatabase.SortedSetRangeByRankAsync 方法，用來以分數順序傳回資料，但不會傳回分數。

您也可以使用分數遞減順序來擷取項目，並透過將額外參數提供給 IDatabase.SortedSetRangeByRankWithScoresAsync 方法來限制傳回項目的數目。 下一個範例會顯示前 10 項排名部落格文章的標題和分數：

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

下一個範例會使用 IDatabase.SortedSetRangeByScoreWithScoresAsync 方法，可用來限制傳回給那些落在指定分數範圍的項目：

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### 使用通道傳訊

Redis 伺服器除了用作資料快取，可透過高效能發行者/訂閱者的機制提供傳訊功能。 用戶端應用程式可以訂閱通道，且其他應用程式或服務可以將訊息發佈至通道。 訂閱應用程式接著將會收到這些訊息，以便能夠進行處理。

若要訂閱通道，Redis 會提供 SUBSCRIBE 命令。 此命令需要一個或多個的通道名稱，其中應用程式會接受訊息。 StackExchange 程式庫包含 ISubscription 介面，可讓 .NET Framework 應用程式訂閱和發佈至通道。 您使用連接至 Redis 伺服器的 GetSubscriber 方法來建立 ISubscription 物件，然後使用此物件的 SubscribeAsync 方法來接聽通道上的訊息。 下列程式碼範例顯示如何訂閱稱為 "messages:blogPosts" 的頻道：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Subscribe 方法的第一個參數即為通道的名稱。 這個名稱會遵循快取中索引鍵所使用的相同慣例，而且可以包含任何二進位資料，但建議您最好使用相對較短且有意義的字串，以便協助您確保良好的效能和可維護性。 您也應該注意通道所使用的命名空間與索引鍵所使用的不同，因此您可以擁有具有相同名稱的通道和索引鍵，不過這可能會讓應用程式程式碼更難進行維護。

第二個參數是動作委派。 每當新的訊息出現在通道上，這個委派就會以非同步方式執行。 這個範例僅顯示主控台上的訊息 (訊息將包含部落格文章的標題)。

若要發佈至通道，應用程式可以使用 Redis PUBLISH 命令。 StackExchange 程式庫提供 IServer.PublishAsync 方法來執行這項作業。 下列程式碼片段顯示如何將訊息發佈到 "messages:blogPosts" 通道：

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

關於發佈/訂閱機制有幾個您應該了解的重點：

- 多個訂閱者可以訂閱相同的通道，且他們都將接收發佈至該通道的訊息。
- 訂閱者僅會接收訂閱後發佈的訊息。 通道不會進行緩衝處理，且一旦發佈訊息之後，Redis 基礎結構會將訊息推播至每個訂閱者並將它移除。
- 依預設，訂閱者會根據傳送的順序來接收訊息。
If



  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## 相關的模式和指引

在您的應用程式中實作快取時，下列模式也可能與您的案例相關：

- 此模式也有助於針對快取中所保留的資料與原始資料存放區中的資料之間維護一致性。
- 

## 相關資訊

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





