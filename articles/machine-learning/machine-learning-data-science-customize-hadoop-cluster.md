<properties 
    pageTitle="針對 Cortana 分析程序自訂 Hadoop 叢集 | Microsoft Azure" 
    description="自訂 Azure HDInsight Hadoop 叢集中提供熱門 Python 模組。"
    services="machine-learning" 
    documentationCenter="" 
    authors="hangzh-msft" 
    manager="paulettm" 
    editor="cgronlun"  />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/13/2015" 
    ms.author="hangzh;bradsev" />

# 針對 Cortana 分析程序自訂 Azure HDInsight Hadoop 叢集 

此功能表會連結至說明如何設定 Cortana 分析程序 (CAP) 所用的各種資料科學環境的主題。

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

本文將說明若將 HDInsight Hadoop 叢集佈建於 HDInsight 服務中，如何藉由在每個節點上安裝 64 位元的 Anaconda (Python 2.7) 來自訂該叢集。 這項自訂會準備要搭配 Cortana 分析程序使用的叢集。 它也會示範如何存取前端節點，以將自訂工作提交至叢集。

這項自訂讓許多熱門的 Python 模組 (隨附於 Anaconda) 非常方便地在使用者定義函式 (UDF) 中使用，這類函式是設計來處理叢集中的 Hive 記錄。 如需在此案例中使用的程序的指示，請參閱 [Hive 查詢提交至 HDInsight Hadoop 叢集的進階的分析程序](machine-learning-data-science-hive-queries.md)。


## <a name="customize"></a>自訂 Azure HDInsight Hadoop 叢集

若要建立自訂的 HDInsight Hadoop 叢集，使用者必須登入 [**傳統 Azure 入口網站**](https://manage.windowsazure.com/), ，按一下 [ **新增** 在左下角，然後選取資料服務]-> [HDINSIGHT]-> [ **自訂建立** 帶出 **叢集詳細資料** 視窗。 

![建立工作區][1]

在設定頁面 1 上輸入要建立的叢集名稱，並接受其他欄位的預設值。 按一下箭號，前往下一個設定頁面。 

![建立工作區][2]

在組態頁面 2 輸入的數字 **資料節點**, ，請選取 **區域/虛擬網路**, ，選取的大小 **前端節點** 和 **資料節點**。 按一下箭號，以前往下一個設定頁面。

>[AZURE.NOTE]  **區域/虛擬網路** 必須為即將用於 HDInsight Hadoop 叢集的儲存體帳戶區域相同。 否則，在第四個組態頁面中，使用者想要使用的儲存體帳戶不會出現在下拉式清單中的 **帳戶名稱**。

![建立工作區][3]

在設定頁面 3 上，提供適用於 HDInsight Hadoop 叢集的使用者名稱和密碼。 **不這麼做** 選取 _輸入 Hive/Oozie Metastore_。 然後按一下箭號，前往下一個設定頁面。 

![建立工作區][4]

在設定頁面 4 上，指定儲存體帳戶名稱，以及 HDInsight Hadoop 叢集的預設容器。 如果使用者選取 _建立預設容器_ 中 **預設容器** 下拉式清單中有相同名稱的容器，將會建立叢集。 按一下箭號，前往最後一個設定頁面。

![建立工作區][5]

在最後 **指令碼動作** 組態] 頁面上，按一下 [ **加入指令碼動作** ] 按鈕，然後使用下列值填入文字欄位。
 
* **名稱** -任何字串做為此指令碼動作的名稱。 
* **節點型別** -選取 **所有節點**。 
* **指令碼 URI** - *http://getgoing.blob.core.windows.net/publicscripts/Azure_HDI_Setup_Windows.ps1* 
    * *publicscripts* 是儲存體帳戶中的公用容器 
    * *getgoing* 來共用 PowerShell 指令碼檔案，以協助使用者在 Azure 中的工作。 
* **參數** -(保留空白)

最後，按一下勾號，開始建立自訂的 HDInsight Hadoop 叢集。 

![建立工作區][6]

## <a name="headnode"></a> 存取 Hadoop 叢集的前端節點

使用者必須先在 Azure 中啟用 Hadoop 叢集的遠端存取，才能透過 RDP 存取 Hadoop 叢集的前端節點。 

1. 登入 [**傳統 Azure 入口網站**](https://manage.windowsazure.com/), ，請選取 **HDInsight** 的左側，請從叢集清單中選取您的 Hadoop 叢集，請按一下 **組態** 索引標籤，然後再按一下 **啟用遠端** 在頁面底部的圖示。
    
    ![建立工作區][7]

2. 在 **設定遠端桌面** ] 視窗中，輸入使用者名稱和密碼] 欄位中，然後選取 [遠端存取的到期日。 接著，按一下勾號，啟用對 Hadoop 叢集前端節點的遠端存取。
    
    >[AZURE.NOTE] 
    >
    >1. 適用於遠端存取的使用者名稱和密碼並非您在建立 Hadoop 叢集時所使用的使用者名稱和密碼。 它們是一組個別的認證
    >
    >2. 遠端存取的到期日必須是從目前日期起算的 7 天內。

    ![建立工作區][8]

3. 啟用遠端存取之後，請按一下 **連接** ，遠端連至前端節點] 頁面的底部。 您登入 Hadoop 叢集前端節點的方式是針對稍早指定的遠端存取使用者輸入認證。

     ![建立工作區][9]

進階的分析程序的下一個步驟中的對應 [學習指南: 進階資料處理，在 Azure 中](machine-learning-data-science-advanced-data-processing.md) ，而且可能包括將資料移至 HDInsight，程序並進行取樣，並在與 Azure Machine Learning 從資料學習的準備步驟。

請參閱 [Hive 查詢提交至 HDInsight Hadoop 叢集的進階的分析程序](machine-learning-data-science-process-hive-tables.md) 如需如何存取之 Python 模組的隨附於 Anaconda 從前端節點叢集的使用者定義函數 (Udf)，用來處理儲存在叢集中之 Hive 記錄。

[1]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img1.png
[2]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img2.png
[3]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img3.png
[4]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img4.png
[5]: ./media/machine-learning-data-science-customize-hadoop-cluster/customize-cluster-img5.png
[6]: ./media/machine-learning-data-science-customize-hadoop-cluster/script-actions.png
[7]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-1.png
[8]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-2.png
[9]: ./media/machine-learning-data-science-customize-hadoop-cluster/enable-remote-access-3.png

 
