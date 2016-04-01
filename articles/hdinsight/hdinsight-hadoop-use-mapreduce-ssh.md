<properties
   pageTitle="MapReduce 和 SSH 連線與 HDInsight 中的 Hadoop | Microsoft Azure"
   description="了解如何使用 SSH，以利用 HDInsight 上的 Hadoop 來執行 MapReduce 工作。"
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
   ms.date="11/06/2015"
   ms.author="larryfr"/>

# 搭配使用 MapReduce 與 HDInsight 上的 Hadoop 和 SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將學習如何使用安全殼層 (SSH) 連線至 HDInsight 叢集上的 Hadoop，然後使用 Hadoop 命令提交 MapReduce 工作。

> [AZURE.NOTE] 如果您已熟悉使用以 Linux 為基礎的 Hadoop 伺服器，但剛接觸 HDInsight，請參閱 [Linux 為基礎的 HDInsight 秘訣](hdinsight-hadoop-linux-information.md)。

##<a id="prereq"></a>必要條件

若要完成本文中的步驟，您需要下列項目：

* Linux 型 HDInsight (HDInsight 上的 Hadoop) 叢集

* SSH 用戶端。 Linux、Unix 和 Mac 作業系統應該具備 SSH 用戶端。 Windows 使用者必須下載用戶端，例如 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

##<a id="ssh"></a>使用 SSH 連線

使用 SSH 命令，連線至 HDInsight 叢集的完整網域名稱 (FQDN)。 FQDN 將是您提供給叢集，後面接著名稱 **。 azurehdinsight.net**。 例如，下列會連線至名為 **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**如果您提供憑證金鑰進行 SSH 驗證** 在建立 HDInsight 叢集時，您可能需要在用戶端系統上，指定私密金鑰的位置，例如 ︰

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**如果您提供密碼進行 SSH 驗證** 在建立 HDInsight 叢集時，您將需要時提示您提供密碼。

如需有關如何使用 SSH 與 HDInsight 的詳細資訊，請參閱 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Linux、 Unix 和 OS X 在 HDInsight 上](hdinsight-hadoop-linux-use-ssh-unix.md)。

###PuTTY (Windows 用戶端)

Windows 未提供內建 SSH 用戶端。 我們建議使用 **PuTTY**, ，這可以從下載 [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

如需有關如何使用 PuTTY 的詳細資訊，請參閱 [使用 SSH 與以 Linux 為基礎的 Hadoop，從 Windows 在 HDInsight 上 ](hdinsight-hadoop-linux-use-ssh-windows.md)。

##<a id="hadoop"></a>使用 Hadoop 命令

1. 您已連接到 HDInsight 叢集之後，請使用下列 **Hadoop** 命令來啟動 MapReduce 工作 ︰

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    這會啟動 **wordcount** 類別，包含在 **mapreduce-hadoop-examples.jar** 檔案。 做為輸入，它會使用 **wasb://example/data/gutenberg/davinci.txt** 文件，輸出則儲存在 **wasb: / 範例/data/WordCountOutput**。

    > [AZURE.NOTE] 如需有關此 MapReduce 工作和範例資料的詳細資訊，請參閱 [在 HDInsight 上的 Hadoop 使用 MapReduce](hdinsight-use-mapreduce.md)。

2. 工作會在處理時發出詳細資料，並於工作完成時傳回與下列類似的資訊：

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. 工作完成時，使用下列命令來列出輸出的檔案儲存在 **wasb://example/data/WordCountOutput**:

        hdfs dfs -ls wasb:///example/data/WordCountOutput

    這應該會顯示兩個檔案， **SUCCESS** 和 **一部分-r-00000**。  **一部分-r-00000** 檔案包含這項工作的輸出。

    > [AZURE.NOTE] 某些 MapReduce 工作可能會分成多個結果 **一部分-r-# # #** 檔案。 若是如此，請使用 ##### 尾碼指出檔案的順序。

4. 若要檢視輸出，請使用下列命令：

        hdfs dfs -cat wasb:///example/data/WordCountOutput/part-r-00000

    這會顯示包含在單字清單 **wasb://example/data/gutenberg/davinci.txt** 檔案和每個單字的出現次數。 以下是要包含在檔案中之資料的範例：

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>摘要

如您所見，Hadoop 命令提供簡單的方法，在 HDInsight 叢集中執行 MapReduce 工作，然後檢視工作輸出。

##<a id="nextsteps"></a>接續步驟

如需 HDInsight 中 MapReduce 工作的一般資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)


