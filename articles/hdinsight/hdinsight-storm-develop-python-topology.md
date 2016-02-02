<properties
   pageTitle="在 HDinsight 上於 Storm 拓撲中使用 Python 元件 | Microsoft Azure"
   description="了解如何在{b> <b}Azure HDInsight 上從 Apache Storm 中Python 元件。您將了解如何從 Java 和 Clojure 型 Storm 拓撲中使用 Python 元件。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# 在 HDInsight 上使用 Python 開發 Apache Storm 拓撲

Apache Storm 支援多種語言，甚至可讓您將數種語言的元件結合成一個拓撲。 本文件中，您將了解如何在 HDInsight 上於 Java 和 Clojure 型 Storm 拓撲中使用 Python 元件。

## 必要條件

* Python 2.7 或更新版本

* Java JDK 1.7 或更新版本

## Storm 多語言支援

Storm 用意是要搭配寫入使用任何程式設計語言，不過，這需要元件了解如何使用元件 [Thrift 定義，如 Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)。 在 Python 中，Apache Storm 專案隨附一個模組，可讓您輕鬆地與 Strom 互動。 您可以找到此模組在 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)。

由於 Apache Storm 是在 Java 虛擬機器 (JVM) 上執行的 Java 程序，以其他語言撰寫的元件會以子程序方式執行。 JVM 中執行的 Storm 程式碼會使用透過 stdin/stdout 傳送的 JSON 訊息，與這些子程序進行通訊。 在元件之間通訊的更多詳細資料可在 [多 lang 通訊協定](https://storm.apache.org/documentation/Multilang-protocol.html) 文件。

### Storm 模組

Storm 模組 (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py，) 提供建立與 Storm 一起使用的 Python 元件所需的位元。

這會提供一些事，像是 `storm.emit` 發出的 tuple，和 `storm.logInfo` 來寫入記錄檔。 建議您詳閱這個檔案，了解它所提供的內容。

## 挑戰

您可以使用 __storm.py__ 模組建立 Python spout 來取用資料、建立 bolt 來處理資料，但是，負責設置元件之間通訊的整個 Storm 拓撲定義，仍是使用 Java 或 Clojure 撰寫。 此外，如果您使用 Java，您也必須建立 Java 元件做為 Python 元件的介面。

另外，因為 Storm 叢集是以分散式方式執行，您必須確定任何 Python 元件所需的任何模組，可供叢集的所有背景工作角色節點使用。 對於多語言的資源，Storm 沒有辦法輕鬆完成此工作，您必須將所有相依性納入拓撲的 jar 檔案中，或在叢集的每一個背景工作角色節點上手動安裝相依性。

有某些專案所要克服這些缺點，例如 [Pyleus](https://github.com/Yelp/pyleus) 和 [Streamparse](https://github.com/Parsely/streamparse)。 雖然這兩項都可以在以 Linux 為基礎的 HDInsight 叢集上執行，但並不是本文的主要焦點，因為它們在叢集安裝期間需要自訂，而且沒有在 HDInsight 叢集經過完整測試。 本文件結尾提供搭配 HDInsight 使用這些架構時的注意事項。

### Java 與Clojure 拓撲定義的比較

在兩種定義拓撲的方法，Clojure 是目前為止最簡單/最乾淨的方法，因為您可以在拓樸定義中直接參考 python 元件。 對於 Java 型拓樸定義，您也必須定義 Java 元件來處理一些工作，例如在 Python 元件傳回的 Tuple 中宣告欄位。

本文件說明這兩種方法，並附上範例專案。

## 使用 Java 拓撲的 Python 元件

> [AZURE.NOTE] 這個範例是可在 https://github.com/Blackmist/hdinsight-python-storm-wordcount __JavaTopology__ 目錄。 這是一個 Maven 型專案。 如果您不熟悉 Maven，請參閱 [開發 Java 型拓撲使用 Apache Storm on HDInsight](hdinsight-storm-develop-java-topology.md) 如需有關建立 Storm 拓撲的 Maven 專案。

使用 Python (或其他 JVM 語言元件) 的 Java 型拓樸乍看之下是使用 Java 元件，但如果查看每個 Java spout/bolt，您會看到類似下列的程式碼：

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Java 在這裡叫用 Python，並執行含有實際 blot 邏輯的指令碼。 Java spout/bolt (針對此範例) 只是在基礎 Python 元件將發出的 Tuple 中宣告欄位。

實際的 Python 檔案會儲存在 `/multilang/資源` 在此範例中的目錄。  `/Multilang` 目錄中被參考 __pom.xml__:

<resources>
    <resource>
        
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

這包括在所有檔案 `/multilang` 資料夾將從這個專案建置的 jar 中。
> [AZURE.IMPORTANT] 請注意，這只會指定 `/multilang` 目錄而非 `/multilang/資源`。 Storm 預期中的非 JVM 資源 `資源` 目錄中，因此它要尋找已經在內部。 將元件放在此資料夾中，可讓您在 Java 程式碼中直接依名稱參考。 例如， `超級 ("python"、"countbolt.py");`。 另一種方式可以把它是 Storm 可看到 `資源` 目錄做為根 (/) 存取多語系資源時。
>
> 此範例專案， `storm.py` 模組包含在 `/multilang/資源` 目錄。

### 建置並執行專案

若要在本機執行此專案，只要使用下列 Maven 命令建置，並以本機模式執行即可：

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

使用 ctrl+c 可結束程序。

若要將專案部署至執行 Apache Storm 的 HDInsight 叢集，請使用下列步驟：

1. 建置 uber jar：

        mvn package

    這會建立名為 __WordCount-1.0 SNAPSHOT.jar__ 中 `/目標` 此專案的目錄。

2. 使用下列其中一種方法，將 jar 檔案上傳至 Hadoop 叢集：

    * 如 __linux__ HDInsight 叢集: 使用 `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` 將 jar 檔案複製到叢集，您的 SSH 使用者名稱與叢集名稱的使用者名稱取代 HDInsight 叢集名稱。

        上傳完成檔案之後，請連接到使用 SSH 的叢集，並啟動拓撲使用 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * 如 __windows__ HDInsight 叢集: 您的瀏覽器中移至 [HTTPS://CLUSTERNAME.azurehdinsight.net/ Storm 儀表板連接。 以您的 HDInsight 叢集名稱取代 CLUSTERNAME，在出現提示時，提供系統管理員名稱和密碼。

        使用表單執行下列動作：

        * __Jar 檔案__：選取 [瀏覽]____，然後選取 __WordCount-1.0-SNAPSHOT.jar__ 檔案
        * __類別名稱__: 輸入 `com.microsoft.example.WordCount`
        * __額外參數__: 輸入好記的名稱，例如 `wordcount` 來識別拓撲

        最後，選取 [提交]____ 啟動拓撲。

> [AZURE.NOTE] Storm 拓撲啟動之後會一直執行到停止 (刪除) 為止。 若要停止拓撲，請使用 `storm kill TOPOLOGYNAME` 命令從命令列 (SSH 工作階段到 Linux 叢集，例如) 或透過 Storm UI 選取拓撲，然後選取 __Kill__ ] 按鈕。

## 使用 Clojure 拓撲的 Python 元件

> [AZURE.NOTE] 這個範例是可在 https://github.com/Blackmist/hdinsight-python-storm-wordcount __ClojureTopology__ 目錄。

此拓撲由使用 [Leiningen](http://leiningen.org) 至 [建立新的 Clojure 專案](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)。 之後，已建立結構的專案經過下列修改：

* __project.clj__：加入 Storm 的相依性，並排除部署至 HDInsight 伺服器時可能造成問題的項目。
* __資源/資源__: Leiningen 建立預設 `資源` 目錄，但檔案儲存在這裡會顯示如何加入此專案中，從建立的 jar 檔案的根目錄，並在 Storm 預期命名的子目錄中的檔案 `資源`。 因此子目錄已經加入，Python 檔案會儲存在 `資源/資源`。 在執行階段，這當成存取 Python 元件時的根目錄 (/)。
* __src/wordcount/core.clj__：此檔案包含拓撲定義，而且由 __project.clj__ 檔案參考。 如需有關使用 Clojure 定義 Storm 拓撲的詳細資訊，請參閱 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)。

### 建置並執行專案

__如果要在本機建置並執行專案__，請使用下列命令：

    lein do clean, run

若要停止拓撲，請使用 __Ctrl+C__。

__如果要建置 Uberjar 並部署至 HDInsight__，請使用下列步驟：

1. 建立包含拓撲和必要相依性的 uberjar：

        lein uberjar

    這會建立名為的新檔案 `wordcount-1.0-SNAPSHOT.jar` 中 `target\uberjar + uberjar` 目錄。

2. 使用下列其中一種方法，將拓撲部署至 HDInsight 叢集來執行：

 * __以 Linux 為基礎的 HDInsight__

   1. 將檔案複製到 HDInsight 叢集前端節點使用 `scp`。 例如：

        scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
    
    Replace USERNAME with an SSH user for your cluster, and CLUSTERNAME with your HDInsight cluster name.

   2. 將檔案複製到叢集之後，使用 SSH 連接到叢集並提交工作。 如需有關搭配使用 SSH 與 HDInsight 的資訊，請參閱下列其中一份文件：

       * [Use SSH with Linux-based HDInsight from Linux, Unix, or OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
       * [Use SSH with Linux-based HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

   3. 連線之後，使用下列命令啟動拓撲：

           storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount


 * __以 Windows 為基礎的 HDInsight__

   1. 在瀏覽器中移至 [HTTPS://CLUSTERNAME.azurehdinsight.net/ 連接 Storm 儀表板。 以您的 HDInsight 叢集名稱取代 CLUSTERNAME，在出現提示時，提供系統管理員名稱和密碼。

   2. 使用表單執行下列動作：

    * __Jar File__: Select __Browse__, then select the __wordcount-1.0-SNAPSHOT.jar__ file
    * __Class Name__: Enter `wordcount.core`
    * __Additional Paramters__: Enter a friendly name such as `wordcount` to identify the topology
    
    Finally, select __Submit__ to start the topology.


> [AZURE.NOTE] Storm 拓撲啟動之後會一直執行到停止 (刪除) 為止。 若要停止拓撲，請使用 `storm kill TOPOLOGYNAME` 命令從命令列 (SSH 工作階段到 Linux 叢集，) 或使用 Storm UI 選取拓撲，然後選取 __Kill__ ] 按鈕。

## Pyleus 架構

[Pyleus](https://github.com/Yelp/pyleus) 是嘗試要進行更輕鬆地使用 Storm 來使用 Python 藉由提供下列架構:

* __以 YAML 為基礎的拓撲定義__：不需要了解 Java 或 Clojure 就能輕鬆定義拓撲
* __以 MessagePack 為基礎的序列化程式__：使用 MessagePack 做為預設序列化，而不是 JSON。 這可以加速元件之間的傳訊
* __相依性管理__：使用 Virtualenv 確保 Python 相依性部署至所有背景工作角色節點。 這需要在背景工作角色節點上安裝 Virtualenv

> [AZURE.IMPORTANT] Pyleus 要求您的開發環境上必須有 Storm。 使用基本 Apache Storm 0.9.3 散發可能會導致 jar 與 HDInsight 隨附的 Storm 版本不相容。 因此，下列步驟使用 HDInsight 做為開發環境。

使用 HDInsight 前端節點做為建置環境，就可以順利建置範例 Pyleus 拓撲。

1. 在 HDInsight 叢集上佈建新的 Storm 時，您必須確定 Python Virtualenv 存在於叢集節點上。 在建立新的以 Linux 為基礎的 HDInsight 叢集時，請使用下列指令碼動作設定與 [叢集自訂](hdinsight-hadoop-customize-cluster.md):

    * __名稱__：在這裡提供易記名稱
    * __ 指令碼 URI__: 使用 `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` 做為值。 此指令碼會在節點上安裝 Python Virtualenv。
        > [AZURE.NOTE] 它也會建立一些目錄，供本文件稍後的 Streamparse 架構使用。

    * __Nimbus__：勾選此項目將指令碼套用到 Nimbus (前端) 節點。
    * __監督員__：勾選此項目將指令碼套用到監督員 (背景工作角色) 節點

    將其他項目保留空白。

1. 建立叢集之後，使用 SSH 連線：

    * [使用 SSH 與以 Linux 為基礎的 HDInsight，從 Linux、 Unix 或 OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [使用 SSH 與以 Linux 為基礎的 HDInsight，從 Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 在 SSH 連線上，使用下列命令建立新的虛擬環境並安裝 Pyleus：

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. 接著，下載 Pyleus git 儲存機制並建置 WordCount 範例：

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml

    建置完成之後，您將有名為的新檔案 `word_count.jar` 目前目錄中。

4. 若要將拓撲提交至 Storm 叢集，請使用下列命令：

        pyleus submit -n localhost word_count.jar

    `-N` 參數指定 Nimbus 主應用程式。 既然我們已經在前端節點上，我們可以使用 `localhost`。

    您也可以使用 `pyleus` Storm 執行其他命令。 使用以下命令列出執行中拓撲，然後再清除 `word_count` 拓撲:

        pyleus list -n localhost
        pyleus kill -n localhost word_count


## Streamparse 架構

[Streamparse](https://github.com/Parsely/streamparse) 是嘗試要進行更輕鬆地使用 Storm 來使用 Python 藉由提供下列架構:

* __架構__：這可讓您輕鬆建立專案的架構，然後修改檔案以加入您的邏輯
* __Clojure DSL 函式__：這些可以降低在 Clojure 拓撲定義中使用 Python 元件時的繁複性。
* __相依性管理__：使用 Virtualenv 確保 Python 相依性部署至所有背景工作角色節點。 這需要在背景工作角色節點上安裝 Virtualenv
* __遠端部署__：Streamparse 可以使用 SSH 自動化將元件部署至背景工作角色節點，而且可以建立 SSH 通道來與 Nimbus 進行通訊。 讓您輕鬆地從開發環境部署至以 Linux 為基礎的叢集，例如 HDInsight

> [AZURE.IMPORTANT] Streamparse 依賴元件預期 [Unix 訊號](https://en.wikipedia.org/wiki/Unix_signal), ，未提供在 Windows 上。 您的開發環境必須是 Linux、Unix 或 OS X，而且 HDInsight 叢集必須以 Linux 為基礎。

1. 在 HDInsight 叢集上佈建新的 Storm 時，您必須確定 Python Virtualenv 存在於叢集節點上。 在建立新的以 Linux 為基礎的 HDInsight 叢集時，請使用下列指令碼動作設定與 [叢集自訂](hdinsight-hadoop-customize-cluster.md):

    * __名稱__：在這裡提供易記名稱
    * __ 指令碼 URI__: 使用 `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh` 做為值。 此指令碼會在節點上安裝 Python Virtualenv，並建立 Streamparse 所使用的目錄
    * __Nimbus__：勾選此項目將指令碼套用到 Nimbus (前端) 節點。
    * __監督員__：勾選此項目將指令碼套用到監督員 (背景工作角色) 節點

    將其他項目保留空白。
    > [AZURE.WARNING] 您也必須使用__公開金鑰__來保護 HDInsight 叢集的 SSH 使用者，才能使用 Streamparse 從遠端部署。
    >
    > 如需有關在 HDInsight 上搭配使用密碼與 SSH 的詳細資訊，請參閱下列其中一份文件：
    >
    > * [使用 SSH 與以 Linux 為基礎的 HDInsight，從 Linux、 Unix 或 OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [使用 SSH 與以 Linux 為基礎的 HDInsight，從 Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 佈建叢集時，使用下列命令在開發環境上安裝 Streamparse：

        pip install streamparse

3. 安裝 Streamparse 之後，使用下列命令建立範例專案：

        sparse quickstart wordcount

    這會建立新目錄 `wordcount`, ，並填入範例字數統計專案。

4. 將目錄切換到 `wordcount` 目錄並開始在本機模式中的拓撲:

        cd wordcount
        sparse run

    使用 Ctrl+C 可停止拓撲。

### 部署拓撲

建立以 Linux 為基礎的 HDInsight 叢集之後，請使用下列步驟將叢集部署至拓撲：

1. 使用下列命令尋找叢集的背景工作角色節點的完整網域名稱：

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'

    這會擷取叢集的主機資訊、傳送到 grep，然後傳回背景工作角色節點的項目。 您應該會看到類似下面的結果：

        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"

    儲存 `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"` 資訊，因為它將用於下一個步驟。

2. 開啟 __config.json__ 檔案中 `wordcount` 目錄中，並變更下列項目:

    * __user__：將此項目設為您為 HDInsight 叢集設定的 SSH 使用者帳戶名稱。 這會在部署專案時用來驗證叢集
    * __nimbus__: 設為 `CLUSTERNAME-ssh.azurehdinsight.net`。 將 CLUSTERNAME 取代為叢集名稱。 與 Nimbus 節點 (叢集的前端節點) 通訊時會使用此項目
    * __workers__：將您使用 curl 擷取的背景工作角色節點的主機名稱寫入 workers 項目中。 例如：

        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```

    * __virtualenv\_root__: 設為"/ virtualenv 」

    這會設定您的 HDInsight 叢集的專案包括 `/virtualenv` 由指令碼動作在佈建期間建立的目錄。

4. 因為 Streamparse 在 HDInsight 上部署需要轉送到背景工作，您透過前端節點的驗證 `ssh` 必須啟動您的工作站上。 在大多數的作業系統上，它會自動啟動。 使用下列命令確認它正在執行：

        echo "$SSH_AUTH_SOCK"

    這會將回應傳回類似下列的 if `ssh` 正在執行:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    > [AZURE.NOTE] 根據您的作業系統，完整路徑可能有所不同。 例如，在 OS X 上的路徑可能如下所示 `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`。 但是如果代理程式正在執行，應該會傳回某些路徑。

    如果沒有傳回任何內容，使用 `ssh` 命令啟動代理程式。

5. 確認代理程式知道您用來向 HDInsight 伺服器驗證的金鑰。 使用下列命令列出代理程式可用的金鑰：

        ssh-add -L

    這會傳回已新增至代理程式的私密金鑰。 您可以比較這些結果，與您建立 SSH 金鑰來向 HDInsight 驗證時所產生之私密金鑰的內容。

    如果未傳回任何資訊，或傳回的資訊不符合您的私密金鑰，請使用下列命令將私密金鑰新增至代理程式：

        ssh-add /path/to/key/file

    例如， `ssh 新增 ~/.ssh/id_rsa`。

4. 您也必須設定 SSH，讓它知道應該將轉送用於 HDInsight 叢集。 將下列內容加入 `~/.ssh/config`。 如果此檔案不存在，請使用下列內容建立檔案：

     Host *.azurehdinsight.net
       ForwardAgent yes
    
     Host *.internal.cloudapp.net
       ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p

 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

 這會在工作站上設定 SSH 代理程式，以支援透過您連接的任何 *.azurehdinsight.net 系統來轉送 SSH 認證。 在此案例中，就是您的叢集的前端節點。 接下來，它會設定命令將前端節點的 SSH 流量代理到個別的背景工作角色節點 (internal.cloudapp.net)。 這可讓 Streamparse 連接至前端節點，再從那裡連接至每個背景工作角色節點 (對 SSH 帳戶使用金鑰驗證)。

5. 最後，使用下列命令將拓撲從本機開發環境提交至 HDInsight 叢集：

        sparse submit

    這將會連接到 HDInsight 叢集、部署拓撲和任何 Python 相依性，然後啟動拓撲。

## 後續步驟

本文件中，您已了解如何從 Storm 拓撲中使用 Python 元件。 請參閱下列文件，了解搭配使用 Python 與 HDInsight 的其他方式。

* [如何使用 Python 串流 MapReduce 工作](hdinsight-hadoop-streaming-python.md)
* [如何使用 Python 使用者定義函數 (UDF) 中 Pig 和 Hive](hdinsight-python.md)




