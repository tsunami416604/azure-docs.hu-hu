<properties
    pageTitle="使用指令碼動作在以 Linux 為基礎的 HDInsight 上安裝 Solr | Microsoft Azure"
    description="在本主題中，您將學習如何使用指令碼動作在以 Linux 為基礎的 HDInsight Hadoop 叢集上安裝 Solr。"
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

# 在 HDInsight Hadoop 叢集上安裝和使用 Solr

在本主題中，您將學習如何使用指令碼動作在 Azure HDInsight 上安裝 Solr。 Solr 是強大的搜尋平台，可對 Hadoop 管理的資料執行企業級搜尋功能。 在 HDInsight 叢集上安裝 Solr 之後，您也將學習如何使用 Solr 搜尋資料。

> [AZURE.NOTE] 這份文件中的步驟需要以 Linux 為基礎的 HDInsight 叢集。 在 Solr 中使用 Windows 叢集的資訊，請參閱 [安裝及使用 Solr 在 HDinsight Hadoop 叢集 (Windows)](hdinsight-hadoop-solr-install.md)

本主題中使用的範例指令碼會以特定組態建立 Solr 叢集。 如果您想要以不同的集合、分區、結構描述和複本等項目設定 Solr 叢集，則必須相應修改指令碼和 Solr 二進位檔。

## <a name="whatis"></a>什麼是 Solr？

