<properties
    pageTitle="使用以 Linux 為基礎的 HDInsight 開發指令碼動作| Microsoft Azure"
    description="深入了解使用指令碼動作來自訂以 Linux 為基礎的 HDInsight 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/04/2015"
    ms.author="larryfr"/>


# 使用 HDInsight 開發指令碼動作

指令碼動作是自訂 Azure HDInsight 叢集的方式，方法是在安裝期間指定叢集組態設定，或者在叢集上安裝額外服務、工具或其他軟體。
> [AZURE.NOTE] 本文件的資訊是以 Linux 為基礎的 HDInsight 叢集的特定資訊。 如需使用指令碼動作以 Windows 為基礎的叢集，請參閱 [開發指令碼動作搭配 HDInsight (Windows)](hdinsight-hadoop-script-actions.md)。

## 什麼是指令碼動作？

指令碼動作是在佈建期間在叢集節點上執行的 Bash 指令碼。 指令碼動作是依據根權限來執行，並具有叢集節點的完整存取權限。

您可以在使用 __Azure 入口網站__、__Azure PowerShell__ 或 __HDInsight .NET SDK__ 佈建叢集時，使用指令碼動作。

自訂使用指令碼動作在叢集的逐步解說，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="bestPracticeScripting"></a>指令碼開發的最佳作法

為 HDInsight 叢集開發自訂的指令碼時，有數個最佳做法需要牢記在心：

