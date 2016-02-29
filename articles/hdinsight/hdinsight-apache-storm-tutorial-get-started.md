<properties
    pageTitle="Apache Storm 教學課程：開始使用 Storm | Microsoft Azure"
    description="在 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。 了解如何使用 Storm 即時處理資料。"
    keywords="apache storm,apache storm tutorial,big data analytics,storm starter"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# Apache Storm 教學課程：在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。 在 Microsoft Azure HDInsight 的 Storm 中，您可以建立雲端式 Storm 叢集，來執行即時的巨量資料分析。 

> [AZURE.NOTE] 這篇文章中的步驟建立以 Windows 為基礎的 HDInsight 叢集。 如需建立以 Linux 為基礎的 Storm on HDInsight 叢集的步驟，請參閱 [Apache Storm 教學課程: 開始使用 HDInsight 上使用資料分析的 Storm Starter 範例](hdinsight-apache-storm-tutorial-get-started-linux.md)

## 先決條件

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## 建立 Storm 叢集

Storm on HDInsight 使用 Azure Blob 儲存體來儲存提交給叢集的記錄檔和拓撲。 請使用以下步驟，來建立可與您的叢集搭配使用的 Azure 儲存體帳戶：

1. 登入 [Azure 入口網站][preview-portal]。

2. 選取 **新增**, ，請選取 __資料分析__, ，然後選取 __HDInsight__。

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-apache-storm-tutorial-get-started/new-cluster.png)

3. 輸入 __叢集名稱__, ，然後選取 __Storm__ 的 __叢集類型__。 旁邊會出現綠色的核取 __叢集名稱__ 是否可用。

    ![叢集名稱、叢集類型及 OS 類型](./media/hdinsight-apache-storm-tutorial-get-started/clustername.png)

4. 如果您有一個以上的訂閱，請選取 __訂閱__ 選取將用於叢集的 Azure 訂閱的項目。

