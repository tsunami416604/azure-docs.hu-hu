<properties
   pageTitle="使用 Apache Storm 和 HBase 分析感應器資料 | Microsoft Azure"
   description="了解如何透過虛擬網路連線至 Apache Storm。 搭配使用 Storm 和 HBase 來處理事件中樞的感應器資料，並使用 D3.js 將其視覺化呈現。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>

# 在 HDInsight (Hadoop) 中使用 Apache Storm、事件中樞和 HBase 分析感應器資料

了解如何使用 Apache Storm on HDInsight 處理 Azure 事件中樞的感應器資料，並使用 D3.js 將其視覺化呈現。 本文件也將說明如何透過 Azure 虛擬網路，將 Storm on HDInsight 與 HBase on HDInsight 連線，並將拓撲的資料儲存至 HBase。

> [AZURE.NOTE] 這份文件中的資訊根據使用 Windows 架構的 Storm on HDInsight 叢集。 如需使用以 Linux 為基礎的 Storm on HDInsight 從 Azure 事件中樞資訊，請參閱 [處理 Azure 事件中心與 Storm on HDInsight 的事件](hdinsight-storm-develop-java-event-hub-topology.md)

## 必要條件

* Azure 訂閱。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* [Apache Storm on HDInsight 叢集](../hdinsight-storm-getting-started.md)

