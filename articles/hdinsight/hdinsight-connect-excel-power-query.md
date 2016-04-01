<properties
    pageTitle="使用 Power Query 將 Excel 連接到 Hadoop | Microsoft Azure"
    description="了解如何利用商業智慧元件和使用 Power Query for Excel 來存取 HDInsight 上的 Hadoop 中儲存的資料。"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/25/2015"
    ms.author="jgao"/>


#使用 Power Query 將 Excel 連接到 Hadoop

Microsoft 巨量資料方案的主要功能之一，是將 Microsoft 商業智慧 (BI) 元件與 Azure HDInsight 上的 Hadoop 叢集相整合。 舉例來說，此整合可讓您使用 Microsoft Power Query for Excel 增益集，將 Excel 連接到包含 Hadoop 叢集相關聯資料的 Azure 儲存體帳戶。 本文將逐步解說如何設定及使用 Power Query，以查詢受 HDInsight 管理的 Hadoop 叢集相關聯資料。

> [AZURE.NOTE] 雖然您可以使用這份文件中的步驟與 Linux 或 Windows 為基礎的 HDInsight 叢集，Windows 就會是用戶端工作站需要有。

## 必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- **HDInsight 叢集**。 若要設定此叢集，請參閱 [開始使用 Azure HDInsight][hdinsight-get-started]。
- **工作站** 執行 Windows 7、 Windows Server 2008 R2 或更新版本的作業系統。
- **Office 2013 Professional Plus、 Office 365 ProPlus，Excel 2013 Standalone 或 Office 2010 Professional Plus**。


## <a id="InstallPowerQuery"></a>安裝 Microsoft Power Query for Excel

Power Query 可讓您將各種來源的資料匯入 Microsoft Excel 中，以輔助 BI 工具 (例如 PowerPivot 和 Power View) 的運作。 特別是，Power Query 可匯入在 HDInsight 叢集上執行的 Hadoop 工作所匯出或產生的資料。

下載 Microsoft Power Query for Excel [Microsoft 下載中心][powerquery-download] 並安裝它。

## <a id="ImportData"></a>將 HDInsight 資料匯入 Excel 中

Power Query add-in for Excel 可協助您將 HDInsight 叢集中的資料匯入至 Excel，以便使用 PowerPivot 和 Power Map 等 BI 工具來檢查、分析及呈現資料。

**從 HDInsight 叢集匯入資料**

1. 開啟 Excel。

2. 建立新的空白活頁簿。

3. 按一下 [ **Power Query** ] 功能表上，按一下 [ **從 Azure**, ，然後按一下 [ **從 Microsoft Azure HDInsight**。

    ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **注意 ︰** 如果您沒有看到 **Power Query** ] 功能表上，移至 **檔案** > **選項** > **增益集**, ，然後選取 **COM 增益集** 從下拉式清單 **管理** 在頁面底部的方塊。 選取 **移至...** 按鈕，並確認已核取方塊 Power Query for Excel 增益集。

    **注意 ︰** Power Query 也可讓您按一下 [匯入資料從 HDFS **從其他來源**。

3. 如 **帳戶名稱**, ，輸入您的叢集相關聯的 Azure Blob 儲存體帳戶名稱，然後按一下 **確定**。

4. 如 **帳戶金鑰**, ，輸入 Blob 儲存體帳戶的金鑰，然後按一下 **儲存**。 (只有在第一次存取此存放區時需要執行此動作。)

5. 在 **導覽** 窗格左側的 [查詢編輯器中，按兩下 Blob 儲存體容器名稱。 依預設，容器名稱與叢集名稱相同。

6. 找出 **HiveSampleData.txt** 中 **名稱** 資料行 (資料夾路徑是 **.../ hive/倉儲/hivesampletable/**)，然後按一下 [ **二進位** HiveSampleData.txt 左側。

    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 如有需要，您可以將欄名稱重新命名。 當您準備好時，按一下 [ **Apply & Close**。

    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>接續步驟

在本文中，您已了解到如何使用 Power Query 將 HDInsight 中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 中的資料擷取至 Azure SQL Database。 此外也可以將資料上傳至 HDInsight。 若要深入了解，請參閱下列文章：

* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][hdinsight-ODBC]
* [將資料上傳到 HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689


