<properties
    pageTitle="存取以 Linux 為基礎之 HDInsight 上的 Hadoop YARN 應用程式記錄 | Microsoft Azure"
    description="了解如何使用命令列和網頁瀏覽器存取以 Linux 為基礎之 HDInsight (Hadoop) 叢集上的 YARN 應用程式記錄檔。"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
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

# 存取以 Linux 為基礎之 HDInsight 上的 YARN 應用程式記錄

本文件說明如何存取已在 Azure HDInsight 的 Hadoop 叢集上完成的 YARN (Yet Another Resource Negotiator) 應用程式記錄。

> [AZURE.NOTE] 這份文件中的資訊是以 Linux 為基礎的 HDInsight 叢集的特定項目。 Windows 叢集上的資訊，請參閱 [存取 YARN 應用程式登入 Windows 為基礎的 HDInsight](hdinsight-hadoop-access-yarn-app-logs.md)

## 必要條件

* 以 Linux 為基礎的 HDInsight 叢集。

* 您必須 [建立 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md) 才能存取 ResourceManager 記錄 web UI。

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) 透過兩個不同的介面提供已完成之應用程式的相關泛型資訊，以及架構特定應用程式資訊。 具體而言：

* 儲存及擷取 HDInsight 叢集上泛型應用程式資訊的功能已在版本 3.1.1.374 或更新版本上啟用。
* Timeline Server 的架構特定應用程式資訊元件目前在 HDInsight 叢集上並未提供。

應用程式的相關泛型資訊包含下列類型的資料：

* 應用程式識別碼，應用程式的唯一識別碼
* 啟動應用程式的使用者
* 為完成應用程式而進行之嘗試的相關資訊
* 任何指定之應用程式嘗試所使用的容器

## <a name="YARNAppsAndLogs"></a>YARN 應用程式和記錄檔

YARN 藉由將資源管理從應用程式排程/監視分離，支援多種程式設計模型 (MapReduce 為其中之一)。 這透過全域 *ResourceManager* (RM)、 每個背景工作節點 *NodeManagers* (Nm)，以及每個應用程式 *主機* (AMs)。 每一應用程式 AM 會與 RM 交涉用來執行您應用程式的資源 (CPU、記憶體、磁碟、網路)。 RM 可授與這些資源，以授與 Nm 搭配 *容器*。 AM 則是負責追蹤 RM 指派給它之容器的進度。 視應用程式的本質而定，一個應用程式可能會需要許多容器。

此外，每個應用程式可能包含多個 *應用程式嘗試* 才能完成時當機，或因 AM 之間的通訊和 RM 因此，容器是被授與應用程式的特定嘗試。 就某方面來說，容器會提供 YARN 應用程式所執行之基本工作單位的內容，而在容器的內容中完成的所有工作，都是在配置給該容器的單一背景工作節點上執行。 請參閱 [YARN 概念][YARN-concepts] 供進一步的參考。

應用程式記錄檔 (和關聯的容器記錄檔) 在對有問題的 Hadoop 應用程式進行偵錯上相當重要。 YARN 提供良好的架構，以收集、 彙總及儲存應用程式記錄檔與 [記錄檔彙總][log-aggregation] 功能。 「記錄檔彙總」功能讓存取應用程式記錄檔更具確定性，因為它會彙總背景工作節點上所有容器的記錄檔，並在應用程式完成之後，將它們以每一背景工作節點一個彙總記錄檔方式儲存在預設檔案系統上。 您的應用程式可能使用數百或數千個容器，但在單一工作者節點上執行之所有容器的記錄檔將一律彙總成單一檔案，也就是為您應用程式所使用的每一背景工作節點產生一個記錄檔。 「記錄檔彙總」在 HDInsight 叢集 (3.0 版和更新版本) 上預設為啟用，而在您叢集的預設容器中，即可找到彙總的記錄檔，位置如下：

    wasb:///app-logs/<user>/logs/<applicationId>

在該位置， *使用者* 是啟動應用程式的使用者名稱和 *applicationId* 是 YARN RM 所指派的應用程式的唯一識別碼

彙總的記錄檔是無法直接讀取，因為它們會以 [TFile][T-file], ，[二進位格式][binary-format] 由容器編制。 您必須使用 YARN ResourceManager 記錄或 CLI 工具，以檢視關注之應用程式或容器的純文字記錄。 

##YARN CLI 工具

若要使用 YARN CLI 工具，您必須先使用 SSH 連接到 HDInsight 叢集。 如需搭配 HDInsight 使用 SSH 的資訊，請參閱下列其中一個文件：

- [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

- [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)
    
您可以執行下列其中一個命令，以檢視這些純文字記錄：

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    
執行這些命令時，您必須指定 &lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId> 及 &ltworker-node-address> 資訊。

##YARN ResourceManager UI

YARN ResourceManager UI 叢集前端節點上執行，而且可以存取透過 Ambari web UI。不過，您必須先 [建立 SSH 通道](hdinsight-linux-ambari-ssh-tunnel.md) 才能存取 ResourceManager UI。

當您建立 SSH 通道後，請使用下列步驟來檢視 YARN 記錄：

1. 在網頁瀏覽器，瀏覽至 https://CLUSTERNAME.azurehdinsight.net。 將 CLUSTERNAME 取代為 HDInsight 叢集的名稱。

2. 從左邊的服務清單中，選取 __YARN__。

    ![選取的 Yarn 服務](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. 從 __快速連結__ 下拉式清單中，選取其中一個叢集前端節點，然後選取 __ResourceManager 記錄__。

    ![Yarn 快速連結](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
    
    您會看到一份 YARN 記錄的連結清單。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/


