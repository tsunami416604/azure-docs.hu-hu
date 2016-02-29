<properties
    pageTitle="Apache Storm 教學課程：在 HDInsight 上開始使用 Linux 架構的 Storm | Microsoft Azure"
    description="在 Linux 架構的 HDInsight 上使用 Apache Storm 和 Storm Starter 範例，開始分析巨量資料。 了解如何使用 Storm 即時處理資料。"
    keywords="apache storm,apache storm tutorial,big data analytics,storm starter"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# Apache Storm 教學課程：在 HDInsight 上使用 Storm Starter 範例開始分析巨量資料

Apache Storm 是一個可處理資料串流的分散式、容錯、即時的運算系統。 在 Storm on Azure HDInsight 中，您可以建立雲端式 Storm 叢集，以執行即時的巨量資料分析。

> [AZURE.NOTE] 這篇文章中的步驟建立以 Linux 為基礎的 HDInsight 叢集。 如需建立 Windows 架構的 Storm on HDInsight 叢集的步驟，請參閱 [Apache Storm 教學課程: 開始使用 HDInsight 上使用資料分析的 Storm Starter 範例](hdinsight-apache-storm-tutorial-get-started.md)

## 開始之前

您必須具備下列先決條件，才能順利完成本 Apache Storm 教學課程：

- **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **熟悉 SSH 和 SCP**。 如需搭配 HDInsight 使用 SSH 和 SCP 的詳細資訊，請參閱下列文章：

    - **Linux、 Unix 或 OS X 用戶端**: 請參閱 [使用 SSH 與以 Linux 為基礎從 Linux、 Unix 或 OS X 在 HDInsight 上的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    - **Windows 用戶端**: 請參閱 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Windows 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-windows.md)

## 建立 Storm 叢集

Storm on HDInsight 使用 Azure Blob 儲存體來儲存提交給叢集的記錄檔和拓撲。 請使用以下步驟建立和您的叢集搭配使用的 Azure 儲存體帳戶：

1. 登入 [Azure 入口網站][preview-portal]。

