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

快取是常用的技巧，進而改善效能和
擴充性的系統暫時複製經常存取的資料
若要快速靠近應用程式的儲存體。 如果這個快速資料儲存體
位於更接近應用程式會比原始來源，然後快取
可以大幅改善回應時間，用戶端應用程式服務
資料更快。 當用戶端執行個體重複快取是最有效
讀取相同的資料，特別是當資料保持相對靜態與
原始資料存放區是相對於快取的速度變慢，
主旨的高爭用，或不遠時網路延遲
可能會造成存取速度緩慢。

## 分散式應用程式中的快取

分散式應用程式通常會實作一個或兩個
快取資料時，下列策略 ︰

- 使用私人快取，其中資料保留在執行應用程式或服務執行個體的本機電腦上。
- 使用共用快取，作為可由多個處理程序和/或電腦存取的常見來源。

在這兩種情況下，在快取可能是 （藉由處理程序的執行的用戶端
使用者介面的系統，例如 web 瀏覽器或桌面應用程式），
和/或伺服器端 （藉由處理程序提供商務服務
從遠端執行）。

### 私人快取

最基本的快取類型是記憶體中存放區，保留的位址
在單一處理序空間執行的程式碼直接存取
在這個程序。 這種類型的快取是非常快速的存取，而且它可以
提供非常有效的策略儲存適度大量
靜態資料的快取大小通常受限於
裝載處理序的電腦上可用的記憶體數量。 如果您
需要快取不在記憶體中，實際可用的資訊
您可以寫入本機檔案系統快取的資料。 這樣可讓
一定會是存取資料保留在記憶體中，但應該比速度較慢
仍然可以更快速且更可靠的網路上擷取資料。

如果您有多個使用此模型的應用程式執行個體
同時執行，每個應用程式執行個體將會有自己
獨立的快取保留它自己的資料複本。

