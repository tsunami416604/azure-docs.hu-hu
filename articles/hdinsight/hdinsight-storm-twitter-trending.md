<properties
   pageTitle="Twitter 趨勢主題與 Apache Storm on HDInsight | Microsoft Azure"
   description="了解如何使用 Trident 建立 Apache Storm 拓撲，藉此根據 # 標籤判斷 Twitter 上的趨勢主題。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#使用 Apache Storm on HDInsight 判斷 Twitter 的趨勢主題

了解如何使用 Trident 建立 Storm 拓撲，藉此判斷 Twitter 上的趨勢主題 (# 標籤)。

Trident 是一種高階抽象概念，可提供聯結、彙總、分組、函數和篩選等工具。 此外，Trident 還會新增原型，以執行可設定狀態的增量處理。 此範例會示範如何使用自訂 Spout、函數和 Trident 所提供的數個內建函數來建置拓撲。

> [AZURE.NOTE] 這個範例主要根據 [Trident Storm](https://github.com/jalonsoramos/trident-storm) Juan Alonso 的範例。

##需求

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java 和 JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Twitter 開發人員帳戶

##下載專案

使用以下程式碼在本機上複製專案。

    git clone https://github.com/Blackmist/TwitterTrending

##拓撲

此範例的拓撲如下：

![拓撲](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] 這是拓撲的簡化檢視。 元件的多個執行個體會分散到叢集中的節點。

實作拓撲的 Trident 程式碼如下：

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

此程式碼會執行以下動作：

1. 從 Spout 建立新的資料流。 Spout 會擷取 Twitter 中的推文，然後篩選特定關鍵字 (在此範例中，為 love、music 和 coffee)。

2. 會使用自訂函數 HashtagExtractor 擷取每則推文中的 # 標籤。 這些會發出至資料流。

3. 資料流接著會依 # 標籤進行分組，然後傳遞給彙總工具。 此彙總工具會建立每個 # 標籤出現次數的計數， 計數資料會保存在記憶體中。 最後，會發出含有 # 標籤和計數的新資料流。

4. 因為我們只在某一批推文，最受歡迎的雜湊標記感興趣 **FirstN** 組件適用於僅傳回前 10 個值，根據計數欄位。

> [AZURE.NOTE] 而非 spout 和 HashtagExtractor，我們會使用內建 Trident 功能。
>
> 如需關於內建作業的資訊，請參閱 <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">封裝 storm.trident.operation.builtin</a>。
>
> 如需非 MemoryMapState 的 Trident-state 實作，請參閱下列各項：
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Storm Trident 彈性搜尋</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">trident-redis</a>

###Spout

Spout **TwitterSpout**, ，會使用 <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> 從 Twitter 擷取推文。 接著會建立篩選 (在此範例中為 love、music 和 coffee)，然後將符合篩選的傳入推文 (status) 儲存在連結的 Blocking Queue 中。 (如需詳細資訊，請參閱 <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank"> LinkedBlockingQueue</a>)。最後，會將項目拉出佇列，然後發出至拓撲。

###HashtagExtractor

若要擷取 # 標籤，可以使用 <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> 來擷取推文中包含的所有 # 標籤。 之後，會將這些發出至資料流。

##啟用 Twitter

使用以下步驟註冊新的 Twitter 應用程式，然後取得讀取 Twitter 內容所需的取用者和存取權杖資訊。

1. 移至 <a href="https://apps.twitter.com" target="_blank">Twitter 應用程式</a> 按一下 **建立新的應用程式** ] 按鈕。 在表單中填滿時保留 **回呼 URL** 欄位空白。

2. 建立應用程式時，按一下 [ **金鑰和存取權杖** ] 索引標籤。

3. 複製 **消費者金鑰** 和 **消費者密鑰** 資訊。

4. 在頁面的底部，選取 **建立我的存取權杖** 如果沒有權杖。 建立權杖後，複製 **存取權杖** 和 **存取權杖密碼** 資訊。

5. 在 **TwitterSpoutTopology** 先前複製，開啟的專案 **resources/twitter4j.properties** 檔案中，在先前步驟中，新增您所收集的資訊並儲存檔案。

##建置拓撲

請使用以下程式碼建置專案：

        cd [directoryname]
        mvn compile

##測試拓撲

請使用以下命令在本機上測試拓撲：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

拓撲啟動後，您應該會看到含有拓撲所發出之 # 標籤和計數的偵錯資訊。 輸出應該如下所示：

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##後續步驟

既然您已經測試在本機的拓撲、 了解如何部署拓撲 ︰ [部署和管理 HDInsight 上的 Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology.md)。

您也可能會對下列 Storm 主題感興趣：

* [使用 Maven 開發 Storm on HDInsight 的 Java 拓撲](hdinsight-storm-develop-java-topology.md)

* [使用 Visual Studio 開發 Storm on HDInsight 的 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)

如需 HDinsight 的 Storm 範例：

* [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)


