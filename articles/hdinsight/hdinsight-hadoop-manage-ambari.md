<properties
   pageTitle="使用 Apache Ambari Web UI 監視和管理 HDInsight 叢集 | Microsoft Azure"
   description="了解如何使用 Ambari 來監視和管理以 Linux 為基礎的 HDInsight 叢集。 在本文件中，您將學習如何使用 HDInsight 叢集隨附的 Ambari Web UI。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/03/2015"
   ms.author="larryfr"/>

#使用 Ambari Web UI 管理 HDInsight 叢集

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari 提供容易使用的 Web UI 和 REST API，可簡化 Hadoop 叢集的管理和監視。 以 Linux 為基礎的 HDInsight 叢集上有 Ambari，用來監視叢集並進行組態變更。

在本文件中，您將學習如何搭配使用 Ambari Web UI 和 HDInsight 叢集。

> [AZURE.NOTE] 這篇文章中的資訊僅適用於 Linux 為基礎的 HDInsight 叢集。 若為以 Windows 為基礎的 HDInsight 叢集，則只能透過 Ambari REST API 進行監視。 請參閱 [在 HDInsight 使用 Ambari API 監視以 Windows 為基礎的 Hadoop](hdinsight-monitor-use-ambari-api.md)。

##<a id="whatis"></a>什麼是 Ambari？

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> 提供簡單易用的 Web UI，以供用來佈建、管理及監視 Hadoop 叢集，讓 Hadoop 管理起來更為簡單。 開發人員可以使用 <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari REST API</a> 將這些功能整合到應用程式。

以 Linux 為基礎的 HDInsight 叢集預設會提供 Ambari Web UI。 

##連線能力

Ambari Web UI 位於您 HDInsight 叢集會在 HTTPS://CLUSTERNAME.azurehdidnsight.net，其中 __CLUSTERNAME__ 是叢集的名稱。 

> [AZURE.IMPORTANT] 連線到 HDInsight 上的 Ambari 需要 HTTPS。 您也必須驗證 ambari 使用系統管理員帳戶名稱 (預設值是 __admin__,，) 和建立叢集時所提供的密碼。

##SSH Proxy

> [AZURE.NOTE] 雖然直接透過網際網路，從 Ambari Web UI （例如 jobtracker） 的一些連結可存取叢集的 Ambari 不是在網際網路上公開。 所以除非您使用安全殼層 (SSH) 通道來代理通往叢集前端節點的 Web 流量，否則會在嘗試存取這些功能時看見「找不到伺服器」錯誤。

建立使用 Ambari SSH 通道的資訊，請參閱 [使用 SSH 通道存取 Ambari web UI、 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

##Ambari Web UI

連線到 Ambari Web UI 時，系統會提示您通過頁面驗證。 使用叢集系統管理員使用者 (預設值是 Admin) 和建立叢集時使用的密碼。

當頁面開啟時，請注意頂端的資訊列。 此資訊列包含下列資訊和控制項：

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari 標誌** -開啟儀表板，可用來監視叢集。

* **叢集名稱 # 項作業** -顯示進行中的 Ambari 作業數目。 選取叢集名稱或 **# 項作業** 會顯示背景作業的清單。

* **# 個警示** -警告或重大警示，如果有任何叢集。 選取這個選項會顯示警示清單。

* **儀表板** -顯示儀表板。

* **服務** -叢集中之服務的資訊和組態設定。

* **主機** -叢集中節點的資訊和組態設定。

* **警示** -資訊、 警告和重大警示的記錄檔。

* **系統管理員** -軟體堆疊/服務安裝在叢集、 服務帳戶資訊及 Kerberos 安全性。

* **系統管理員按鈕** -Ambari 管理、 使用者設定和登出。

##監視

###Alerts

Ambari 提供許多警示，其可能狀態如下：

* **OK**

* **警告**

* **重要**

* **未知**

以外的警示 **確定** 會導致 **# 個警示** 要顯示的警示數目頁面頂端的項目。 選取此項目將會顯示警示及其狀態。

警示分成數個預設群組，而您可以檢視從 **警示** 頁面。

![警示頁面](./media/hdinsight-hadoop-manage-ambari/alerts.png)

您可以使用管理群組 **動作** ] 功能表並選取 **管理警示群組**。 這可讓您修改現有群組，或建立新的群組。

![管理警示群組對話方塊](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

您也可以建立警示的通知 **動作** 功能表。 這可讓您建立透過傳送通知的觸發程序 **電子郵件** 或 **SNMP** 發生組合的警示/嚴重性時。 例如，您可以傳送警示時的任何警示中 **YARN 預設** 群組設為 **重大**。

![建立警示對話方塊](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###叢集

 **度量** 儀表板] 索引標籤包含一系列的 widget，可讓您輕鬆地監視您一眼的叢集狀態。 數個 widget 可，例如 **CPU 使用量**, ，按下後提供其他資訊。

![儀表板與度量](./media/hdinsight-hadoop-manage-ambari/metrics.png)

 **熱圖** ] 索引標籤會以我們從綠色到紅色的彩色熱圖顯示度量。