* [Node.js](http://nodejs.org/)︰ 用於 web 儀表板，並將傳送至事件中樞的感應器資料。

* [Java 和 JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] 也可以透過 Java、 JDK、 Maven 和 Git 是 [Chocolatey NuGet](http://chocolatey.org/) 封裝管理員。

## 架構

![架構圖表](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

此範例由下列元件組成：

* **Azure 事件中樞**︰ 提供收集自感應器的資料。 在此範例中，我們提供的是應用程式產生的假資料。

* **Storm on HDInsight**︰ 從事件中心提供即時處理資料。

* **在 HDInsight 上的 HBase** （選用） ︰ 提供持續的 NoSQL 資料存放區。

* **Azure 虛擬網路服務** (選擇性，如果使用 HBase 所需): Storm on HDInsight 及 HBase on HDInsight 叢集之間能夠安全通訊。

* **儀表板網站**︰ 建立即時資料圖表的範例儀表板。

    * 此網站採用 Node.js，因此可在任何用戶端作業系統上測試，或者也能部署至 Azure 網站。

    * [Socket.io](http://socket.io/) 用於 Storm 拓撲及網站之間的即時通訊。

        > [AZURE.NOTE] 這是實作詳細資料。 您可以使用任何通訊架構，例如原始 WebSockets 或 SignalR。

    * [D3.js](http://d3js.org/) 用來傳送到網站的資料。

拓撲從事件中心讀取資料，使用 **com.microsoft.eventhubs.spout.EventHubSpout** Storm on HDInsight 叢集所提供的類別。 此網站的通訊透過使用 [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java)。

（選擇性） 與 HBase 通訊透過使用 [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) 類別，提供 Storm 的一部分。

此拓撲的圖表如下。

![拓撲圖](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] 這是拓撲的非常簡化檢視。 執行過程中，系統會針對正在讀取之事件中樞的每個分割區，為各個元件建立執行個體。 這些執行個體會分散至叢集的節點上，而資料在節點間路由傳送的情形如下：
>
> * 從 Spout 傳送至剖析器的資料會經過負載平衡。
> * 從剖析器傳送至儀表板及 HBase (若有使用) 的資料會依照裝置識別碼分組，讓相同裝置的訊息一律傳送至相同元件。

### 元件

* **事件中樞 Spout**︰ 一部分提供的 spout [HDInsight Storm 範例](https://github.com/hdinsight/hdinsight-storm-examples) GitHub 上。

* **ParserBolt.java**: spout 所發出的資料為原始 JSON，並偶爾多個事件，就會發出一次。 此 Bolt 示範如何讀取 Spout 發出的資料，並將其以包含多個欄位之 Tuple 的形式發至新的串流。

* **DashboardBolt.java**︰ 這會示範如何使用 Socket.io 用戶端程式庫 java 將資料即時傳送至 web 儀表板。

## 準備您的環境

使用此範例之前，您必須建立 Azure 事件中樞供 Storm 拓撲讀取。 由於用以讀取事件中樞資料的元件只能透過叢集取得，因此您也必須建立 Storm on HDInsight 拓撲。

> [AZURE.NOTE] 最後會透過 Maven 取得事件中心 spout。

### 設定事件中樞

事件中樞是此範例的資料來源。 請使用下列步驟建立新的事件中心。

1. 從 [Azure 傳統入口網站](https://manage.windowsazure.com), ，請選取 **新增 |服務匯流排 |事件中心 |自訂建立**。

2. 在 **新增新的事件中心** 對話方塊方塊中，輸入 **事件中樞名稱**, ，請選取 **區域** 建立中樞，然後建立新的命名空間或選取現有的命名。 最後，按一下箭頭以繼續。

2. 在 **設定事件中樞** ] 對話方塊中，輸入 **分割計數** 和 **訊息保留** 值。 在此範例中，資料分割計數使用 10，訊息保留使用 1。

3. 建立事件中樞後，選取命名空間，然後選取 [ **事件中樞**。 最後，選取您稍早建立的事件中樞。

4. 選取 **設定**, ，然後使用下列資訊建立兩個新的存取原則。

    <table>
    <tr><th>名稱</th><th>權限</th></tr>
    <tr><td>裝置</td><td>傳送</td></tr>
    <tr><td>Storm</td><td>接聽</td></tr>
    </table>

    建立權限之後, 選取 **儲存** 在頁面底部的圖示。 這會建立共用存取原則，用以傳送訊息至此中樞，以及讀取此中樞的訊息。

5. 儲存原則之後, 使用 **共用存取金鑰產生器** 要擷取的索引鍵之頁面的底部 **裝置** 和 **storm** 原則。 妥善儲存這些金鑰，以便稍後使用。

### 建立 Storm on HDInsight 叢集

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2. 按一下 [ **HDInsight** 中左的窗格中，然後按一下 **+ 新增** 頁面左下角。

3. 按一下第二個資料行中的 HDInsight 圖示，然後選取 **自訂**。

4. 在 **叢集詳細資料** 頁面上，輸入新的叢集名稱，然後選取 **Storm** 的 **叢集類型**。 按一下箭頭以繼續。

5. 輸入的數字 1 **資料節點** 要用於此叢集。

    > [AZURE.NOTE] 若要使用這份文件的叢集成本降到最低，請減少 **叢集大小** 到 1，並於使用完畢之後刪除叢集。

6. 輸入系統管理員 **使用者名稱** 和 **密碼**, ，然後按一下箭頭以繼續。

4. 如 **儲存體帳戶**, ，請選取 **建立新的儲存體** 或選取現有的儲存體帳戶。 選取或輸入 **帳戶名稱** 和 **預設容器** 使用。 選取左下角的勾號圖示以建立 Storm 叢集。

## 下載並安裝 EventHubSpout

1. 下載 [HDInsight Storm 範例專案](https://github.com/hdinsight/hdinsight-storm-examples/)。 下載完成後，尋找 **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔案。

2. 從命令提示字元中，使用下列命令來安裝 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 檔案到本機 Maven 存放區。 這可讓您在稍後的步驟中，輕鬆將檔案加入 Storm 專案中做為參考。

        mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

## 下載並設定專案。

使用下列項目從 GitHub 下載專案。

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

命令執行完畢後，您會擁有以下目錄架構：

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this is the Java topology
            conf/
                Config.properties
                hbase-site.xml
            src/
            test/
            dashboard/ - this is the node.js web dashboard
            SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] 這份文件不討論要包含在此範例中，程式碼的完整詳細資料不過，完整註程式碼。

開啟 **Config.properties** 檔案，並加入您先前建立事件中樞時使用的資訊。 資訊新增完畢後，請儲存檔案。

    eventhubspout.username = storm

    eventhubspout.password = <the key of the 'storm' policy>

    eventhubspout.namespace = <the event hub namespace>

    eventhubspout.entitypath = <the event hub name>

    eventhubspout.partitions.count = <the number of partitions for the event hub>

    ## if not provided, will use storm's zookeeper settings
    ## zookeeper.connectionstring=localhost:2181

    eventhubspout.checkpoint.interval = 10

    eventhub.receiver.credits = 1024

## 編譯並在本機測試

測試前，您必須先開啟儀表板，檢視拓撲輸出的資料並產生要存放在事件中樞的資料。

### 啟動 Web 應用程式

1. 開啟新的命令提示字元或終端機，並將目錄變更至 **hdinsight-hdinsight-eventhub-example/dashboard**, ，然後使用下列命令安裝 web 應用程式所需的相依性 ︰

        npm install

2. 使用下列命令啟動 Web 應用程式：

        node server.js

    您應該會看見類似下方的訊息：

        Server listening at port 3000

2. 開啟網頁瀏覽器並輸入 **http://localhost:3000/ /** 做為位址。 您應該會看到如下所示的頁面：

    ![Web 儀表板](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

    保持開啟此命令提示字元或終端機。 測試完成後，使用 Ctrl-C 鍵中斷 Web 伺服器。

### 開始產生資料

> [AZURE.NOTE] 本章節中的步驟使用 Node.js，讓他們可以在任何平台上執行。 如需其他語言的範例，請參閱 **SendEvents** 目錄。


1. 開啟新的命令提示字元或終端機，並將目錄變更至 **hdinsight-hdinsight-eventhub-example/SendEvents/nodejs**, ，然後使用下列命令來安裝應用程式需要的相依性 ︰

        npm install

2. 開啟 **app.js** 檔案在文字編輯器中，並新增您稍早取得的事件中樞資訊 ︰

        // ServiceBus Namespace
        var namespace = 'servicebusnamespace';
        // Event Hub Name
        var hubname ='eventhubname';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'key';

2. 使用以下命令在事件中樞插入新項目：

        node app.js

    您應該會看見好幾行輸出資料，傳送至事件中樞的資料也會包含在內。 此資訊類似以下內容：

        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### 啟動拓撲

2. 使用下列命令在本機上啟動拓撲：

    mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

    這樣會啟動拓撲，從事件中樞讀取檔案，然後將檔案傳送至 Azure 網站中執行的儀表板。 您應該會看見 Web 儀表板中出現資料行，如下所示：

    ![儀表板與資料](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. 儀表板執行時，請使用前幾個步驟的 `node app.js` 命令，將新的資料傳送至儀表板。 由於溫度值是隨機產生，因此圖形必須更新才能顯示新值。

3. 確定更新成功後，請按下 Ctrl+C 鍵停止拓撲。 若要停止 SendEvent 應用程式，請選取視窗並按任意鍵。 您也可以使用 Ctrl-C 鍵中斷 Web 伺服器。

## 封裝拓撲並部署到 HDInsight

在開發環境中，使用下列步驟在 HDInsight Storm 叢集上執行 Temperature 拓撲。

### 發佈網站儀表板。

1. 若要部署至 Azure 網站的儀表板，請依照下列中的步驟 [建置並部署 Node.js 網站至 Azure](../web-sites-nodejs-develop-deploy-mac.md)。 記下的網站，將會類似於 URL **mywebsite.azurewebsites.net**。

2. 建立網站時，請移至 Azure 傳統入口網站中的站台，並選取 **設定** ] 索引標籤。 啟用 **Web 通訊端**, ，然後按一下 [ **儲存** 頁面的底部。

2. 開啟 **hdinsight-eventhub-example\TemperatureMonitor\src\main\java\com\microsoft\examples\bolts\DashboardBolt.java** 並變更下列這一行以指向已發行的儀表板的 URL:

        socket = IO.socket("http://mywebsite.azurewebsites.net");

3. 儲存 **DashboardBolt.java** 檔案。

### 封裝並部署拓撲

1. 使用下列命令從專案建立 JAR 封裝：

        mvn package

    這會建立名為 **TemperatureMonitor-1.0-SNAPSHOT.jar** 中 **目標** 專案的目錄。

2. 請依照 [部署和管理 Storm 拓撲](hdinsight-storm-deploy-monitor-topology.md) 上傳，並使用 Storm on HDInsight 叢集上啟動拓撲 **Storm 儀表板**。

3. 拓撲啟動後，以瀏覽器開啟您發佈至 Azure 的網站，然後使用 `node app.js` 命令將資料傳送至事件中樞。 您應該會看見顯示資訊的 Web 儀表板更新。

    ![儀表板](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## 選擇性：使用 HBase

若要搭配使用 Storm 和 HBase，您必須先建立 Azure 虛擬網路，然後在網路中建立 Storm 及 HBase 叢集。

### 建立 Azure 虛擬網路 (選擇性)

如果您打算透過此範例使用 HBase，您必須建立內含 Storm on HDInsight 叢集和 HBase on HDInsight 叢集的 Azure 虛擬網路。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。

2. 在頁面底部，按一下 [ **+ 新增** > **網路服務** > **虛擬網路** > **快速建立**。

3. 輸入或選取下列值：

    - **名稱**︰ 虛擬網路的名稱。

    - **位址空間**︰ 選擇夠大，無法在叢集中所有節點提供位址的虛擬網路的位址空間。 否則，佈建作業將會失敗。

    - **最大 VM 計數**︰ 選擇其中一個最大的虛擬機器計數。

    - **位置**︰ 此位置必須與您將建立的 HBase 叢集相同。

    - **DNS 伺服器**︰ 本文使用 Azure 所提供的內部 DNS 伺服器，因此，您可以選擇 **無**。 同時也支援更多自訂 DNS 伺服器的進階網路設定。 如需詳細的指引，請參閱 [名稱解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

4. 按一下 [ **建立虛擬網路**。 新的虛擬網路名稱隨即會出現在清單中。 等到 [狀態] 欄顯示 **建立**。

5. 在主要窗格上，按一下您剛建立的虛擬網路。

6. 按一下頁面頂端 **儀表板**。

7. 在 **快速瀏覽**, ，記下 **VIRTUAL NETWORK ID**。 佈建 Storm 和 HBase 叢集時需要用到它。

8. 在頁面頂端，按一下 [ **設定**。

9. 預設子網路名稱是在頁面底部， **子網路 1**。 使用 **新增子網路** 按鈕來加入 **subnet-2**。 這些子網路中將存放 Storm 和 HBase 叢集。

    > [AZURE.NOTE] 在本文中，我們將使用叢集只有一個節點。 如果您要建立多節點叢集，您必須確認 **CIDR （位址計數）** 將用於叢集之子網路。 位址計數必須大於背景工作節點數量加上 7 的數目 (閘道器：2、前端節點：2、Zookeeper：3)。 例如，如果您需要 10 個節點的 HBase 叢集，那麼子網路的位址計數必須大於 17 (10 + 7)。 否則，部署作業將會失敗。
    >
    > 強烈建議您一個叢集只指定一個子網路。

11. 按一下 [ **儲存** 頁面的底部。

### 在虛擬網路上建立 Storm 及 HBase 叢集

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2. 按一下 [ **HDInsight** 中左的窗格中，然後按一下 **+ 新增** 頁面左下角。

3. 按一下第二個資料行中的 HDInsight 圖示，然後選取 **自訂**。

4. 在 **叢集詳細資料** 頁面上，輸入新的叢集名稱，然後選取 **Storm** 的 **叢集類型**。 按一下箭頭以繼續。

5. 輸入的數字 1 **資料節點** 要用於此叢集。 如 **區域/虛擬網路**, ，選取您稍早建立的 Azure 虛擬網路。 如 **虛擬網路子網路**, ，請選取 **子網路 1**。

    > [AZURE.NOTE] 若要使用這份文件的叢集成本降到最低，請減少 **叢集大小** 到 1，並於使用完畢之後刪除叢集。

6. 輸入系統管理員 **使用者名稱** 和 **密碼**, ，然後按一下箭頭以繼續。

4. 如 **儲存體帳戶**, ，請選取 **建立新的儲存體** 或選取現有的儲存體帳戶。 選取或輸入 **帳戶名稱** 和 **預設容器** 使用。 選取左下角的勾號圖示以建立 Storm 叢集。

5. 重複上述步驟來建立新的 **HBase** 叢集。 主要差異如下：

    * **叢集類型**︰ 選取 **HBase**

    * **虛擬網路子網路**︰ 選取 **子網路 2**

    * **儲存體帳戶**︰ 您應該使用一個 Storm 叢集使用的容器不同。

### 探索 HBase DNS 尾碼

若要從 Storm 叢集寫入 HBase，該 HBase 叢集必須使用完整網域名稱 (FQDN)。 請使用以下命令來探索此資訊：

    curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

在傳回的 JSON 資料，找出 **"host_name"** 項目。 這會包含叢集中節點的 FQDN，例如：

    ...
    "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
    ...

開頭的網域名稱的叢集名稱的部分是為 DNS 尾碼，例如 **mycluster.b1.cloudapp.net**。

### 啟用 HBase Bolt

1. 開啟 **hdinsight-eventhub-example\TemperatureMonitor\conf\hbase-site.xml** 並取代 `suffix` DNS 尾碼與下面這一行的項目先前取得的 HBase 叢集。 完成變更後，請儲存檔案。

        <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

    HBase Bolt 將藉此與 HBase 叢集通訊。

1. 開啟 **hdinsight-eventhub-example\TemperatureMonitor\src\main\java\com\microsoft\examples\bolts\ ** 在文字編輯器中，並取消註解下列程式碼行移除 `//` 開頭。 完成此項變更後，請儲存檔案。

        topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
          .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

    這樣會啟用 HBase bolt。

    > [AZURE.NOTE] 當您部署到 Storm 叢集，不時您會在本機測試，您應該只啟用 HBase bolt。

### HBase 與 Storm 資料

執行拓撲前，您必須備妥 HBase 以接受資料。

1. 使用遠端桌面連線至 HBase 叢集。

2. 從桌面上啟動 HDInsight 命令列，並輸入下列命令：

    cd %hbase_home%
    bin\hbase 介面

3. 在 HBase Shell 中輸入下列命令，以建立將儲存感應器資料的資料表：

    create 'SensorData', 'cf'

4. 輸入下列命令，以確認資料表不含任何資料：

    scan 'SensorData'

啟動 Storm 叢集上的拓撲並處理資料後，您可以再次利用 `scan 'SensorData'` 命令，確認該資料已經插入 HBase。


## 後續步驟

您現在已了解如何使用 Storm 來讀取事件中樞的資料，以及在外部儀表板上使用 SignalR 和 D3.js 顯示 Storm 的資訊。 若您採用選擇性步驟，也會了解如何在虛擬網路中設定 HDInsight，以及如何使用 HBase Bolt 在 Storm 拓撲和 HBase 之間通訊。

* 如需 Storm 拓撲搭配使用 HDinsight 的更多範例，請參閱：

    * [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)

* 如需 Apache Storm 的詳細資訊，請參閱  [Apache Storm](https://storm.incubator.apache.org/) 站台。

* 如需關於 HBase on HDInsight，請參閱 [HBase 與 HDInsight 概觀](hdinsight-hbase-overview.md)。

* 如需 Socket.io 的詳細資訊，請參閱 [socket.io](http://socket.io/) 站台。

* 如需 D3.js 的詳細資訊，請參閱 [D3.js-資料驅動型文件](http://d3js.org/)。

* 以 Java 建立拓撲的相關資訊，請參閱 [為 Apache Storm on HDInsight 開發 Java 拓撲](hdinsight-storm-develop-java-topology.md)。

* 以.NET 建立拓撲的相關資訊，請參閱 [Apache Storm on HDInsight 使用 Visual Studio 開發 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

[azure-portal]: https://manage.windowsazure.com/