2. 選取 **新增**, ，請選取 __資料分析__, ，然後選取 __HDInsight__

    ![在 Azure 入口網站中建立新的叢集](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. 輸入 __叢集名稱__, ，然後選取 __Storm__ 的 __叢集類型__。 旁邊就會出現綠色的核取 __叢集名稱__ 是否可用。

    ![叢集名稱、叢集類型及 OS 類型](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

    選取 __Ubuntu__ 來建立以 Linux 為基礎的 HDInsight 叢集。
    
4. 如果您有一個以上的訂閱，請選取 __訂閱__ 選取將用於叢集的 Azure 訂閱的項目。

5. 如 __資源群組__, ，您可以選取的項目，查看現有的資源群組的清單，然後選取 [建立叢集的一個。 您可以選取或者 __新建__ ，然後輸入新的資源群組的名稱。 出現綠色核取記號即表示新群組的名稱可用。

    > [AZURE.NOTE] 如果有的話，這個項目將會預設為其中一個現有的資源群組。

6. 選取 __認證__, ，然後輸入 __叢集登入密碼__ 的 __叢集登入使用者名稱__。 您也必須輸入 __SSH 使用者名稱__ 和 __密碼__ 或 __公開金鑰__, ，這將用來驗證 SSH 使用者。 最後，使用 __選取__ 按鈕，以設定認證。

    ![叢集認證刀鋒視窗](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

    如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md) 

6. 如 __資料來源__, ，您可以選取的項目，選擇現有的資料來源，或建立一個新。

    ![資料來源刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
    
    目前您可以選取 Azure 儲存體帳戶做為 HDInsight 叢集資料來源。 了解這些項目上使用下列 __資料來源__ 刀鋒視窗。
    
    - __選取方法__: 設為 __從所有的訂閱__ 才能瀏覽儲存體帳戶，您的訂閱。 設定為 __便捷鍵__ 如果您想要輸入 __儲存體名稱__ 和 __便捷鍵__ 的現有儲存體帳戶。
    
    - __建立新__: 使用此選項可建立新的儲存體帳戶。 使用出現的欄位輸入儲存體帳戶名稱。 如果該名稱可用，將會出現綠色核取記號。
    
    - __選擇預設容器__: 使用此選項可輸入要用於叢集的預設容器名稱。 雖然您可以輸入任何名稱，但我們建議您使用與叢集相同的名稱，以便輕易辨識用於這個特定叢集的容器。
    
    - __位置__: 儲存體帳戶的地理區域中，或將中建立。
    
        > [AZURE.IMPORTANT] 選取的預設資料來源的位置也會設定 HDInsight 叢集的位置。 叢集和預設資料來源必須位於相同區域中。
        
    - __選取__: 使用此選項可將儲存的資料來源的設定。
    
7. 選取 __節點定價層__ 顯示將建立此叢集節點的相關資訊。 根據預設，背景工作節點數會設定為 __4__。 該叢集的預估成本將會顯示在此刀鋒視窗的底部。

    ![節點定價層刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
    
    使用 __選取__ ] 按鈕以儲存 __節點定價層__ 資訊。

8. 選取 __選擇性組態__。 此刀鋒視窗可讓您選取叢集的版本，以及設定其他選擇性設定，例如聯結 __虛擬網路__ 」 或者設定 __自訂 Metastore__ 要保留 Hive 和 Oozie 的資料。

    ![選用設定刀鋒視窗](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)

9. 請確認 __釘選到開始面板__ 已選取，然後選取 __建立__。 這將會建立叢集，並將該叢集磚加入到您 Azure 入口網站的「開始面板」。 該圖示可表示該叢集正在佈建，並將在佈建完成後變更為 HDInsight 圖示。

    | 佈建期間 | 佈建完成 |
    | ------------------ | --------------------- |
    | ![「開始面板」上的佈建指示器](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![佈建的叢集磚](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

    > [AZURE.NOTE] 需要花一些時間，叢集才會建立，通常約 15 分鐘的時間。 在開始面板中，使用 [] 磚或 __通知__ 佈建程序檢查頁面左邊的項目。

##在 HDInsight 上執行 Storm Starter 範例

 [Storm starter](https://github.com/apache/storm/tree/master/examples/storm-starter) 範例包含 HDInsight 叢集上。 在下列步驟中，您將執行 WordCount 範例。

1. 使用 SSH 連線到 HDInsight 叢集

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    如果您已經使用密碼保護您 SSH 使用者帳戶的安全，系統會提示您輸入密碼。 如果您使用的是公開金鑰，您可能必須使用 `-i` 參數來指定對應的私密金鑰。 例如，`ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`。
        
    如需搭配使用 SSH 與以 Linux 為基礎的 HDInsight 的詳細資訊，請參閱下列文章：
    
    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows)

2. 使用下列命令以啟動範例拓撲：

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
        
    > [AZURE.NOTE]  `0.9.3.2.2.4.9-1` HDinsight Storm 的較新版本更新時，可能會變更檔案名稱的一部分。

    這會在叢集上使用 'wordcount' 的易記名稱，啟動範例 WordCount 拓撲。 命令會隨機產生句子，並計算句子中每個字詞的出現次數。

    > [AZURE.NOTE] 當提交至叢集的拓撲，您必須先將 jar 檔案，內含該叢集，才能使用 `storm` 命令。 而您可以從檔案所在的用戶端使用 `scp` 命令來完成這個動作。 例如，`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > WordCount 範例和其他 Storm 入門範例都已經包含在叢集中，位置是 `/usr/hdp/current/storm-client/contrib/storm-starter/`。

##監視拓撲

Storm UI 提供 Web 介面來處理執行中的拓撲，包含在您的 HDInsight 叢集中。

> [AZURE.IMPORTANT] Storm UI 不網際網路上公開可用，而且必須使用到 HDInsight 叢集前端節點的 SSH 通道來存取。 如需詳細資訊，請參閱 [使用 SSH 通道存取 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

使用下列步驟來檢視 Storm UI：

1. 一旦您建立叢集的 SSH 通道，開啟網頁瀏覽器 https://CLUSTERNAME.azurehdinsight.net，其中 __CLUSTERNAME__ 是叢集的名稱。 這會開啟 Ambari Web UI。

    > [AZURE.NOTE] 如果系統要求您提供使用者名稱和密碼，請輸入叢集系統管理員 (管理員) 和建立叢集時使用的密碼。 系統可能會要求驗證兩次，一次是來自瀏覽器，第二次是來自 Ambari Web UI。兩次都使用相同的認證。

2. 從左邊的頁面上的服務清單中選取 __Storm__。 然後選取 __Storm UI__ 從 __快速連結__。

    ![快速連結中的 Storm UI 項目](./media/hdinsight-apache-storm-tutorial-get-started-linux/ambari-storm.png)

    這會顯示 Storm UI︰

    ![Storm UI](./media/hdinsight-apache-storm-tutorial-get-started-linux/stormui.png)
    
    > [AZURE.NOTE] 如果您收到的錯誤，找不到伺服器時，您可能會不建立叢集的 SSH 通道。 請參閱 [使用 SSH 通道存取 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md) 如需詳細資訊。

4. 在 **拓樸摘要**, ，請選取 **wordcount** 中的項目 **名稱** 資料行。 這麼做會顯示拓撲的詳細資訊。

    ![包含 Storm Starter WordCount 拓樸資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

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

5. 從這個頁面上，選取一個項目 **Spout** 或 **Bolt** 一節。 如此會顯示所選元件的相關資訊。

    ![包含所選元件相關資訊的 Storm 儀表板。](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

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

    這項資料來自您所見，這個字 **七個** 發生出現 1493957 次。 這就是啟動拓撲後，該字所出現的次數。

##停止拓撲

返回 **拓樸摘要** 字數統計拓撲，頁面上，然後選取 **Kill** 按鈕 **拓撲動作** 一節。 出現提示時，請先輸入要等候 10 秒，再停止拓撲。 逾時期限過後，拓撲將不再顯示當您瀏覽 **Storm UI** 儀表板] 區段。

##摘要

您已在本 Apache Storm 教學課程中藉由 Storm Starter 了解如何建立 Storm on HDInsight 叢集，以及如何使用 Storm 儀表板部署、監視和管理 Storm 拓撲。

##<a id="next"></a>後續步驟

* 以下文件含有可和 Storm on HDInsight 搭配使用的其他範例清單：

    * [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/
