<properties
   pageTitle="搭配使用 MapReduce 和 Curl 與 HDInsight 中的 Hadoop | Microsoft Azure"
   description="了解如何使用 Curl 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop。"
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
   ms.date="12/04/2015"
   ms.author="larryfr"/>

#使用 Curl 搭配執行 MapReduce 工作與 HDInsight 上的 Hadoop

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文件中，您將學習如何使用 Curl 在 HDInsight 叢集的 Hadoop 上執行 MapReduce 工作。

Curl 用來示範如何使用原始 HTTP 要求與 HDInsight 互動，以執行 MapReduce 工作。 要想執行這些作業，就要使用 HDInsight 叢集所提供的 WebHCat REST API (先前稱為 Templeton)。

> [AZURE.NOTE] 如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱 [您需要在 HDInsight 上的 Linux 型 Hadoop 需知](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Hadoop (Linux 或 Windows 型)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>使用 Curl 執行 MapReduce 工作

> [AZURE.NOTE] 當您 WebHCat 的情況下，使用 Curl 或任何其他 REST 通訊時，您必須提供 HDInsight 叢集系統管理員使用者名稱和密碼來驗證要求。 您也必須使用叢集名稱，做為用來將要求傳送至伺服器的 URI 一部分。
>
> 針對本節中的命令，將 **USERNAME** 與要驗證叢集，使用者和 **密碼** 取代為使用者帳戶的密碼。 取代 **CLUSTERNAME** 與您的叢集名稱。
>
> 使用[基本存取驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)來保護 REST API 的安全。 您應該一律使用 HTTPS 提出要求，確保認證安全地傳送至伺服器。

1. 從命令列中，使用下列命令來確認您可以連線到 HDInsight 叢集：

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    您應該會收到如下所示的回應：

        {"status":"ok","version":"v1"}

    此命令中使用的參數如下：

    * **-u**︰ 表示的使用者名稱和密碼可用來驗證要求
    * **-G**︰ 指出這是 GET 要求

    URI，開頭 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, ，也適用於所有要求。

2. 若要提交 MapReduce 工作，請使用下列命令：

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    URI 結尾 (/mapreduce/jar) 告訴 WebHCat，此要求會從 jar 檔案中的類別啟動 MapReduce 工作。 此命令中使用的參數如下：

    * **-d**: `-G` 未使用，因此要求會依預設使用 POST 方法。 `-d` 指定傳送的資料值與要求。

        * **user.name**︰ 執行命令的使用者
        * **jar**︰ 執行包含的類別的 jar 檔案的位置
        * **類別**︰ 包含 MapReduce 邏輯的類別
        * **arg**︰ 引數傳遞給 MapReduce 工作，在此情況下，輸入的文字檔和目錄，以供輸出

    此命令應該會傳回可用來檢查工作狀態的工作識別碼：

        {"id":"job_1415651640909_0026"}

3. 若要檢查工作的狀態，請使用下列命令。 取代 **JOBID** 取代上一個步驟中傳回的值。 例如，如果傳回值為 `{"id":"job_1415651640909_0026"}`，則 JOBID 會是 `job_1415651640909_0026`。

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    如果工作已完成，則狀態會是 [成功]。

    > [AZURE.NOTE] 此 Curl 要求會傳回工作; 的相關資訊的 JSON 文件jq 可用來擷取狀態值。

4. 當工作的狀態已變更為 **成功**, ，您可以從 Azure Blob 儲存體擷取工作結果。  `statusdir` 與查詢一起傳遞的參數包含輸出檔案的; 在此案例中，位置 **wasb: / 範例/curl**。 此位址會將儲存在工作的輸出 **範例/curl** 目錄中的 HDInsight 叢集所使用的預設儲存體容器。

您可以列出並下載這些檔案使用 [適用於 Mac、 Linux 和 Windows 的 Azure CLI](../xplat-cli-install.md)。 例如，若要列出檔案中 **範例/curl**, ，使用下列命令 ︰

    azure storage blob list <container-name> example/curl

若要下載檔案，請使用下列命令：

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] 您必須指定包含 blob 使用儲存體帳戶名稱 `-a` 和 `-k` 參數或組 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS\_KEY** 環境變數。 請參閱 [如何將資料上傳至 HDInsight](hdinsight-upload-data.md) 如需詳細資訊。

##<a id="summary"></a>摘要

如這份文件所示，您可以使用原始 HTTP 要求來執行、監視和檢視 HDInsight 叢集中的 Hive 工作結果。

如需這篇文章中所使用的 REST 介面的詳細資訊，請參閱 [WebHCat 參照](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)