![儀表板與熱圖](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

如需詳細資訊，在叢集中節點上的，選取 **主機**, ，然後選取您感興趣的特定節點。

![主機詳細資料](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###服務

 **服務** 提要欄位在儀表板提供快速深入了解在叢集上執行之服務的狀態。 其中使用各種圖示來指出狀態或應採取的動作，例如需要回收服務時便會出現黃色回收符號。

![服務提要欄位](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

選取服務將會顯示服務的詳細資訊。

![服務摘要資訊](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####快速連結

某些服務顯示 **快速連結** 在頁面頂端的連結。 此連結可用來存取服務特定的 Web UI，例如：

* **工作歷程記錄** -MapReduce 作業記錄。

* **資源管理員** -YARN ResourceManager UI。

* **NameNode** -Hadoop 分散式檔案系統 (HDFS) NameNode UI。

* **Oozie Web UI** -Oozie UI。

選取任何一個連結便會在瀏覽器中開啟新索引標籤以顯示選取的頁面。

> [AZURE.NOTE] 選取 **快速連結** 連結的任何服務將會導致 「 找不到伺服器 」 的錯誤是使用安全通訊端層 (SSL) 通道以 proxy 將 web 流量傳送到叢集。 這是因為用來顯示這項資訊的 Web 應用程式不會在網際網路上公開。
>
> 如需搭配 HDInsight 使用 SSL 通道，請參閱 [使用 SSH 通道存取 Ambari web UI、 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md)

##管理

###Ambari 使用者、群組和權限

使用者、群組和權限的管理不應與 HDInsight 叢集搭配使用。

###主機

 **主機** 頁面會列出叢集中所有主機。 若要管理主機，請遵循下列步驟。

![主機頁面](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] 加入、 解除委任或重新委任主機不應使用 HDInsight 叢集。

1. 選取您想要管理的主機。

2. 使用 **動作** ] 功能表上，選取您想要執行的動作 ︰

    * **啟動所有元件** -都啟動主機上的所有元件。

    * **停止所有元件** -都停止主機上的所有元件。

    * **重新啟動所有元件** -先停止再啟動主機上的所有元件。

    * **開啟維護模式** -隱藏主機的警示。 如果您所執行的動作會產生警示，則應加以啟用，例如，重新啟動執行中的服務所依賴的服務時。

    * **關閉維護模式** -讓主機恢復正常警示功能。

    * **停止** -停駐點 DataNode 或 NodeManagers 主機上的。

    * **啟動** -啟動 DataNode 或 NodeManagers 主機上的。

    * **重新啟動** -停止再啟動 DataNode 或 NodeManagers 主機上的。

    * **解除委任** -從叢集移除主機。

        > [AZURE.NOTE] 請勿在 HDInsight 叢集上使用此動作。

    * **重新委任** -將先前已解除委任的主機加入叢集。

        > [AZURE.NOTE] 請勿在 HDInsight 叢集上使用此動作。

###<a id="service"></a>服務

從 **儀表板** 或 **服務** 頁面上，使用 **動作** 來停止及啟動所有服務的服務清單底部的按鈕。

![服務動作](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING] 雖然 __新增服務__ 會列在這個功能表中，它不應將服務新增至 HDInsight 叢集。 您應該在叢集佈建期間，使用指令碼動作加入新服務。 如需有關如何使用指令碼動作的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。


雖然 **動作** 按鈕可以重新啟動所有服務，通常您要啟動、 停止或重新都啟動特定的服務。 使用下列步驟可對個別服務執行動作：

1. 從 **儀表板** 或 **服務** 頁面上，選取一項服務。

2. 從頂端 **摘要** 索引標籤上，使用 **服務動作** 按鈕，然後選取要採取的動作。 這會重新啟動所有節點上的服務。

    ![服務動作](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

    > [AZURE.NOTE] 叢集正在執行時重新啟動某些服務可能會產生警示。 若要避免這個問題，您可以使用 **服務動作** ] 按鈕來啟用 **維護模式** 之前執行重新啟動服務。

3. 一旦選取某個動作， **# 項作業** 在頁面頂端的項目會遞增數字，指出正在進行背景作業。 如果設定為顯示，則會顯示背景作業的清單。

    > [AZURE.NOTE] 如果您啟用 **維護模式** 服務，請務必將它停用使用 **服務動作** 作業已完成] 按鈕。

若要設定服務，請使用下列步驟：

1. 從 **儀表板** 或 **服務** 頁面上，選取一項服務。

2. 選取 **設定** ] 索引標籤。 隨即會顯示目前的組態。 同時也會顯示先前組態的清單。

    ![組態](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 使用以修改組態，然後選取顯示的欄位 **儲存**。 或選取先前的組態，然後選取 **設為現用** 回復到先前的設定。

##Ambari 檢視

Ambari 檢視可讓開發人員將 UI 項目插入 Ambari Web UI 使用 [Ambari 檢視 Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views)。 HDInsight 提供下列具有 Hadoop 叢集類型的檢視：

* Yarn 佇列管理員：佇列管理員提供簡單的 UI 以用於檢視及修改 YARN 佇列。
* Hive 檢視：Hive 檢視可讓您直接從網頁瀏覽器執行 Hive 查詢。 您可以儲存查詢、檢視結果、將結果儲存至叢集存放區，或將結果下載到您本機系統。 如需有關使用 Hive 檢視的詳細資訊，請參閱 [搭配 HDInsight 使用 Hive 檢視](hdinsight-hadoop-use-hive-ambari-view.md)。
* Tez 檢視：Tez 檢視可讓您透過檢視 Tez 工作執行方式及工作使用哪些資源的相關資訊，更深入了解工作以及將工作最佳化。