5. 如 __資源群組__, ，您可以選取的項目，查看現有的資源群組的清單，然後選取 [建立叢集的一個。 您可以選取或者 __新建__ ，然後輸入新的資源群組的名稱。 出現綠色勾號即表示新群組的名稱可供使用。

    > [AZURE.NOTE] 如果有的話，這個項目會預設為其中一個現有的資源群組。

6. 選取 __認證__, ，然後輸入 __叢集登入使用者名稱__ 和 __叢集登入密碼__。 最後，使用  __選取__ 設定的認證。 本文件中不會使用遠端桌面，所以您可以將其停用。

    ![叢集認證刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/clustercredentials.png)

6. 如 __資料來源__, ，您可以選取的項目，選擇現有的資料來源，或建立一個新。

    ![資料來源刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/datasource.png)

    您目前可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。 了解這些項目上使用下列 __資料來源__ 刀鋒視窗。

    - __選取方法__: 設為 __從所有的訂閱__ 才能瀏覽儲存體帳戶，您的訂閱。 設定為 __便捷鍵__ 如果您想要輸入 __儲存體名稱__ 和 __便捷鍵__ 的現有儲存體帳戶。

    - __建立新__: 使用此選項可建立新的儲存體帳戶。 使用出現的欄位輸入儲存體帳戶名稱。 如果該名稱可供使用，就會出現綠色勾號。

    - __選擇預設容器__: 使用此選項可輸入要用於叢集的預設容器名稱。 雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。

    - __位置__: 儲存體帳戶的地理區域中，或將中建立。

        > [AZURE.IMPORTANT] 選取的預設資料來源的位置也會將設定 HDInsight 叢集的位置。 叢集和預設資料來源必須位於相同區域中。

    - __選取__: 使用此選項可將儲存的資料來源的設定。

7. 選取 __節點定價層__ 顯示將建立此叢集節點的相關資訊。 根據預設，背景工作節點數目設定為 __4__。 請設為 __1__, ，因為這是本教學課程，可減少叢集的成本。 該叢集的預估成本將顯示於此刀鋒視窗的底部。

    ![節點定價層刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/nodepricingtiers.png)

    使用  __選取__ 儲存 __節點定價層__ 資訊。

8. 選取 __選擇性組態__。 此刀鋒視窗可讓您選取叢集的版本，以及設定其他選擇性設定，例如聯結 __虛擬網路__ 」 或者設定 __外部中繼存放區__ 要保留 Hive 和 Oozie 的資料。

    ![選用設定刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started/optionalconfiguration.png)

9. 請確認 __釘選到開始面板__ 已選取，然後選取 __建立__。 這會建立叢集，並將該叢集磚加入到您 Azure 入口網站的開始面板。 該圖示表示該叢集正在佈建中，並會在佈建完成之後變更為顯示 HDInsight 圖示。

    | 佈建期間 | 佈建完成 |
    | ------------------ | --------------------- |
    | ![在開始面板上佈建指示器](./media/hdinsight-apache-storm-tutorial-get-started/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-storm-tutorial-get-started/provisioned.png) |

    > [AZURE.NOTE] 這需要一些時間，叢集才會建立，通常約 15 分鐘的時間。 在開始面板中，使用 [] 磚或 __通知__ 左邊頁面上，若要檢查佈建程序的項目。

## 在 HDInsight 上執行 Storm Starter 範例

本 Apache Storm 教學課程會向您介紹在 GitHub 上使用 Storm Starter 範例的巨量資料分析。

每個 Storm on HDInsight 叢集都會隨附一個 Storm 儀表板，讓您將 Storm 拓撲上傳到叢集並在其中執行 Storm 拓撲。 每個叢集也會隨附可直接從 Storm 儀表板執行的範例拓撲。

### <a id="connect"></a>連接至儀表板

儀表板位於 **https://&lt;clustername>.azurehdinsight.net//**, ，其中 **clustername** 是叢集的名稱。 您也可以找到儀表板的連結，從 [開始面板] 中選取叢集，然後選取 __儀表板__ 分頁頂端的連結。

![含 Storm 儀表板連結的 Azure 入口網站](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [AZURE.NOTE] 連接至儀表板時，系統會提示您輸入使用者名稱和密碼。 這是系統管理員名稱 (**admin**) 和建立叢集時所使用的密碼。

載入 Storm 儀表板後，您會看到 **提交拓樸** 表單。

![利用 Storm 儀表板提交 Storm Starter 拓撲。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

 **提交拓樸** 表單可用來上傳並執行包含 Storm 拓撲的.jar 檔案。 它也包含數個與叢集一併提供的基本範例。

### <a id="run"></a>從 GitHub 的 Storm Starter 專案執行 word-count 範例

與叢集一併提供的範例包含 word-counting 拓撲的數個變體。 這些範例包含 **spout** 隨機產生句子，並 **bolt** 每個句子拆成個別單字，然後計算每個單字的出現的次數。 這些範例均來自 [Storm Starter 範例](https://github.com/apache/storm/tree/master/examples/storm-starter) (Apache Storm 的一部分)。

請使用以下步驟執行 Storm Starter 範例：

1. 選取 **StormStarter-WordCount** 從 **Jar 檔案** 下拉式清單。 這會填入 **類別名稱** 和 **其他參數** 欄位使用這個範例中的參數。

    ![在 Storm 儀表板上選取的 Storm Starter WordCount。](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

    * **類別名稱** -提交拓撲的.jar 檔案中的類別。
    * **其他參數** -拓撲所需的任何參數。 在此範例中，此欄位用於讓提交的拓撲有易記名稱。

2. 按一下 [  **提交**。 隨後， **結果** 欄位會顯示用來提交工作，如命令結果的命令。  **錯誤** 欄位會顯示提交拓撲的任何錯誤發生。

    ![Storm Starter WordCount 的提交按鈕和結果。](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)

    > [AZURE.NOTE] 結果並不表示，已完成的拓樸- **Storm 拓撲一旦啟動，會執行，直到您將它停止。**Word-count 拓撲會產生隨機的句子，並持續計算每個單字出現的次數，直到您停止拓撲為止。

### <a id="monitor"></a>監視拓撲

您可以使用 Storm UI 監視拓撲。

1. 選取 **Storm UI** 從 Storm 儀表板的頂端。 這會顯示叢集和所有執行中拓撲的摘要資訊。

    ![顯示 Storm Starter WordCount 拓樸摘要的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)

    在上方的頁面中，您會看到拓撲作用中的時間，以及使用的背景工作數、執行程式數和工作數。

    > [AZURE.NOTE]  **名稱** 資料行包含透過稍早提供的易記名稱 **其他參數** 欄位。

4. 在 **拓樸摘要**, ，請選取 **wordcount** 中的項目 **名稱** 資料行。 這會顯示拓撲的詳細資訊。

    ![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)

    此頁面提供以下資訊：

    * **拓撲統計資料** -拓撲效能的基本資訊整理為時間範圍。

        > [AZURE.NOTE] 選取特定時段變更網頁上的其他區段中顯示之資訊的時間間隔。

    * **Spout** -spout，包括每個 spout 傳回的最後一個錯誤的基本資訊。

    * **Bolt** -bolt 的基本資訊。

    * **拓撲組態** -拓撲組態有關的詳細資訊。

    此頁面也提供可對拓撲採取的動作：

    * **啟動** -繼續處理的已停用的拓撲。

    * **停用** -暫停執行中拓撲。

    * **重新平衡** -調整拓撲的平行處理原則。 變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。 這可讓拓撲調整平行處理原則，以彌補叢集中增加/減少的節點數目。 如需詳細資訊，請參閱[了解 Storm 拓撲的平行處理原則](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    * **Kill** -指定的逾時之後終止 Storm 拓撲。

5. 從這個頁面上，選取一個項目 **Spout** 或 **Bolt** 一節。 這會顯示所選元件的相關資訊。

    ![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)

    此頁面會顯示以下資訊：

    * **Spout/Bolt 統計資料** -元件效能的基本資訊整理為時間範圍。

        > [AZURE.NOTE] 選取特定時段變更網頁上的其他區段中顯示之資訊的時間間隔。

    * **輸入統計資料** (僅限 bolt)-關於產生 bolt 所使用的資料之元件的資訊。

    * **輸出統計資料** -此 bolt 發出之資料的資訊。

    * **執行程式** -這個元件的執行個體的詳細資訊。

    * **錯誤** -此元件產生的錯誤。

5. 當您檢視 spout 或 bolt 的詳細資料，選取一個項目 **連接埠** 中的資料行 **執行程式** 區段，以檢視詳細資料之元件的特定執行個體。

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    這項資料，您可以看到 word **七個** 發生 1,493,957 的時間。 這就是啟動拓撲後，該字所出現的次數。

### 停止拓撲

返回 **拓樸摘要** 字數統計拓撲，頁面上，然後選取 **Kill** 從 **拓撲動作** 一節。 出現提示時，請先輸入要等候 10 秒，再停止拓撲。 逾時期限過後，拓撲就不會顯示當您瀏覽 **Storm UI** 儀表板] 區段。

## 摘要

您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。

## <a id="next"></a>後續步驟

* **HDInsight Tools for Visual Studio** -HDInsight Tools 可讓您使用 Visual Studio 提交、 監視及管理 Storm 拓撲類似稍早提到的 Storm 儀表板。 HDInsight Tools 也能讓您建立 C# Storm 拓撲，並提供可讓您在叢集上部署和執行的範例拓撲。

    如需詳細資訊，請參閱[開始使用 HDInsight Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)。

* **範例檔案** -HDInsight Storm 叢集也提供數個範例中的 **%STORM_HOME%\contrib** 目錄。 每個範例應該會包含下列項目：

    * 原始程式碼 - 例如，storm-starter-0.9.1.2.1.5.0-2057-sources.jar

    * Java 文件 - 例如，storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

    * 範例 - 例如，storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

    請使用 'jar' 命令來解壓縮原始程式碼或 Java 文件。 例如，'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'。

    > [AZURE.NOTE] Java 文件由網頁組成。 解壓縮之後，請使用瀏覽器來檢視 **index.html** 檔案。

    若要存取這些範例，您必須為 Storm on HDInsight 叢集啟用遠端桌面，並複製檔案從 **%STORM_HOME%\contrib**。

* 以下文件含有可和 Storm on HDInsight 搭配使用的其他範例清單：

    * [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