[Apache Solr](http://lucene.apache.org/solr/features.html) 是可對資料執行強大全文搜尋作業的企業搜尋平台。 Hadoop 可儲存和管理大量資料，而 Apache Solr 則是提供搜尋功能以便快速擷取資料。 本主題說明如何自訂 HDInsight 叢集以安裝 Solr。

## 指令碼會執行哪些作業

此指令碼可以對 HDInsight 叢集進行下列變更：

* 將 Solr 安裝至 `/usr/hdp/current/solr`
* 建立新的使用者， __solrusr__, ，用來執行 Solr 服務
* 設定 __solruser__ 的擁有者 `/usr/hdp/current/solr`
* 新增 [崛起](http://upstart.ubuntu.com/) 會啟動 Solr，如果重新啟動叢集節點的組態。 Solr 也會在安裝之後於叢集節點上自動啟動

## <a name="install"></a>安裝 Solr 使用指令碼動作

在 HDInsight 叢集上安裝 Solr 的範例指令碼是可以從唯讀的 Azure 儲存體 blob 位於 [https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh)。 本節提供有關如何在使用 Azure 入口網站佈建叢集時使用範例指令碼的指示。

> [AZURE.NOTE] 您也可以使用 Azure PowerShell 或 HDInsight.NET SDK 建立叢集，使用此指令碼。 如需有關如何使用這些方法的詳細資訊，請參閱 [指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

1. 開始使用中的步驟來佈建叢集 [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-provision-linux-clusters.md#portal), ，但不是會完成佈建。

2. 在 **選擇性組態** 分頁中，選取 **指令碼動作**, ，並提供下列資訊:

    * __名稱__: 輸入指令碼動作的易記名稱。
    * __指令碼 URI__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
    * __HEAD__: 勾選此選項
    * __背景工作__: 勾選此選項
    * __ZOOKEEPER__: 勾選此選項可在 Zookeeper 節點上安裝
    * __參數__: 將此欄位保留空白

3. 在底部 **指令碼動作**, ，使用 **選取** ] 按鈕以儲存設定。 最後，使用 **選取** 底部的按鈕 **選擇性組態** ] 以儲存的選擇性組態資訊。

4. 繼續中所述，佈建叢集 [佈建以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="usesolr"></a>如何在 HDInsight 中使用 Solr?

### 索引資料

您必須從以某些資料檔案編製 Solr 的索引來開始。 然後，您可以使用 Solr 來對已編製索引的資料執行搜尋查詢。 使用下列步驟以將某些範例資料新增至 Solr，然後查詢它：

1. 使用 SSH 連線到 HDInsight 叢集

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：

    * [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

    > [AZURE.IMPORTANT] 步驟稍後在此文件，請使用的 SSL 通道來連接到 Solr web UI。 為了使用這些步驟，您必須建立 SSL 通道，然後設定您的瀏覽器以使用它。
    >
    > 如需詳細資訊，請參閱 [使用 SSH 通道存取 Ambari web UI、 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md)

2. 使用下列命令以具備 Solr 索引範例資料：

        cd /usr/hdp/current/solr/example/exampledocs
        java -jar post.jar solr.xml monitor.xml

    您會在主控台上看到下列輸出：

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    Post.jar 公用程式使用兩個範例文件索引 Solr **solr.xml** 和 **monitor.xml**。 這些會儲存在 __collection1__ Solr 內。

3. 使用下列項目以查詢 Solr 公開的 REST API：

        curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

    這會發出查詢 __collection1__ 比對任何文件 __\ *: \ *__ (編碼成 \*%3A\* 在查詢字串) 和回應應該傳回為 JSON。 回應看起來應該如下所示：

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### 使用 Solr 儀表板

Solr 儀表板是 Web UI，可讓您透過網頁瀏覽器使用 Solr。 Solr 儀表板不會直接從您的 HDInsight 叢集公開至網際網路上，必須使用 SSH 通道來存取。 如需有關如何使用 SSH 通道的詳細資訊，請參閱 [使用 SSH 通道存取 Ambari web UI、 ResourceManager、 JobHistory、 NameNode、 Oozie、 和其他 web UI](hdinsight-linux-ambari-ssh-tunnel.md)

一旦您建立 SSH 通道，請使用下列步驟以使用 Solr 儀表板：

1. 決定前端節點的主機名稱：

    1. 在瀏覽器中，移至 https://CLUSTERNAME.azurehdinsight.net。 出現提示時，使用系統管理員使用者名稱和密碼來向網站進行驗證。
    
    2. 從頁面頂端的功能表中選取 __主機__。
    
    3. 選取的項目，以開始 __hn0__。 當頁面開啟時，主機名稱會顯示在頂端。 主機名稱的格式是 __hn0 PARTOFCLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__。 這是您連接到 Solr 儀表板時必須使用的主機名稱。
    
1. 在瀏覽器中，連接到 __http://HOSTNAME:8983/solr #/__, ，其中 __HOSTNAME__ 是您在先前步驟決定的名稱。 

    此要求應該會透過 SSH 通道路由傳送至您的 HDInsight 叢集的前端節點。 您應該會看到如下所示的頁面：

    ![Solr 儀表板的映像](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. 從左窗格中，使用 **核心選取器** 下拉式清單選取 **collection1**。 數個項目其下方應會出現 __collection1__。

3. 從下列項目 __collection1__, ，請選取 __查詢__。 使用下列值來填入搜尋頁面：

    * 在 **q** 文字中，輸入 **\ *:**\ *。 如此便會傳回已在 Solr 中編製索引的所有文件。 如果您想要搜尋文件內的特定字串，您可以在此輸入該字串。

    * 在 **wt** 文字中，選取輸出格式。 預設值是 **json**。

    最後，選取 **執行查詢** 搜尋 pate 底部的按鈕。

    ![使用指令碼動作以自訂叢集](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

    輸出中會傳回兩個我們之前用於對 Solr 編製索引的文件。 輸出結果類似下面：

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### 啟動和停止 Solr

如果您需要手動停止或啟動 Solar，請使用下列命令：

    sudo stop solr

    sudo start solr

## 備份已編製索引的資料

您最好從 Solr 叢集節點將已編製索引的資料備份到 Azure Blob 儲存體。 請執行下列步驟來進行此作業：

1. 使用 SSH 連線到叢集，然後使用下列命令來取得前端節點的主機名稱：

        hostname -f
        
2. 使用下列命令來建立已編製索引之資料的快照。 取代 __HOSTNAME__ 與前一個命令所傳回的名稱:

        curl http://HOSTNAME:8983/solr/replication?command=backup

    您應該會看到如下所示的回應：

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

2. 接下來，將目錄變更至 __/usr/hdp/current/solr/example/solr__。 在這裡每個集合會有子目錄。 包含每個集合的目錄 __資料__ 目錄，也就是該集合的快照集所在位置。

    例如，如果您採用步驟稍早範例文件編製索引 __/usr/hdp/current/solr/example/solr/collection1/data__ 目錄現在應該包含名為 __快照集。 # # #__ # 的所在位置的日期和時間的快照集。

3. 使用如下的命令，建立快照資料夾的壓縮封存：

        tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

    這會建立新的保存檔名為 __snapshot.20150806185338855.tgz__, ，其中包含的內容 __snapshot.20150806185338855__ 目錄。

3. 然後您可以使用下列命令，將封存儲存至叢集的主要儲存體：

    hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

    > [AZURE.NOTE] 若要建立專用的目錄，以儲存 Solr 快照集。 例如，`hadoop fs -mkdir /solrbackup`。

如需有關使用 Solr 備份和還原的詳細資訊，請參閱 [進行，並還原備份的 SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores)。


## 另請參閱

- [安裝和使用 HDInsight 叢集上的色調](hdinsight-hadoop-hue-linux.md)。 色調是 Web UI，可讓您更輕鬆地建立、執行及儲存 Pig 和 Hive 工作，以及瀏覽您的 HDInsight 叢集的預設儲存體。

- [安裝和使用 Spark HDInsight 叢集上][hdinsight-install-spark]。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Spark。 Spark 是一個開放原始碼平行處理架構，可支援記憶體內部處理，大幅提升巨量資料分析應用程式的效能。

- [HDInsight 叢集上安裝 R][hdinsight-install-r]。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 R。 R 是一個用於統計計算的開放原始碼語言和環境。 它提供數百個內建的統計函式及它自己的程式設計語言，此語言結合了函式型和物件導向程式設計的層面。 它也提供廣泛的圖形功能。

- [HDInsight 叢集上安裝 Giraph](hdinsight-hadoop-giraph-install-linux.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝 Giraph。 Giraph 可讓您利用 Hadoop 執行圖形處理，且可以搭配 Azure HDInsight 一起使用。

- [HDInsight 叢集上安裝色調](hdinsight-hadoop-hue-linux.md)。 在 HDInsight Hadoop 叢集上使用叢集自訂安裝色調。 色調是一組 Web 應用程式，用來與 Hadoop 叢集互動。



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