- [目標的 Hadoop 版本](#bPS1)
- [提供穩定的指令碼資源連結](#bPS2)
- [使用預先編譯的資源](#bPS4)
- [確保叢集自訂指令碼具有等冪性](#bPS3)
- [確保叢集架構具有高可用性](#bPS5)
- [設定自訂元件來使用 Azure Blob 儲存體](#bPS6)
- [將資訊寫入至 STDOUT 與 STDERR](#bPS7)
- [將檔案儲存為 ASCII，與 LF 行尾結束符號](#bps8)

> [AZURE.IMPORTANT] 指令碼動作必須在 60 分鐘內完成，否則就會逾時。 在節點佈建期間，會同時執行指令碼與其他安裝和設定程序。 與在您開發環境中的執行時間相較，爭用 CPU 時間和網路頻寬等資源可能會導致指令碼需要較長的時間才能完成。

### <a name="bPS1"></a>目標的 Hadoop 版本

不同版本的 HDInsight 有不同版本的 Hadoop 服務和已安裝的元件。 如果您的指令碼預期特定版本的服務或元件，您應該只在包含必要元件的 HDInsight 版本中使用指令碼。 您可以找到有關隨附於 hdinsight 元件版本 [HDInsight 元件版本設定](hdinsight-component-versioning.md) 文件。

### <a name="bPS2"></a>提供穩定的指令碼資源連結

使用者應該確定在叢集的整個存留期間，由指令碼使用的所有指令碼及資源都保持可用，並且這些檔案的版本在此持續時間內不會變更。 如果叢集中的節點重新製作映像，就必須有這些資源。

最佳做法是下載並封存您的訂用帳戶上 Azure 儲存體帳戶中的所有項目。
> [AZURE.IMPORTANT] 使用的儲存體帳戶必須是叢集的預設儲存體帳戶，或是位於其他任何儲存體帳戶上的公用唯讀容器。

例如，由 Microsoft 所提供的範例會儲存在 [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) 儲存體帳戶，是由 HDInsight 小組所維護的公用、 唯讀容器。

### <a name="bPS4"></a>使用預先編譯的資源

若要讓執行指令碼所花費的時間降到最低，請避免這類從原始程式碼直接編譯的作業。 相反地，請預先編譯相關資源並將二進位版本儲存在 Azure Blob 儲存體中，這樣可讓其能夠快速地從您指令碼下載到叢集。

### <a name="bPS3"></a>確保叢集自訂指令碼具有等冪性

您必須預期在叢集存留期間將會為 HDInsight 叢集的節點重新製作映像。且在發生時會使用叢集自訂指令碼。 此指令碼必須設計成具有等冪性，意思就是在重新製作映像時，此指令碼應該確保叢集在每一次執行時會回到相同的狀態。

例如，如果自訂指令碼在第一次執行時，在 /usr/local/bin 中安裝了某個應用程式，則在後續每次的執行中，此指令碼應該先檢查 /usr/local/bin 位置中是否有該應用程式，再繼續執行指令碼中的其他步驟。

### <a name="bPS5"></a>確保叢集架構具有高可用性

以 Linux 為基礎的 HDInsight 叢集提供在叢集中是作用中的兩個前端節點，且指令碼動作會針對兩個節點執行。 如果您安裝的元件預期只有一個前端節點，您必須設計指令碼，它只會在叢集中兩個前端節點之一安裝元件。
> [AZURE.IMPORTANT] 安裝為 HDInsight 一部分的預設服務是被設計來視需要在兩個前端節點之間容錯移轉，但是此功能未擴充至透過指令碼動作安裝的自訂元件。 如果您需要讓透過指令碼動作安裝的元件高度可用，您必須實作您自己的容錯移轉機制，該機制使用兩個可用的前端節點。

### <a name="bPS6"></a>設定自訂元件來使用 Azure Blob 儲存體

您安裝在叢集上的元件可能有使用 Hadoop 分散式檔案系統 (HDFS) 儲存體的預設組態。 在重新製作叢集映像時，會格式化 HDFS 檔案系統，因此您會遺失儲存在其中的所有資料。 您應該變更組態，改為使用 Azure Blob 儲存體 (WASB)，因為這是叢集的預設儲存體，即使刪除叢集也會保留。

例如，以下會將 giraph-examples.jar 檔案從本機檔案系統複製到 WASB：

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>將資訊寫入至 STDOUT 與 STDERR

寫入 STDOUT 和 STDERR 的資訊會記錄起來，可以在使用 Ambari Web UI 佈建叢集之後檢視。

大部分的公用程式和安裝套件已經將資訊寫入 STDOUT 和 STDERR，不過您可能想要新增其他記錄。 若要將文字傳送到 STDOUT 使用 `echo`。 例如：

        echo "Getting ready to install Foo"

根據預設， `echo` 將字串傳送至 STDOUT。若要將其導向至 STDERR，加入 `> & 2` 之前 `echo`。例如：

        >&2 echo "An error occured installing Foo"

這會將傳送到 STDOUT (1，這是預設值，因此未在此處列出) 的資訊重新導向至 STDERR (2)。 如需有關 IO 重新導向的詳細資訊，請參閱 [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html)。

如需有關如何檢視記錄的指令碼動作資訊的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> 將檔案儲存為 ASCII，與 LF 行尾結束符號

Bash 指令碼應該儲存為 ASCII 格式，該格式以 LF 做為行尾結束符號。 如果檔案儲存為 UTF-8，可能在檔案開頭包含位元組順序標記，或者以 CRLF 做為行尾結束符號，這對於 Windows 編輯器很常見，則指令碼將會失敗，且具有如下的錯誤：

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

## <a name="helpermethods"></a>自訂指令碼的 helper 方法

指令碼動作協助程式方法是您在撰寫字訂指令碼時可以使用的公用程式。 定義在 [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh), ，而且可以包含在指令碼，使用下列命令:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

這樣可讓下列協助程式可用於您的指令碼：

| 協助程式使用方式| 說明|
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [覆寫]`| 從來源 URL 將檔案下載到指定的檔案路徑中。根據預設，它不會覆寫現有的檔案。|
| `untar_file TARFILE DESTDIR`| 解壓縮 tar 檔案 (使用 `-xf`,，) 的目的地目錄。|
| `test_is_headnode`| 如果在叢集前端節點上執行，會傳回 1；否則傳回 0。|
| `test_is_datanode`| 如果目前節點是資料 (背景工作角色) 節點，會傳回 1；否則傳回 0。|
| `test_is_first_datanode`| 如果目前節點是第一個資料 (背景工作角色) 節點 (名為 workernode0)，會傳回 1；否則傳回 0。|

## <a name="commonusage"></a>常見使用模式

本節指引您如何實作某些撰寫自訂指令碼時可能遇到的常見使用模式。

### 將參數傳遞給指令碼

在某些情況下，您的指令碼可能需要參數。 例如，您可能需要叢集的系統管理員密碼，以從 Ambari REST API 擷取資訊。

傳遞至指令碼參數稱為 _positional parameters_，指派給 `$1` 第一個參數， `$2` 秒，並因此入。 `$0` 包含指令碼本身的名稱。

傳遞至指令碼做為參數的值應該加上單引號 (')，以便傳遞的值會被視為常值，並且不會給予特殊處理方式以包含如 '!' 的字元。

### 設定環境變數

設定環境變數是以下列動作執行：

    VARIABLENAME=value

其中 VARIABLENAME 是變數的名稱。 若要存取的變數，在此之後，使用 `$VARIABLENAME`。 例如，若要將位置參數提供的值指派為名為 PASSWORD 的環境變數，您會使用下列項目：

    PASSWORD=$1

接著可以使用資訊的後續存取 `$PASSWORD`。

在指令碼中設定的環境變數只會存在於指令碼的範圍內。 在某些情況下，您可能需要新增整個系統的環境變數，這些變數在指令碼完成之後仍然會保存。 通常這就是為何透過 SSH 連線至叢集的使用者可以使用您的指令碼所安裝的元件。 您可以藉由新增環境變數，以完成這 `eg /etc/ 環境`。 例如，加入下列 __HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### 存取自訂指令碼儲存所在位置

用來自訂叢集的指令碼必須位於叢集的預設儲存體帳戶，或是位於其他任何儲存體帳戶上的公用唯讀容器。 如果指令碼存取位於他處的資源，則這些資源也必須是可公開存取的資源 (至少是公用唯讀狀態)。 您可能要將檔案下載到叢集中使用的執行個體 `download_file`。

將檔案儲存在叢集可以存取的 Azure 儲存體帳戶 (例如預設儲存體帳戶)，可以提供快速存取，因為此儲存體是在 Azure 網路內。

## <a name="deployScript"></a>部署指令碼動作的檢查清單

以下是我們在準備部署這些指令碼時所採取的步驟：

- 將包含自訂指令碼的檔案放在叢集節點可於部署期間存取的位置。 此位置可以是部署叢集時所指定的預設或其他儲存體帳戶，或是其他任何可公開存取的儲存體容器。

- 在指令碼中加入檢查以確定它們以等冪方式執行，使得指令碼可以在相同的節點上執行多次。

- 使用暫存檔案目錄 /tmp 來存放指令碼所使用的下載檔案，然後在執行完指令碼之後將這些檔案清除。

- 如果作業系統層級設定或 Hadoop 服務組態檔已變更，您可能會想要重新啟動 HDInsight 服務，讓它們可以載入任何作業系統層級設定，例如指令碼中設定的環境變數。

## <a name="runScriptAction"></a>如何執行指令碼動作

您可以透過 Azure 入口網站、Azure PowerShell 或 HDInsight .NET SDK，使用指令碼動作自訂 HDInsight 叢集。 如需指示，請參閱 [如何使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#howto)。

## <a name="sampleScripts"></a>自訂指令碼範例

Microsoft 提供了在 HDInsight 叢集上安裝元件的範例指令碼。 您可以在下方的連結中找到這些範例指令碼和其使用方式的說明：

- [安裝和使用色調 HDInsight 叢集上](hdinsight-hadoop-hue-linux.md)
- [安裝和使用 Spark HDInsight 叢集上](hdinsight-hadoop-spark-install-linux.md)
- [安裝和使用 R HDInsight Hadoop 叢集上](hdinsight-hadoop-r-scripts-linux.md)
- [安裝及使用 Solr HDInsight 叢集上](hdinsight-hadoop-solr-install-linux.md)
- [安裝及使用 Giraph 的 HDInsight 叢集](hdinsight-hadoop-giraph-install-linux.md)

> [AZURE.NOTE] 以上的連結文件是針對以 Linux 為基礎的 HDInsight 叢集。 使用 Windows 型 HDInsight 指令碼，請參閱 [開發指令碼動作搭配 HDInsight (Windows)](hdinsight-hadoop-script-actions.md) 或頂端的每個發行項使用可用的連結。

## 疑難排解

使用您所開發的指令碼時可能會遇到下列錯誤：

__錯誤__: `$' \r': 找不到命令`。 有時候後面 `語法錯誤: 意外的檔案結尾`。

原因__：這個錯誤的原因是指令碼中以 CRLF 作為行尾結束符號。 Unix 系統預期只有 LF 當做行尾結束符號。

此問題最常發生在於 Windows 環境中撰寫指令碼時，因為 CRLF 是 Windows 上許多文字編輯器中常見的行尾結束符號。

解決方式__：如果您的文字編輯器提供選項，請選取 Unix 格式或 LF 作為行尾結束符號。 您也可以在 Unix 系統上使用下列命令，將 CRLF 變更為 LF：
> [AZURE.NOTE] 下列命令大致相當於將 CRLF 行尾結束符號變更為 LF。 根據您的系統上可用的公用程式，選取其中一個。

| 命令| 注意事項|
| ------- | ----- |
| `unix2dos INFILE b`| 原始檔案會以 .BAK 副檔名進行備份|
| `-d '\r' < INFILE > OUTFILE tr`| OUTFILE 會包含只有 LF 行尾結束符號的版本|
| `perl-pi-e 的/\r\n/\n/g ' INFILE`| 這將會直接修改檔案而不會建立新的檔案|
| `sed 的 / $'"/' 回應 \\\r'/"INFILE > OUTFILE`| OUTFILE 會包含只有 LF 行尾結束符號的版本。

__錯誤__: `第 1 行: #! / usr/bin/env: 沒有此類檔案或目錄`。

_原因_：指令碼儲存為具有位元組順序標記 (BOM) 的 UTF-8 時，就會發生這個錯誤。

_解決方式_：將檔案儲存為 ASCII，或不具有 BOM 的 UTF-8。 您也可以在 Linux 或 Unix 系統上使用下列命令，以建立不具有 BOM 的新檔案：

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

對於上述命令，以包含 BOM 的檔案取代 __INFILE__。 __OUTFILE__ 應該是新檔案的名稱，且包含不具有 BOM 的指令碼。

## <a name="seeAlso"></a>另請參閱

[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)





