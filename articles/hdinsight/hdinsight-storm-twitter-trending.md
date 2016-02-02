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


# 使用 Apache Storm on HDInsight 判斷 Twitter 的趨勢主題

了解如何使用 Trident 建立 Storm 拓撲，藉此判斷 Twitter 上的趨勢主題 (# 標籤)。

Trident 是一種高階抽象概念，可提供聯結、彙總、分組、函數和篩選等工具。 此外，Trident 還會新增原型，以執行可設定狀態的增量處理。 此範例會示範如何使用自訂 Spout、函數和 Trident 所提供的數個內建函數來建置拓撲。
> [AZURE.NOTE] 

## 需求

* 

* 

* 

* Twitter 開發人員帳戶

## 下載專案

使用以下程式碼在本機上複製專案。

    git clone https://github.com/Blackmist/TwitterTrending

## 拓撲

此範例的拓撲如下：

![拓撲](./media/hdinsight-storm-twitter-trending/trident.png)
> [AZURE.NOTE] 這是簡化的拓撲樣貌。 元件的多個執行個體會分散到叢集中的節點。

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

4. 因為我們只對指定的這批推文中最受歡迎的 # 標籤感興趣，所以會套用 **FirstN** 組件，並根據計數欄位，僅傳回前 10 個值。

> [AZURE.NOTE] 我們會使用內建 Trident 功能，而非 Spout 和 HashtagExtractor。
>
> 
>
> 如需非 MemoryMapState 的 Trident-state 實作，請參閱下列各項：
>
> 
>
> 

### Spout

接著會建立篩選 (在此範例中為 love、music 和 coffee)，然後將符合篩選的傳入推文 (status) 儲存在連結的 Blocking Queue 中。  最後，會將項目拉出佇列，然後發出至拓撲。

### HashtagExtractor

之後，會將這些發出至資料流。

## 啟用 Twitter

使用以下步驟註冊新的 Twitter 應用程式，然後取得讀取 Twitter 內容所需的取用者和存取權杖資訊。

1. 填寫表單時，請將 [回呼 URL]**** 欄位保留空白。

2. 建立應用程式後，按一下 [金鑰和存取權杖]**** 索引標籤。

3. 複製 [取用者金鑰]**** 和 [取用者密碼]**** 的資訊。

4. 如果沒有權杖，請在頁面底部選取 [建立我的存取權杖]****。 建立權杖後，複製 [存取權杖]**** 和 [存取權杖密碼]**** 的資訊。

5. 在先前複製的 **TwitterSpoutTopology** 專案中，開啟 **resources/twitter4j.properties** 檔案，並新增先前步驟中所收集的資訊，然後儲存檔案。

## 建置拓撲

請使用以下程式碼建置專案：

        cd [directoryname]
        mvn compile

## 測試拓撲

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

## 後續步驟



您也可能會對下列 Storm 主題感興趣：

* 

* 

如需 HDinsight 的 Storm 範例：

* 





