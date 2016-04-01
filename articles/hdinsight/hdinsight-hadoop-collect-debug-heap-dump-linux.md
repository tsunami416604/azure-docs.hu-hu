<properties
    pageTitle="在 HDInsight 上啟用 Hadoop 服務的堆積傾印 | Microsoft Azure"
    description="在以 Linux 為基礎的 HDInsight 叢集上啟用 Hadoop 服務的堆積傾印，以進行偵錯和分析。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>


#在以 Linux 為基礎的 HDInsight 上啟用 Hadoop 服務的堆積傾印 (預覽)

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

堆積傾印含有應用程式記憶體的快照，其中包括建立傾印時的變數值。 因此堆積傾印非常有助於診斷在執行階段發生的問題。

> [AZURE.NOTE] 這篇文章中的資訊僅適用於以 Linux 為基礎的 HDInsight。 如需 Windows 為基礎的 HDInsight 資訊，請參閱 [啟用堆積傾印 Windows 為基礎的 HDInsight 上的 Hadoop 服務](hdinsight-hadoop-collect-debug-heap-dumps.md)

## <a name="whichServices"></a>服務

您可以啟用下列服務的堆積傾印：

*  **hcatalog** -tempelton
*  **hive** -hiveserver2、 metastore、 derbyserver
*  **mapreduce** -jobhistoryserver
*  **yarn** -resourcemanager、 nodemanager、 timelineserver
*  **hdfs** -datanode、 secondarynamenode、 namenode

您也可以針對 HDInsight 所執行的 map 和 reduce 處理序來啟用堆積傾印。

## <a name="configuration"></a>了解堆積傾印組態

堆積傾印的啟用方式，是在服務啟動時將選項 (有時稱為參數) 傳遞至 JVM。 就大部分的 Hadoop 服務而言，修改啟動服務所使用的 Shell 指令碼即可完成這項作業。

在每個指令碼中，沒有匯出 **\*\_OPTS**, ，其中包含傳遞至 JVM 的選項。 例如，在 **hadoop-env.sh** 指令碼開頭的那一行 `export HADOOP_NAMENODE_OPTS=` 含有 NameNode 服務的選項。

map 和 reduce 處理序會稍有不同，因為它們是 MapReduce 服務的子處理序。 每個 map 或 reduce 處理序都會在一個子容器中執行，且有兩個含有這些 JVM 選項的項目。 兩者均包含在 **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [AZURE.NOTE] 我們建議使用 Ambari 來修改指令碼和 mapred-site.xml 設定，因為 Ambari 會處理將變更複寫到叢集中的節點。 請參閱 [使用 Ambari](#using-ambari) 一節取得的特定步驟。

###啟用堆積傾印

以下選項可在發生 OutOfMemoryError 時啟用堆積傾印：

    -XX:+HeapDumpOnOutOfMemoryError

 **+** 表示已啟用此選項。 預設值為停用。

> [AZURE.WARNING] 堆積傾印不會啟用在 HDInsight 上的 Hadoop 服務依預設，當傾印檔案可能很大。 如果您為了進行疑難排解而啟用它們，請記得在重現問題並收集傾印檔案之後將其停用。

###傾印位置

傾印檔案的預設位置是目前的工作目錄。 您可以使用以下選項，控制檔案的儲存位置：

    -XX:HeapDumpPath=/path

例如，使用 `-XX:HeapDumpPath=/tmp` 會使傾印儲存在 /tmp 目錄中。

###指令碼

您也可以觸發指令碼時 **OutOfMemoryError** 就會發生。 例如觸發通知，讓您知道發生了錯誤。 這可由以下選項來控制：

    -XX:OnOutOfMemoryError=/path/to/script

> [AZURE.NOTE] 由於 Hadoop 是分散式的系統中，使用任何指令碼必須放置在叢集上執行服務的所有節點上。
>
> 指令碼也必須位於服務所屬的帳戶可以存取的位置中，而且必須提供執行權限。 例如，您可能想要在 `/usr/local/bin` 中存放指令碼，並用 `chmod go+rx /usr/local/bin/filename.sh` 授與讀取和執行權限。

##使用 Ambari

若要修改服務的組態，請依照下列步驟進行：

1. 開啟叢集的 Ambari Web UI。 URL 會是 https://YOURCLUSTERNAME.azurehdinsight.net。

    出現提示時，請使用您叢集的 HTTP 帳戶名稱 (預設值：admin) 和密碼來向網站驗證。

    > [AZURE.NOTE] 您可能會提示輸入第二次 ambari 的使用者名稱和密碼。 此時只要重新輸入同一組帳戶名稱和密碼即可。

2. 使用左側的清單，選取您想要修改的服務區域。 例如， **HDFS**。 在 [中心] 區域中，選取 **設定** ] 索引標籤。

    ![Ambari Web 圖片 (已選取 HDFS 設定索引標籤)](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. 使用 **篩選...** 項目，請輸入 **opts**。 這會篩選為只包含此文字，設定項目的清單，而且可以快速找到 shell 指令碼或 **範本** ，可以用來設定這些選項。

    ![篩選的清單](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. 尋找 **\*\_OPTS** 服務項目要啟用堆積傾印，並新增您想要啟用的選項。 在下圖中，我新增了 `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` 至 **HADOOP\_NAMENODE\_OPTS** 項目 ︰

    ![含有 -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/ 的 HADOOP_NAMENODE_OPTS](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

    > [AZURE.NOTE] 您在啟用堆積傾印地圖，或降低子處理序，將會改為欄位標記為尋找 **mapreduce.admin.map.child.java.opts** 和 **mapreduce.admin.reduce.child.java.opts**。

    使用 **儲存** ] 按鈕以儲存所做的變更。 系統會允許您輸入簡短的附註，說明所做的變更。

5. 套用所做的變更之後, **需要重新啟動** 圖示會出現一或多個服務旁邊。

    ![必須重新啟動圖示和重新啟動按鈕](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. 選取需要重新啟動，每個服務，並使用 **服務動作** 按鈕 **開啟維護模式**。 這可避免您在重新啟動此服務時，從中產生警示。

    ![開啟維護模式功能表](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. 一旦您啟用維護模式中使用 **重新啟動** 服務按鈕 **重新啟動所有受影響**

    ![重新啟動所有受影響的項目](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

    > [AZURE.NOTE] 項目 **重新啟動** 按鈕可能會因的其他服務。

8. 一旦重新啟動服務，使用 **服務動作** 按鈕 **關閉維護模式**。 這麼做可讓 Ambari 繼續監視服務是否有警示。