您應該將快取視為在原始資料的快照集
在過去的點。 如果這項資料不是靜態的可能是，
不同的應用程式執行個體將會以不同版本的
其快取中的資料。 因此，這些執行的相同查詢
執行個體可能傳回不同的結果，如 [圖 1 所示。

![在不同應用程式執行個體中使用記憶體中快取](media/best-practices-caching/Figure1.png)

_圖 1：在不同應用程式執行個體中使用記憶體中快取_

### 共用快取

使用共用快取有助於減少資料可能的考量
與每個快取中，可能發生於記憶體中快取的不同。 共用
快取可確保不同的應用程式執行個體會看到相同
藉由在不同的位置，找出快取的快取資料檢視
通常託管為屬於不同的服務，如 [圖 2 所示。

![使用共用快取_](media/best-practices-caching/Figure2.png)

_圖 2：使用共用快取_

使用共用快取方式的重要優點是
它可以協助提供延展性。 許多共用快取服務
使用伺服器叢集來實作及使用軟體，
將資料分散於叢集以透明的方式。 「
應用程式執行個體只會將要求傳送至快取服務，
和基礎結構會負責決定活動
在叢集中的快取資料的位置。 您可以輕鬆地調整
新增更多伺服器的快取。

方法的共用快取缺點就是快取
因為它不再保留在本機至每個存取速度較慢
應用程式執行個體，以及實作個別的要求
快取服務可能會加入至方案的複雜性。

## 使用快取的考量

下列各節說明詳細的考量
設計和使用快取。

### 何時應該快取資料？

快取可大幅改善效能、延展性和可用性。 詳細資料
您所需要存取此資料，較大的使用者數目愈大，
快取會減少延遲和競爭處理相關聯的優點
將較大的原始資料存放區中的並行要求。 例如，資料庫
可能支援有限的數量的並行連線，但從共用擷取資料
快取，而不是基礎資料庫允許存取此資料的用戶端應用程式
即使目前已用完可用的連線數目。 此外，如果
資料庫會變成無法使用，用戶端應用程式可能會繼續使用
資料保留在快取。

您應該考慮快取讀取頻繁，但很少修改的資料
（資料具有高比例的讀取作業相較於寫入作業）。 不過，
您不應該使用快取為授權存放區的重要資訊。您應該
請確定您的應用程式無法承受損失的所有變更一律都儲存至
永續性資料存放區。 如此一來，如果快取無法使用，您的應用程式可以
仍繼續使用資料存放區作業，就不會遺失重要
資訊。

### 資料類型和快取母體擴展策略

若要有效地使用快取的關鍵在於決定最適合的資料
快取，並在適當的時間快取它。 資料可能會在加入至快取
指定第一次，讓應用程式必須擷取應用程式
從資料存放區，而後續存取可滿足之後，只擷取資料
使用快取。

或者，快取可能會部分或完全填入資料、
通常應用程式啟動時 （稱為植入方法）。 不過，有可能會
不是建議實作大型快取植入，因為這種方法可以強制
原始資料存放區應用程式開始執行時突然的高負載。

通常的使用模式分析可以協助決定是否要完全或部分
預先填入快取中，為，並選擇應該快取的資料。 比方說，它
可能會有用來植入快取的靜態使用者設定檔資料
客戶使用的應用程式定期 （也許是每天），但不適用於
使用應用程式一次一週的客戶。

快取通常適用於不變，或變更的資料
很少。 範例包括參考資訊，例如產品和定價
電子商務應用程式或最密切相關的共用靜態資源中的資訊
若要建構。 部分或所有這些資料可以載入至快取，應用程式
啟動資源需求降至最低並改善效能。 它也可以是
適用於具有背景處理序，會定期更新參考資料
在快取中，確認其為最新版本，或重新整理快取時參考
資料變更。

快取可能會較不適合動態資料，雖然有一些例外狀況
這項考量 （請參閱章節稍後在此快取高動態資料
指引的詳細資訊）。 當原始資料定期變更或是
快取的資訊可以很快會變得過時或保留的額外負荷
與原始資料存放區同步處理快取可降低的效果
快取。 請注意，快取並沒有包含完整資料
實體。 例如，如果資料項目代表多重值的物件，例如銀行
這些元素可能具有名稱、 地址和帳戶餘額的客戶，
維持靜態 （名稱和地址），而其他 （例如帳戶餘額）
可能是更動態的。 在這些情況下，可能會有用來快取靜態
部分資料，只擷取 （或計算） 剩餘資訊
並在必要時。

應該執行效能測試和流量分析以判斷是否
預先填入或隨選載入快取中，或是兩者的組合，
適當的。 此決策應該根據為變動性的組合，
使用資料的模式。 快取使用量和效能分析
特別重要，尤其會遇到大量載入，而且必須是應用程式
具有高擴充性。 比方說，具有高擴充性案例中就不難理解了
植入快取在尖峰時間減少資料存放區的負載。

快取也可用來避免重複計算，因為應用程式
運作。 如果將資料轉換作業，或執行複雜的計算，
它可以快取中儲存作業的結果。 如果相同的計算
是必要之後，應用程式可以只擷取的結果
快取。

應用程式可以修改資料保留在快取，不過您應該考慮
做為可能隨時消失的暫時性資料存放區的快取。 不要儲存
重要的資料，只在快取，但請確定您維護的資訊
在原始資料存放區以及。 如此一來，如果快取
無法使用，您可以盡可能減少遺失資料的機會。

### 快取高動態資料

儲存永續性資料存放區中快速變更造成的資訊
對系統造成負擔。 例如，假設裝置持續報告
狀態或其他度量單位。 如果應用程式選擇不要快取這
快取的資訊幾乎一律會過期，然後為基礎的資料
相同的考量可能是儲存和擷取這項資訊時，則為 true。
從資料存放區中。儲存並擷取這項資料所花費的時間內可能
變更。 在這類情況下，請考慮儲存動態的優點
直接在快取，而非持續性資料存放區中的資訊。 如果
資料為非緊急，而且不需要進行稽核，則並不重要
如果遺失非經常性變更。

### 管理快取中的資料到期

在大部分情況下，保留在快取中的資料會保留原始資料中的資料的複本
存放區。 已快取，造成之後，可能會變更原始資料存放區中的資料
快取的資料過時。 許多快取系統可讓您設定
快取過期資料，並減少為其資料可能過期的期間。

快取的資料過期時則會從快取中移除，而必須使用應用程式
從原始 （它可以將新擷取的資料存放區擷取資料
資訊傳回至快取）。 您可以設定預設到期原則時您
設定快取。 您也可以在許多快取服務規定到期日
當您儲存它們以程式設計方式快取中的個別物件
(某些快取可讓您將到期時間指定為絕對值，或
使變化值如果不是要從快取中移除的項目
在指定時間內存取。 此設定會覆寫任何快取範圍
到期原則，但僅會針對指定的物件。

> [AZURE.NOTE] 請考慮快取和物件，其中包含仔細的逾期期限。 如果設定的時間太短，則物件會太快過期，而且會減少您使用快取的優點。 如果設定的時間太長，則您需負擔資料過時的風險。

此外，也可以快取可能會填滿，如果資料可以維持
常駐長的時間。 在此情況下，任何要求加入新項目到
快取可能會導致某些項目遭到強制移除，這道程序中
收回。 快取服務通常會收回最最近使用過的 (LRU) 上的資料
為基礎，但是您可以通常覆寫此原則並防止項目
收回。 不過，若您採用這種方法可能會超過您快取
記憶體，它有可用的且應用程式嘗試新增項目
快取將會失敗並發生例外狀況。

某些快取實作可能會提供其他收回原則。 這
應用程式通常包括最近使用的原則 (在預期的情況下，
資料將不會需要一次），（最舊的資料是後進先出原則
收回第一次），或根據觸發事件的明確移除 (例如
要修改資料）。

### 用戶端快取中的失效資料

資料保留在用戶端快取通常被視為是外部
提供資料給用戶端，服務的 auspices服務
不能直接強制用戶端來新增或移除資訊
用戶端快取。 這表示有可能使用的用戶端
（例如，到期的原則設定不良快取
適當地實作） 若要繼續使用過時的資訊快取
在本機上原始資料來源中的資訊已變更。

如果您要建置 web 應用程式，可透過 HTTP 的資料
連線，您可以隱含地強制 web 用戶端 (例如瀏覽器或
web proxy) 來擷取最新的資訊，如果更新資源
藉由變更該資源的 URI。 Web 用戶端通常會使用 URI
在用戶端快取索引鍵的資源，因此變更 URI
導致 web 用戶端忽略任何先前快取的版本
資源和改為擷取新的版本。

## 管理快取中的並行存取

快取通常會設計成共用多個執行個體
重新接收。 每個應用程式執行個體可以讀取及修改資料
快取。 因此，相同並行發生的問題
任何共用的資料存放區也適用於快取。 情況下
應用程式需要修改資料保留在快取中，您也可以
確保應用程式的一個執行個體所進行的更新
不要盲目地覆寫另一個執行個體所做的變更。

視資料的本質和衝突的可能性
您可以採用兩種方式可以並行處理的其中一個 ︰

- __開放式。__ 應用程式會檢查快取中的資料自擷取之後，以及正要更新之前是否已變更。 如果資料仍然相同，則可以進行變更。 否則，應用程式必須決定是否要進行更新 (驅動這項決策的商務邏輯屬於應用程式特定)。 這種方法適合不常更新，或其中不太可能發生衝突的情況。
- __封閉式。__ 應用程式擷取快取中的資料時會鎖定資料，以避免另一個執行個體變更資料。 此程序可確保不會發生衝突，但可能會封鎖其他需要處理相同資料的執行個體。 封閉式並行存取可能會影響解決方案的延展性，而且僅應用於短期的作業。 這種方法可能適用於很可能發生衝突的情況，特別是當應用程式更新快取中的多個項目，且必須確保這些變更會一致套用的情況。

### 實作高可用性和延展性，並改善效能

不應該快取的資料; 主要的儲存機制這是角色
要從中填入快取的原始資料存放區。 Auch die Eigenschaften
原始資料存放區會負責確保的持續性
資料。

請小心不要介紹關鍵性的相依性的可用性
您的方案到共用快取服務。 應用程式應該是
能夠繼續運作如果提供共用快取的服務
無法使用。應用程式不應該在停止回應，或在等待時失敗
若要繼續快取服務。 因此，必須是應用程式
準備好偵測快取服務的可用性，並切換回
原始資料存放區無法存取快取時。 Auch die Eigenschaften
[斷路器模式](http://msdn.microsoft.com/library/dn589784.aspx) 對於處理這種情況下很有用。 Auch die Eigenschaften
服務提供可以復原快取，而且它會變成一次
使用快取重新填入表單讀取資料時
原始資料存放區，下列策略，例如 [另行快取模式](http://msdn.microsoft.com/library/dn589799.aspx)。

不過，在回到原始資料存放區如果快取
暫時無法使用可能會影響延展性系統。
資料存放區時正在執行復原原始資料存放區
可能忙於處理資料，導致逾時的要求，
連接失敗。 您應該考慮的策略是
在應用程式的每個執行個體中實作本機私人快取
搭配共用快取的所有應用程式執行個體
存取權。 當應用程式擷取項目時，可能會先檢查
在本機快取，則共用快取中，最後原始
資料存放區。 可以使用中的資料來填入本機快取
共用快取或如果無法使用共用快取的資料庫。
此方法需要仔細的組態設定，以防止本機
變得太過時相對於共用快取，但它的快取
無法連線到共用快取時，扮演的緩衝區。 圖 3
顯示此結構。

![使用本機、 私人快取的共用快取 _](media/best-practices-caching/Caching3.png)
_[圖 3 ︰ 使用本機、 私人快取搭配共用快取_

若要支援大型快取部分保留相對存留較久的資料，
快取服務可提供實作高可用性選項
自動容錯移轉快取無法使用時。 這種方法
通常需要複寫儲存在主要上的快取的資料
次要的快取伺服器，並切換至快取伺服器
如果主要伺服器失敗或連接的次要伺服器
遺失。 若要減少寫入多個相關聯的延遲
當資料寫入主要快取的目的地
伺服器上，複寫到次要伺服器可能會發生
以非同步的方式。  這個方法會導致某些
快取的資訊可能會發生問題時，遺失但
這項資料的比例應該很小相較於整體
快取的大小。

如果共用快取很大，可能很有幫助資料分割
跨節點，以減少爭用的機會，快取資料，
改善延展性。 許多共用快取支援的功能
以動態方式加入 （和移除） 節點，以及重新平衡跨資料
資料分割。 這種方法可能牽涉到叢集，
節點的集合會向用戶端應用程式
隨選即用單一快取，但是在內部資料會分散
下列部分的節點之間預先定義的散發策略
其中平均地平衡負載。  [資料分割的指引文件](http://msdn.microsoft.com/library/dn589795.aspx)
Microsoft 網站提供關於可能詳細資訊
資料分割策略。

叢集也可以進一步新增快取的可用性如果
節點失敗時，快取的其餘部分則仍可存取。
使用叢集時經常搭配複寫
與容錯移轉。每個節點皆可複寫且複本
時快速上線的節點失敗。

許多讀取和寫入作業將可能涉及單一資料
值或物件。 不過，有時候可能時
需要儲存或快速擷取大量資料。
例如，植入快取可能會牽涉到寫了上百或
可能需要數千個項目快取中，或應用程式
從快取做為擷取大量相關項目
同一個要求的一部分。 許多大型快取會提供批次
針對這些用途，讓用戶端應用程式的作業
大量的項目插入單一要求封裝和
減少相關聯執行大量的額外負荷
小型要求。

## 快取和最終一致性

另行快取模式取決於應用程式的執行個體
填入可以存取最新的快取和
一致的資料版本。 在系統中實作
這可能是最終一致性 （例如複寫的資料存放區）
不是如此。 可以修改應用程式的一個執行個體
項目資料，並使該項目的快取的版本。 另一個
應用程式的執行個體可能會嘗試讀取此項目
這會導致快取遺漏，讓它顯示的資料快取
資料存放區，並將它加入至快取。 不過，如果資料存放區
尚未完全同步的其他複本
應用程式執行個體可能讀取並填入快取
舊值。

如需處理資料一致性的詳細資訊，請參閱
Microsoft 網站上的資料一致性指引頁面。

### 保護快取的資料

無論快取服務使用，您應該考慮
如何保護從未經授權的快取中保留的資料
存取權。 其中有兩個主要的考量：

- 快取中的資料隱私權。
- 快取之間流動的資料隱私權和
  使用快取應用程式。

若要保護快取中的資料，可能會實作快取服務
要求的驗證機制的應用程式
經過身分識別，並授權配置
指定哪些身分識別可以存取快取中的資料，
作業 （讀取和寫入），這些身分識別
允許執行。 若要減少負擔
讀取和寫入資料，一旦被授與身分識別
寫入和/或快取的身分識別可以使用 「 讀取 」 權限
快取中的任何資料。 如果您需要限制存取
快取資料的子集，您可以 ︰

- 分割成資料分割的快取 （藉由使用不同的快取
  伺服器） 並僅授與存取權的身分識別
  若要使用，應該允許的資料分割或
- 使用不同的金鑰來加密每一個子集合中的資料
  並只身分的加密金鑰
  應該可以存取每個子集。 用戶端應用程式
  仍然可以擷取所有快取中的資料
  但它只能夠解密它的資料
  具有索引鍵。

若要保護的資料，因為它出入快取您
相依於網路所提供的安全性功能
用戶端應用程式用來連接到基礎結構
快取。 如果使用現場伺服器來實作快取
裝載在相同組織中用戶端應用程式
然後網路本身的隔離可能不需要您
進行任何額外的步驟。 如果快取位於遠端和
需要透過公用網路 （例如 TCP 或 HTTP 連線
做為網際網路），您應該考慮實作 SSL。

## 使用 Microsoft Azure 實作快取的考量

Azure 提供了 Azure Redis 快取。 這是實作
開啟的來源做為服務中執行的 Redis 快取
Azure 資料中心。 它提供可在快取服務
是否從任何 Azure 應用程式，存取應用程式
會實作為雲端服務、 網站或內部
Azure 虛擬機器。 用戶端可以共用快取
應用程式具有適當的存取金鑰。

Redis 是高效能的快取解決方案，提供
可用性、 延展性和安全性。 通常會執行
以服務分散到一或多個專用的機器，
嘗試儲存可能是記憶體中的資訊越
確保快速存取。 這個架構的目的在於提供
低延遲及高輸送量，藉由減少到
執行緩慢的 I/O 作業。

Azure Redis 快取是與許多各種相容
用戶端應用程式的 Api。 如果您有現有的
應用程式已使用執行於內部，Redis
Azure Redis 快取提供快取的快速移轉路徑
在定域機組。

> [AZURE.NOTE] Azure 也提供受管理的快取服務。 此
  服務根據 Microsoft AppFabric 快取引擎。 此程式碼
  可讓您建立可共用的分散式快取
  鬆散耦合的應用程式。 在裝載快取
  在 Azure 資料中心中執行的高效能伺服器。
  不過，不建議使用此選項，並僅
  提供給支援現有已建置的應用程式
  若要使用它。 所有新的程式開發使用 Azure Redis
  快取。
>
> 此外，Azure 支援角色中快取。 這項功能
  可讓您建立雲端服務的特定快取。
  快取所在的 web 或背景工作角色執行個體和
  只能為在同一個執行作業的角色存取
  雲端服務部署單位 （部署單位是集合
  角色執行個體部署為雲端服務在特定的
  區域）。 快取已叢集化，以及所有執行個體
  託管快取之相同部署單位內的角色
  成為相同的快取叢集的一部分。 現有的應用程式
  使用角色中快取的可以繼續這麼做，但
  移轉至 Azure Redis 快取可能會提供更多優點。
  如需有關是否要使用 Azure Redis 快取
  或角色中快取，請瀏覽的頁面
  [我適合使用哪個 Azure 快取服務？](http://msdn.microsoft.com/library/azure/dn766201.aspx) Microsoft 網站。


### Redis 的功能

Redis 是多個簡單的快取伺服器;它提供分散式的記憶體中
具有廣泛的命令集，可支援許多常見的情況下，資料庫
稍後在此快取中的使用情況下一節所述 Redis
文件。 本節將摘要列出一些 Redis 的主要功能
提供。

### Redis 作為記憶體中資料庫

Redis 同時支援讀取和寫入作業。 不同於許多快取 (這應視為暫時性資料存放區)，寫入可以透過在本機快照集檔案或僅限附加的記錄檔中定期儲存來防止系統失敗。 所有寫入皆為非同步，且不會封鎖用戶端讀取和寫入資料。 當 Redis 開始執行時，它會從快照集或記錄檔讀取資料，並使用它來建構記憶體中快取。 如需詳細資訊，請參閱 [Redis 持續性](http://redis.io/topics/persistence) Redis 網站上。

> [AZURE.NOTE] Redis 不保證所有寫入將會都儲存在事件
  災難性的失敗，但在最糟，您應該僅遺失幾秒鐘
  資料量。 請記住，不能做為快取
  授權的資料來源，並負責應用程式
  使用快取，以確保重要資料已成功儲存到
  適當的資料存放區。 如需詳細資訊，請參閱＜另行快取模式＞。

#### Redis 資料類型

Redis 是索引鍵值存放區，其中的值可以包含簡易類型或複雜資料結構，例如雜湊、清單，以及集合。 它針對這些資料類型支援一組不可部分完成的作業。 索引鍵可以永久存在或標記為有限的存留時間，屆時索引鍵及其對應的值會自動從快取中移除。 如需有關 redis 索引鍵和值的詳細資訊，請瀏覽的頁面 [Redis 資料類型和抽象概念簡介](http://redis.io/topics/data-types-intro) Redis 網站上。

#### Redis 複寫和叢集

Redis 支援主要/下層複寫，以協助確保可用性並維護輸送量；Redis 主要節點的寫入作業會複寫至一個或多個下層節點，而讀取作業可由主要或任何的下層項目來提供。 至於網路磁碟分割，下層項目可以繼續提供資料，然後在重新建立連接時以透明的方式與主要節點重新同步。 如需詳細資訊，請瀏覽 [複寫](http://redis.io/topics/replication) Redis 網站上的頁面。

Redis 也提供叢集，可讓您以透明的方式在伺服器之間將資料分割成分區並分散負載。 這項功能隨著可新增新的 Redis 伺服器可改善延展性，且增加資料可重新分割的快取大小。 此外，叢集中的每一部伺服器可以使用主要/下層複寫進行複寫，以確保整個叢集中每個節點的可用性。 如需有關叢集和分區化的詳細資訊，請瀏覽 [Redis 叢集教學課程頁面](http://redis.io/topics/cluster-tutorial) Redis 網站上。

> [AZURE.NOTE] Azure Redis 快取目前不支援叢集。 如果您想要建立 Redis 叢集，您可以建置自己的自訂 Redis 伺服器。 如需詳細資訊，請參閱本文件稍後的＜建置自訂的 Redis 快取＞一節。

### Redis 記憶體使用

Redis 快取具有有限的大小，取決於主機電腦上可用的資源。 當您設定的 Redis 伺服器時，您可以指定伺服器可以使用的記憶體最大數量。 Redis 快取中的索引鍵可以設定到期時間，屆時它會自動從快取中移除。 這項功能可協助避免記憶體中快取填滿老舊或過時的資料。

當記憶體填滿時，Redis 可以遵循原則數目自動收回索引鍵及其值。 預設值是 LRU (最近最少使用的)，但您也可以選取其他原則，例如，隨機收回金鑰，或完全關閉收回 (在此情況下，當快取已滿時嘗試將項目新增至快取將會失敗)。 頁面 [使用 Redis 作為 LRU 快取](http://redis.io/topics/lru-cache) 提供詳細資訊。

### Redis 交易與批次

Redis 可讓用戶端應用程式提交一系列的作業，以便在快取中讀取和寫入資料以作為不可部分完成的交易。 交易中的所有命令保證會循序執行，且其他並行用戶端所發出的命令將不會在兩者之間交互編排。 不過，這些不是實際上的交易，因為關聯式資料庫會執行它們。 交易處理包含兩個階段；即命令佇列和命令執行。 在命令佇列階段期間，用戶端會提交構成交易的命令。 如果此時發生某種形式的錯誤 (例如語法錯誤，或參數數目不正確) 則 Redis 將會拒絕處理整個交易並捨棄它。 在執行階段期間，Redis 會循序執行每個佇列中的命令。 如果在這個階段期間命令失敗，Redis 將會繼續執行下一個佇列中的命令，且它不會復原任何已執行命令的效果。 這個簡化的交易形式可協助維護效能，並避免爭用所造成的效能問題。 Redis 會實作一種開放式鎖定，以便協助維護一致性。 如需使用 Redis 交易和鎖定的詳細資訊，請瀏覽 [交易頁面](http://redis.io/topics/transactions) Redis 網站上。

Redis 也支援非交易式批次要求。 用戶端用來將命令傳送到 Redis 伺服器的 Redis 通訊協定，可讓用戶端將以相同要求的一部分來傳送一系列作業。 這有助於減少在網路上的封包分割。 處理批次時，系統會執行每個命令。 不同於交易，如果這些命令的任何格式不正確，則將會遭到拒絕，但會執行剩餘的命令。 另外也不保證批次中處理命令的順序。

### Redis 安全性

Redis 純粹著重於提供資料的快速存取，並設計為在受信任的環境中執行，且僅能由受信任的用戶端存取。 Redis 僅支援以密碼驗證為基礎的有限安全性模型 (可以完全移除驗證，然而不建議這樣做)。 所有已驗證的用戶端會共用相同的全域密碼，並可存取相同的資源。 如果您需要更完整的登入安全性，您必須在 Redis 伺服器之前實作自己的安全性層級，且所有用戶端要求應通過此額外的層級，Redis 不應直接向不受信任或未經驗證的用戶端公開。

您可以透過停用命令或重新命名命令 (僅提供有權限的用戶端使用新的名稱) 來限制命令的存取。

Redis 不直接支援任何形式的資料加密，因此所有編碼必須由用戶端應用程式執行。 此外，Redis 不提供任何形式的傳輸安全性，因此如果您基於資料在網路上流通而需要保護資料，您應實作 SSL Proxy。

如需詳細資訊，請瀏覽 [Redis 安全性](http://redis.io/topics/security) Redis 網站上的頁面。

> [AZURE.NOTE] Azure Redis 快取提供自己的安全性層級，讓用戶端連線。基礎 Redis
  伺服器不會公開至公用網路。

### 使用 Azure Redis 快取

Azure Redis 快取提供 Redis 伺服器的存取權，該伺服器在 Azure 資料中心所主控的伺服器上執行；它可作為可提供存取控制與安全性的外觀。 您可以使用 Azure 管理入口網站來佈建快取。 入口網站會提供數個預先定義的組態，範圍從執行專用服務的 53GB 快取，用來支援 SSL 通訊 (適用於隱私權) 以及主要/下層複寫搭配 99.9% 可用性的 SLA，到共用硬體上所執行不含複寫 (無可用性保證) 的 250 MB 快取。

您也可以使用 Azure 管理入口網站來設定快取的收回原則，並透過將使用者新增至所提供角色；擁有者、參與者和讀取者來控制快取的存取權。 這些角色會定義成員可以執行的作業。 例如，擁有者角色成員擁有快取 (包含安全性) 及其內容的完整控制權，參與者角色成員則可以讀取和寫入快取中的資訊，而讀取者角色成員僅能從快取擷取資料。

多數管理工作可透過 Azure 管理入口網站來執行，且基於這個理由，許多 Redis 標準版中的可用系統管理命令皆無法使用，包含以程式設計方式修改組態、關閉 Redis 伺服器、設定其他從屬伺服器，或強制將資料儲存到磁碟等功能。

Azure 管理入口網站包含便利的圖形化顯示，可讓您監視快取的效能。 例如，您可以檢視建立的連接數目、執行的要求數目、讀取與寫入的資料量，以及快取命中與快取遺漏的數目。 您可以使用這項資訊來判斷快取的效率，且可視需要切換至不同的組態，或變更收回原則。 此外，如果一個或多個關鍵度量落在預期的範圍之外，您可以建立將電子郵件訊息傳送給系統管理員的警示。 例如，如果快取遺漏數目在最後一小時超過指定的值，則系統管理員可能會收到警告，表示快取可能太小或資料可能太快收回。

您也可以監視 CPU、記憶體和快取的網路使用量。

> [AZURE.NOTE] Azure Redis 快取是純粹用作快取，而非資料庫。 如此一來，它目前不會實作 Redis 持續性。

如需詳細資訊和範例來示範如何建立和設定 Azure Redis 快取，請瀏覽網頁 [奔馳在 Azure Redis 快取](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) Azure 部落格。

## 快取工作階段狀態和 HTML 輸出

如果您建置透過使用 Azure Web 角色來執行的 ASP.NET Web 應用程式，您可以將工作階段狀態資訊和 HTML 輸出儲存在 Azure Redis 快取中。 Azure Redis 快取的工作階段狀態提供者可讓您在 ASP.NET Web 應用程式的不同執行個體之間共用工作階段資訊，並且在用戶端與伺服器親和性無法使用，且快取工作階段記憶體中資料並不適當的 Web 伺服陣列情況下相當實用。

使用工作階段狀態提供者搭配 Azure Redis 快取可提供幾項優點，包含：

- 它可以在 ASP.NET Web 應用程式的大量執行個體之間共用工作階段狀態，並提供改善的延展性，
- 它針對多個讀取者和單一寫入者的相同工作階段狀態資料支援受控制的並行存取權，以及
- 它可以使用壓縮來節省記憶體，並改善網路效能。

如需詳細資訊，請造訪 [Azure Redis 快取的 ASP.NET 工作階段狀態提供者](http://msdn.microsoft.com/library/azure/dn690522.aspx) Microsoft 網站上的頁面。

> [AZURE.NOTE] 請勿在 Azure 環境以外執行的 ASP.NET 應用程式中使用 Azure Redis 快取工作階段狀態提供者。 從 Azure 外部存取快取的延遲會消除快取資料的效能優勢。

同樣地，Azure Redis 快取的輸出快取提供者可讓您儲存透過 ASP.NET Web 應用程式所產生的 HTTP 回應。 使用輸出快取提供者搭配 Azure Redis 快取，可針對呈現複雜 HTML 輸出的應用程式改善回應時間；產生類似回應的應用程式執行個體可以使用快取中的共用輸出片段，而不會重新產生此 HTML 輸出。  如需詳細資訊，請造訪 [Azure Redis 快取的 ASP.NET 輸出快取提供者](http://msdn.microsoft.com/library/azure/dn798898.aspx) Microsoft 網站上的頁面。

## 建置自訂的 Redis 快取

Azure Redis 快取用作基礎 Redis 伺服器的外觀。 目前它支援一組固定的組態，但不提供 Redis 叢集。 如果您需要 Azure Redis 快取未涵蓋的進階組態 (例如大於 53 GB 的快取)，您可以使用 Azure 虛擬機器來建置並主控您自己的 Redis 伺服器。 因為您在想要實作複寫時可能需要建立數個 VM 作為主要和下層節點，這可能會是很複雜的程序。 此外，如果您想要建立叢集，則需要多個主要和下層伺服器；最小叢集、可提供高可用性的複寫拓撲，以及包含至少 6 個 VM 並組織成 3 個成對主要/下層伺服器 (叢集必須包含至少 3 個主要節點) 的延展性。 每個主要/下層配對應彼此鄰近以減少延遲，但如果您想要找出靠近應用程式，且該應用程式最有可能使用的快取資料，每一組配對可以在位於不同區域的不同 Azure 資料中心內執行。 頁面 [在 Azure 的 CentOS Linux VM 上執行 Redis](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) microsoft 網站逐步解說範例，示範如何建立和設定做為 Azure VM 執行的 Redis 節點。

請注意，如果您以這種方式實作自己的 Redis 快取，則必須負責監視、管理和保護服務。

## Redis 快取的資料分割

資料分割快取涉及多部電腦之間的分割快取。 此結構具有數個優點，可使用單一快取伺服器，包含：

- 建立比儲存在單一伺服器上更大的快取。
- 將資料分散到伺服器，並改善可用性。 如果一部伺服器失敗或變成無法存取，則僅其所保存的資料無法使用；剩餘伺服器上的資料仍然可以存取。 至於快取並不重要，因為快取資料僅是暫時性保留在資料庫的資料複本，而變成無法存取伺服器上的快取資料可以改為在不同的伺服器上進行快取。
- 在伺服器之間分配負載，藉此改善效能和延展性。
- 將資料配置在鄰近供使用者存取的地理位置以降低延遲。

至於快取，最常見的資料分割形式是分區化。 在此策略中，每個資料分割 (或分區) 是獨立存在的 Redis 快取。 資料會使用分區化邏輯導向至特定的資料分割，可以使用各種不同的方法來散發資料。  [分區化模式](http://msdn.microsoft.com/library/dn589797.aspx) 提供有關實作分區化的詳細資訊。

若要在 Redis 快取中實作資料分割，您可以採用下列方法其中之一：

- _伺服器端查詢路由。_ 在這項技術，用戶端應用程式將要求傳送到任何
  Redis 快取 （可能是最接近的伺服器） 所組成的伺服器。 每個 Redis 伺服器會儲存
  描述保存，並同時包含資訊的資料分割的中繼資料
  資料分割位於其他伺服器上。 Redis 伺服器會檢查用戶端要求，如果它
  可以在本機解析它將會執行要求的作業，否則它會將轉寄
  要求到適當的伺服器。 這個模型由 Redis 叢集，並
  詳細說明在 [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial) Redis 網站上的頁面。 Redis 叢集
  對用戶端應用程式和其他 Redis 伺服器可以加入至叢集是透明的
  （和重新分割資料） 而不需要重新設定用戶端。

  > [AZURE.IMPORTANT] Azure Redis 快取目前不支援 Redis 叢集。 如果您想要
  實作此方法，那麼您應該建立自訂的 Redis 快取，如先前所述。

- _用戶端資料分割。_ 在此模型中，用戶端應用程式包含的邏輯 （可能是在
  程式庫的形式） 路由要求至適當的 Redis 伺服器。 這種方法
  可以搭配 Azure Redis 快取。建立多個 Azure Redis 快取 （一個用於每個資料
  磁碟分割），並實作將要求路由到正確的用戶端邏輯
  快取。 如果資料分割配置有所變更 （如果已建立其他 Azure Redis 快取，
  例如），用戶端應用程式可能需要重新設定。

- _Proxy 輔助資料分割。_ 在這種配置，用戶端應用程式傳送的要求
  中繼 proxy 服務的了解如何分割資料，然後路由
  適當的 Redis 伺服器的要求。 這個方法也可以搭配 Azure
  Redis 快取。proxy 服務可以實作為 Azure 雲端服務。 此
  方法需要一層額外的複雜性來實作服務，以及
  要求時間可能會比使用用戶端執行。

頁面 [資料分割 ︰ 如何在多個 Redis 執行個體的資料分割](http://redis.io/topics/partitioning)
Redis 網站提供關於使用 Redis 實作資料分割的進一步資訊。

### 實作 Redis 快取用戶端應用程式

Redis 支援以多種程式設計語言撰寫的用戶端應用程式。 如果您要使用.NET Framework 來建置新的應用程式，建議的方法是使用 StackExchange.Redis 用戶端程式庫。 此程式庫會提供.NET Framework 物件模型，用來擷取詳細資料以便連接到 Redis 伺服器連接、傳送命令，以及接收回應。 它是在 Visual Studio 中作為 NuGet 封裝提供使用。 您可以使用這個相同的程式庫來連接至 Azure Redis 快取，或 VM 上所託管的自訂 Redis 快取。

若要連接到 Redis 伺服器，您可以使用 `ConnectionMultiplexer` 類別的靜態 `Connect` 方法。 這個方法建立的連接適用於整個用戶端應用程式的存留期，且相同連接可供多個並行執行緒使用；每次您執行 Redis 操作時請勿重新連接並中斷連接，因為這可能會降低效能。 您可以指定連接參數，例如 Redis 主機的位址和密碼。 如果您使用 Azure Redis 快取，則密碼可能是針對 Azure Redis 快取使用 Azure 管理入口網站所產生的主要或次要索引鍵。

在您已連線到 Redis 伺服器之後，可以在作為快取的 Redis 資料庫上取得控制代碼。 Redis 連線提供 `GetDatabase` 方法來執行這項操作。 您可以使用 `StringGet` 和 `StringSet` 方法，從快取擷取項目並在快取中儲存資料。 這些方法會預期索引鍵作為參數使用，且會在具有相符值的快取中傳回項目 (`StringGet`)，或將項目新增至使用此索引鍵的快取 (`StringSet`)。

根據 Redis 伺服器的位置，許多作業可能會造成一些延遲，而要求會傳輸到伺服器，且回應會傳回給用戶端。 StackExchange 程式庫提供許多方法的非同步版本，它會進行公開以便協助用戶端應用程式保持回應狀態。 這些方法支援 [工作架構非同步模式](http://msdn.microsoft.com/library/hh873175.aspx) .NET Framework 中。

下列程式碼片段會顯示一個稱為 `RetrieveItem` 的方法，並說明根據 Redis 和 StackExchange 程式庫實作另行快取模式的範例。 此方法會接受字串索引值，並透過呼叫 `StringGetAsync` 方法 (`StringGet` 的非同步版本)，嘗試從 Redis 快取中擷取對應的項目。 如果找不到項目，它會使用 `GetItemFromDataSourceAsync` 方法 (這是本機方法並不屬於 StackExchange 程式庫的一部分) 從基礎資料來源擷取項目，然後再使用 `StringSetAsync` 方法新增至快取，以便下一次可以更快速地擷取項目。

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

`StringGet` 和 `StringSet` 方法不會限制要擷取或儲存字串值；他們可以接受任何序列化為位元組陣列的項目。 如果您需要儲存.NET 物件，可以將其序列化為位元組資料流，並使用 StringSet 方法來寫入至快取。 同樣地，您可以使用 StringGet 方法，從快取中讀取物件，並將其還原序列化為.NET 物件。 下列程式碼顯示一組的 IDatabase 介面的擴充方法 (Redis 連線的 GetDatabase 方法會傳回 `IDatabase` 物件)，並使用這些方法來讀取和寫入 BlogPost 物件至快取的一些範例程式碼 ︰

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

下列程式碼將說明一個稱為 `RetrieveBlogPost` 的方法，該方法會使用這些擴充方法，遵循另行快取模式來讀取和寫入序列化 `BlogPost` 物件至快取：

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

頁面 [開發 Azure Redis 快取](http://msdn.microsoft.com/library/azure/dn690520.aspx) Microsoft 網站提供有關如何撰寫可以使用 Azure Redis 快取的用戶端應用程式的詳細資訊。 額外的資訊位於 [基本使用方式頁面](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) StackExchange.Redis 網站，和 [管線與多工器](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) 相同網站上提供非同步作業和流水線搭配 Redis 與 StackExchange 程式庫的詳細資訊。  本指引中稍後＜Redis 快取的使用案例＞一節，提供一些更進階的技術範例，以便您套用至保留在 Redis 快取中的資料。

## Redis 快取的使用案例

最簡單的使用 Redis 快取涉及儲存值的位置是未解譯的字串可以包含任何二進位資料的任意長度的索引鍵/值組 （它是基本上可以視為字串的位元組陣列）。 這種案例即之前在本指引中的＜實作 Redis 快取用戶端應用程式＞一節所說明的情況。 您應該注意索引鍵也包含未解譯的資料，因此雖然可以使用任何二進位資訊作為索引鍵，但愈長的索引鍵，儲存也花費愈多的空間，且執行查閱作業的時間也會愈久。 如需可用性和維護的方便性，請仔細設計您的 keyspace 並使用有意義 (但非詳細資訊) 的索引鍵。 例如，使用結構化的索引鍵，像是 "customer:100" 來表示客戶識別碼為 100 的索引鍵，而非僅以 "100" 表示。 此配置可讓您輕鬆區別儲存不同資料類型之間的值。 例如，您也可以使用索引鍵 "orders:100" 來表示順序識別碼為 100 的索引鍵。

除了一維的二進位字串，Redis 索引鍵/值配對中的值也可以保留更結構化資訊，包含清單、集合 (已排序和未排序)，以及雜湊。 Redis 提供一組完整的命令集，可處理這些類型，且這些命令當中有許多可透過例如 StackExchange 的用戶端程式庫，使用於 .NET Framework 應用程式。 頁面 [Redis 資料類型和抽象概念簡介](http://redis.io/topics/data-types-intro) Redis 網站提供這些類型和可用來操作這些命令的詳細的概觀。

本節將針對這些資料類型和命令摘要出一些常用的案例。

### 執行不可部分完成的作業和批次作業

Redis 支援在字串值上進行一系列不可部分完成的取得和設定作業。 這些作業會移除使用個別 `GET` 和 `SET` 命令時可能會發生的競爭危險。 可用的作業包含：

- `INCR`, `INCRBY`, ，`DECR`, ，和 `DECRBY` 其上執行不可部分完成的遞增和遞減作業
  整數數字資料值。 StackExchange 程式庫提供的多載的版本
  `IDatabase.StringIncrementAsync` 和 `IDatabase.StringDecrementAsync` 方法來執行
  這些作業和傳回結果值會儲存在快取。 下列程式碼會
  程式碼片段將說明如何使用這些方法 ︰

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

- `GETSET` 用來擷取具有與索引鍵相關聯的值，並會將其變更為新值。 Auch die Eigenschaften
  StackExchange 程式庫，讓這項作業可透過 `IDatabase.StringGetSetAsync`
  方法將程式碼部署至服務。 下方的程式碼片段會顯示這個方法的範例。 此程式碼會傳回目前
  值"資料 ︰ counter"上例中索引鍵相關聯，並且重設值
  這個金鑰設回零，做為在相同的作業 ︰

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` 和 `MSET` 可以作為單一作業傳回或變更一組字串值。 Auch die Eigenschaften
  `IDatabase.StringGetAsync` 和 `IDatabase.StringSetAsync` 以支援多載方法
  這項功能，如下列範例所示 ︰

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

您也可以將多項作業結合到單一 Redis 交易，如同本指引的＜Redis 交易與批次＞一節中所述。 StackExchange 程式庫可透過 `ITransaction` 介面提供交易支援。 您可以使用 IDatabase.CreateTransaction 方法來建立一個 ITransaction 物件，並使用 `ITransaction` 物件所提供的方法叫用交易的命令。 `ITransaction` 介面提供一組如同 `IDatabase` 介面的類似方法，但不包含所有非同步的方法；它們僅在叫用 `ITransaction.Execute` 方法時執行。 Execute 方法所傳回的值表示是否已成功建立交易 (true) 或建立失敗 (false)。

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

如需詳細資訊，請參閱 [Redis 中的交易](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) StackExchange.Redis 網站上的頁面。

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

當您在 Redis 快取中儲存項目時，您可以指定逾時，而在指定時間之後，項目會自動從快取中移除。 您也可以在金鑰到期之前，使用 `TTL` 命令來查詢剩餘時間；此命令可透過使用 IDatabase.KeyTimeToLive 方法而用於 StackExchange 應用程式。

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

> _提示 ︰_ 您可以手動移除項目從快取使用 DEL 命令，可透過 StackExchange 程式庫中作為 IDatabase.KeyDeleteAsync 方法。

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

許多應用程式所需的常見問題是尋找最近存取過的項目。 例如，部落格網站可能會想要顯示最近讀取過部落格文章的相關資訊。 您可以使用 Redis 清單來實作這項功能。 Redis 清單包含多個共用相同金鑰的項目，但清單會用作雙端的佇列。 您可以使用 LPUSH (推播至左) 和 RPUSH (推播至右) 命令將項目推播至清單兩端。 您可以使用 LPOP 和 RPOP 命令，從清單的任一端擷取項目。  您也可以使用 LRANGE 和 RRANGE 命令，以傳回一組元素。 下列程式碼片段顯示如何使用 StackExchange 程式庫來執行這些作業。 此程式碼使用先前範例中的 BlogPost 類型。 當使用者讀取部落格文章時，系統會透過使用 IDatabase.ListLeftPushAsync 方法，將部落格文章的標題推播至與 Redis 快取中索引鍵 "blog:recent_posts" 相關聯的清單：

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

> [AZURE.NOTE] StackExchange 程式庫也會提供 IDatabase.SortedSetRangeByRankAsync 方法，以分數順序傳回的資料，但不會傳回分數。

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
- 依預設，訂閱者會根據傳送的順序來接收訊息。 大量的高度活躍系統中
  訊息和許多 「 訂閱者 」 和 「 發行者 」，保證循序傳遞訊息可能會降低系統效能。 If
  每個訊息都是獨立且順序並不重要您可以為了 Redis 系統啟用並行處理
  改善回應性。 您可以達到此目的在 StackExchange 用戶端，藉由設定所使用的連接 PreserveAsyncOrder
  「 訂閱者 」 為 false:
  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## 相關的模式和指引

在您的應用程式中實作快取時，下列模式也可能與您的案例相關：

- [另行快取模式](http://msdn.microsoft.com/library/dn589799.aspx)︰ 這個模式描述如何依需要將資料載入資料存放區中的快取。 此模式也有助於針對快取中所保留的資料與原始資料存放區中的資料之間維護一致性。
-  [分區化模式](http://msdn.microsoft.com/library/dn589797.aspx) 提供實作水平資料分割，以協助改善延展性，當儲存和存取大量資料的資訊。

## 相關資訊

-  [MemoryCache 類別](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) Microsoft 網站上的頁面。
-  [Microsoft Azure 快取](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) Microsoft 網站上的頁面。
-  [哪個 Azure 快取服務是最適合我？](http://msdn.microsoft.com/library/azure/dn766201.aspx) Microsoft 網站上的頁面。
-  [組態模型](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) Microsoft 網站上的頁面。
-  [工作架構非同步模式](http://msdn.microsoft.com/library/hh873175.aspx) Microsoft 網站上的頁面。
-  [管線與多工器](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) StackExchange.Redis GitHub 儲存機制上的頁面。
-  [Redis 持續性](http://redis.io/topics/persistence) Redis 網站上的頁面。
-  [複寫頁面](http://redis.io/topics/replication) Redis 網站上。
-  [Redis 叢集教學課程](http://redis.io/topics/cluster-tutorial) Redis 網站上的頁面。
-  [資料分割 ︰ 如何在多個 Redis 執行個體的資料分割](http://redis.io/topics/partitioning) Redis 網站上的頁面。
- 頁面 [使用 Redis 作為 LRU 快取](http://redis.io/topics/lru-cache) Redis 網站上。
-  [交易頁面](http://redis.io/topics/transactions) Redis 網站上。
-  [Redis 安全性](http://redis.io/topics/security) Redis 網站上的頁面。
- 頁面 [奔馳在 Azure Redis 快取](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) Azure 部落格。
- 頁面 [CentOS Linux VM 上執行 Redis](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) Microsoft 網站上的 Azure 中。
-  [Azure Redis 快取的 ASP.NET 工作階段狀態提供者](http://msdn.microsoft.com/library/azure/dn690522.aspx) Microsoft 網站上的頁面。
-  [Azure Redis 快取的 ASP.NET 輸出快取提供者](http://msdn.microsoft.com/library/azure/dn798898.aspx) Microsoft 網站上的頁面。
- 頁面 [開發 Azure Redis 快取](http://msdn.microsoft.com/library/azure/dn690520.aspx) Azure 網站上。
- 頁面 [Redis 資料類型和抽象概念簡介](http://redis.io/topics/data-types-intro) Redis 網站上。
-  [基本使用方式](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) StackExchange.Redis 網站上的頁面。
-  [Redis 中的交易](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) StackExchange.Redis 儲存機制上的頁面。
-  [資料分割指南](http://msdn.microsoft.com/library/dn589795.aspx) Microsoft 網站上。


