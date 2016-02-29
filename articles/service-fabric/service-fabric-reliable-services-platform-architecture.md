<properties
   pageTitle="可靠的服務架構 | Microsoft Azure"
   description="可設定狀態與無狀態服務的可靠服務架構的概觀。"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# 具狀態與無狀態之可靠的服務架構

Service Fabric 可靠的服務可能是可設定狀態或無狀態。 每一種服務都在這篇文章中所述的特定架構內執行。
請參閱 [可靠的服務概觀](service-fabric-reliable-services-introduction.md) 如需可設定狀態與無狀態服務之間的差異。

## 具狀態之可靠的服務

### 具狀態服務的架構
![架構圖表](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### 具狀態之可靠的服務

具狀態可靠的服務可以從 StatefulService 或 StatefulServiceBase 類別衍生。 這兩個基底類別所提供的 Service Fabric 和
提供各種層級的支援和抽象概念與 Service Fabric 互動，以及加入 Service Fabric 叢集內的服務，您可設定狀態的服務。
StatefulService 衍生自 StatefulServiceBase；StatefulServiceBase 提供服務更多的彈性，但需要對 Service Fabric 內部運作有更多了解。
請參閱 [可靠的服務概觀](service-fabric-reliable-services-introduction.md) 和 [可靠的服務進階用法](service-fabric-reliable-services-advanced-usage.md) 如需詳細資訊
撰寫使用 StatefulService 和 StatefulServiceBase 類別的服務。

這兩個基底類別會管理服務實作的存留期和角色。 服務實作可能會覆寫任一基底類別的虛擬方法，如果服務實作有工作来執行
在服務實作生命週期中的這些點，或是想要建立通訊接聽程式物件。 請注意，雖然服務實作
可能會實作公開 ICommunicationListener，在上圖中，通訊接聽程式是自己的通訊接聽程式物件
Service Fabric 所實作，因為服務實作會使用 Service Fabric 所實作的通訊接聽程式。

您的具狀態可靠的服務會使用可靠的狀態管理員來利用可靠的集合。 可靠的集合是您的服務，以高度可用的本機資料結構
也就是一直都能不論服務容錯移轉。 可靠的集合的每個類型是由可靠的狀態提供者所實作。
如需可靠的集合的詳細資訊請參閱 [可靠的集合概觀](service-fabric-reliable-services-reliable-collections.md)

### 可靠的狀態管理員和狀態提供者

可靠的狀態管理員是管理可靠的狀態提供者，而且功能來建立、 刪除、 列舉和確保可靠的狀態提供者的物件
持續性和高可用性。 可靠的狀態提供者執行個體代表持續保存且高度可用的資料結構的執行個體例如
字典或佇列。 每個可靠的狀態提供者會公開具狀態服務所使用的介面，以和可靠的狀態提供者互動。 例如
IReliableDictionary 用來與可靠的字典而 IReliableQueue 用來與可靠的佇列。 所有可靠的狀態提供者都實作 IReliableState 介面。

可靠的狀態管理員具有名為 IReliableStateManager 可允許存取它，您可設定狀態的服務介面。 與可靠的狀態提供者的介面會透過 IReliableStateManager 傳回。

可靠的狀態管理員使用外掛程式架構，因此新類型的可靠的集合可以動態地插入。

可靠的字典和可靠的佇列是建立在高效能版本的差異存放區實作上。

### 交易式複寫器

交易式複寫器元件會負責確保狀態的服務，也就是可靠的狀態管理員和可靠的集合內的狀態，
在所有複本皆保持一致執行您的服務與該狀態會同時保存記錄檔中。 可靠狀態管理員透過私用機制與交易式複寫器互動。

交易式複寫器使用網路通訊協定來與服務執行個體的其他複本溝通狀態，如此所有複本都有最新的狀態資訊。

交易式複寫器使用記錄來保存狀態資訊，因此狀態資訊可以保存經歷處理序或節點當機。 記錄檔的介面是透過私用機制。

### 記錄檔

記錄元件提供高效能的持續性存放區，它可以針對寫入旋轉或固態磁碟而最佳化，也針對最有效率地使用磁碟空間進行最佳化。 設計
記錄檔是永續性儲存體 (例如硬碟)
若要為本機執行您可設定狀態的服務，以允許低延遲及高輸送量，相較於不屬於本機節點的永續性儲存體的節點。

記錄元件會使用兩種類型的記錄檔。 有一個全節點的共用記錄檔，應該位於只用於該記錄檔的磁碟上。 這個檔案被置於 Service Fabric 節點
工作目錄。 您服務的每個複本也有專用的記錄檔，並放在服務的工作目錄內。 共用的記錄檔是狀態資訊的過渡區域
專用的記錄檔時保存位置的最終目的地。 在這種設計的狀態資訊會先寫入共用的記錄檔，然後延遲地轉出到專用的記錄檔
在背景中的檔案。 如此，對共用記錄檔的寫入延遲會最小且輸送量會最高，可允許服務進度更快速。

不過當記錄元件使用 OptimizeForLocalSSD 設定固態磁碟最佳化設定，則狀態資訊會寫入直接與專用
記錄檔，會略過共用的記錄檔。 由於固態磁碟不會發生磁頭移動爭用而導致延遲，因此直接寫入專用記錄檔沒有負面影響。

當記錄元件已最佳化，使用 OptimizeLogForLowerDiskUsage 將磁碟空間用量減到最低時，專用記錄檔會建立為 NTFS 疏鬆檔案。  因為記錄檔通常
並不一定裝滿狀態資訊的疏鬆檔案使用能過度佈建的磁碟空間供更多複本。 如果未以這種方式設定記錄檔空間
會預先配置，而且記錄元件可以直接寫入至檔案最高的效能。

除了對記錄檔的最小使用者模式介面，記錄檔會撰寫為核心模式驅動程式。 藉由執行核心模式驅動程式記錄檔可以提供最高的效能，所有服務，
使用它。

如需設定記錄的詳細資訊，請參閱 [設定具狀態可靠服務](service-fabric-reliable-services-configuration.md)。

## 無狀態之可靠的服務

### 無狀態服務的架構
![架構圖表](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### 無狀態之可靠的服務

無狀態服務實作衍生自 StatelessService 類別或 StatelessServiceBase 類別，其中 StatelessServiceBase 類別比 StatelessService 允許更多彈性。
這兩個基底類別會管理服務的存留期和角色。 服務實作可能會覆寫任一基底類別的虛擬方法，如果您的服務有工作来執行
在服務生命週期中的這些點，或是想要建立通訊接聽程式物件。 請注意，雖然您的服務可能會實作自己的通訊
接聽程式物件並公開 ICommunicationListener，在上圖中，通訊接聽程式由 Service Fabric 做為該服務實作會使用
Service Fabric 所實作的通訊接聽程式。

請參閱 [可靠的服務概觀](service-fabric-reliable-services-introduction.md) 和 [可靠的服務進階用法](service-fabric-reliable-services-advanced-usage.md) 如需詳細資訊
撰寫使用 StatelessService 和 StatelessServiceBase 類別的服務。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

如需 Service Fabric 的詳細資訊，請參閱：

[可靠的服務概觀](service-fabric-reliable-services-introduction.md)

[快速啟動](service-fabric-reliable-services-quick-start.md)

[可靠的集合概觀](service-fabric-reliable-services-reliable-collections.md)

[可靠的服務的進階用法](service-fabric-reliable-services-advanced-usage.md)

[可靠的服務組態](service-fabric-reliable-services-configuration.md)  
 

