<properties urlDisplayName="Connect Excel to HDInsight" pageTitle="Connect Excel to Hadoop with the Hive ODBC Driver | Azure" metaKeywords="" description="Learn how to set up and use the Microsoft Hive ODBC driver for Excel to query data in an HDInsight cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Connect Excel to Hadoop with the Microsoft Hive ODBC Driver

One key feature of Microsoft's Big Data Solution is the integration of Microsoft Business Intelligence (BI) components with Apache Hadoop clusters that have been deployed by the Azure HDInsight. An example of this integration is the ability to connect Excel to the Hive data warehouse of an Hadoop cluster in HDInsight using the Microsoft Hive Open Database Connectivity (ODBC) Driver.

It is also possible to connect the data associated with an HDInsight cluster and other data sources, including other (non-HDInsight) Hadoop clusters, from Excel using the Microsoft Power Query add-in for Excel. For information on installing and using Power Query, see [Connect Excel to HDInsight with Power Query][Connect Excel to HDInsight with Power Query].

**Prerequisites**:

Before you begin this article, you must have the following:

-   An HDInsight cluster. To configure one, see [Get started with Azure HDInsight][Get started with Azure HDInsight].
-   A computer that is running Windows 8, Windows 7, Windows Server 2012, or Windows Server 2008 R2.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone, or Office 2010 Professional Plus.

## In this article

1.  [Install the Microsoft Hive ODBC Driver][Install the Microsoft Hive ODBC Driver]
2.  [Create a Hive ODBC Data Source][Create a Hive ODBC Data Source]
3.  [Import data into Excel from an HDInsight cluster][Import data into Excel from an HDInsight cluster]
4.  [Next steps][Next steps]

## <span id="InstallHiveODBCDriver"></span></a>Install the Microsoft Hive ODBC Driver

Download and install Microsoft Hive ODBC Driver from the [Download Center][Download Center].

This driver can be installed on 32-bit or 64-bit versions of Windows 7, Windows 8, Windows Server 2008 R2 and Windows Server 2012 and will allow connection to Azure HDInsight (version 1.6 and later) and Azure HDInsight Emulator (v.1.0.0.0 and later). You should install the version that matches the version of the application where you will be using the ODBC driver. For this tutorial, the driver will be used from Office Excel.

## <span id="CreateHiveODBCDataSource"></span></a>Create a Hive ODBC Data Source

The following steps show you how to create a Hive ODBC Data Source.

1.  From Windows 8, press the Windows key to open the Start screen, and then type **data sources**.
2.  Click **Set up ODBC Data sources (32-bit)** or **Set up ODBC Data Sources (64-bit)** depending on your Office version. If you are using Windows 7, choose **ODBC Data Sources (32 bit)** or **ODBC Data Sources (64 bit)** from **Administrative Tools**. This will launch the **ODBC Data Source Administrator** dialog.

    ![OBDC data source administrator][OBDC data source administrator]

3.  From User DNS, click **Add** to open the **Create New Data Source** wizard.
4.  Select **Microsoft Hive ODBC Driver**, and then click **Finish**. This will launch the **Microsoft Hive ODBC Driver DNS Setup** dialog.

5.  Type or select the following values:

    |------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Property**     | **Description**                                                                                                                                                                   |
    | Data Source Name | Give a name to your data source                                                                                                                                                   |
    | Host             | Enter <hdinsightclustername>.azurehdinsight.net. For example, myHDICluster.azurehdinsight.net                                                                                     |
    | Port             | Use **443**. (This port has been changed from 563 to 443.)                                                                                                                        |
    | Database         | Use **Default**.                                                                                                                                                                  |
    | Hive Server Type | Select **Hive Server 2**                                                                                                                                                          |
    | Mechanism        | Select **Azure HDInsight Service**                                                                                                                                                |
    | HTTP Path        | Leave it blank.                                                                                                                                                                   |
    | User Name        | Enter HDInsight cluster user username. This is the username created during the cluster provision process. If you used the quick create option, the default username is **admin**. |
    | Password         | Enter HDInsight cluster user password.                                                                                                                                            |

    There are some important parameters to be aware of when you click **Advanced Options**:

    |-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Use Native Query              | When it is selected, the ODBC driver will NOT try to convert TSQL into HiveQL. You shall use it only if you are 100% sure you are submitting pure HiveQL statements. When connecting to SQL Server or Azure SQL Database, you should leave it unchecked. |
    | Rows fetched per block        | When fetching a large amount of records, tuning this parameter may be required to ensure optimal performances.                                                                                                                                           |
    | Default string column length, 
      Binary column length,         
      Decimal column scale          | The data type lengths and precisions may affect how data is returned. They will cause incorrect information to be returned due to loss of precision and/or truncation.                                                                                   |

    ![Advanced options][Advanced options]

6.  Click **Test** to test the data source. When the data source is configured correctly, it shows *TESTS COMPLETED SUCCESSFULLY!*.
7.  Click **OK** to close the Test dialog. The new data source should now be listed on the **ODBC Data Source Administrator**.
8.  Click **OK** to exit the wizard.

## <span id="ImportData"></span></a>Import data into Excel from an HDInsight cluster

The steps below describe the way to import data from a hive table into an Excel workbook using the ODBC data source that you created in the steps above.

1.  Open a new or existing workbook in Excel.
2.  From the **Data** tab, click the **Get External Data** tile, click **From Other Data Sources**, and then click **From Data Connection Wizard** to launch the **Data Connection Wizard**.

    ![Open data connection wizard][Open data connection wizard]

3.  Select **ODBC DSN** as the data source, and then click **Next**.
4.  From ODBC data sources, select the data source name that you created in the previous step, and then click **Next**.
5.  Re-enter the password for the cluster in the wizard, and then click **Test** to verify the configuration
6.  Click **OK** to close the test dialog.
7.  Click **OK**. Wait for the **Select Database and Table** dialog to open. This can take a few seconds.
8.  Select the table that you want to import, and then click **Next**. The *hivesampletable* is a sample hive table that comes with HDInsight clusters. You can choose it if you haven't created one. For more information on run Hive queries and create Hive tables, see [Use Hive with HDInsight][Use Hive with HDInsight].
9.  Click **Finish**.
10. In the **Import Data** dialog, you can change or specify the query. To do so, click **Properties**. This can take a few seconds.
11. Click on the **Definition** tab, and then append **LIMIT 200** to the Hive select statement in the **Command text** textbox. The modification will limit the returned record set to 200.

    ![Connection Properties][Connection Properties]

12. Click **OK** to close the Connection Properties dialog.
13. Click **OK** to close the **Import Data** dialog.
14. Re-enter the password, and then click **OK**. It takes a few seconds before data gets imported to Excel.

## <span id="nextsteps"></span></a>Next steps

In this article you learned how to use the Microsoft Hive ODBC driver to retrieve data from the HDInsight Service into Excel. Similarly, you can retrieve data from the HDInsight Service into SQL Database. It is also possible to upload data into an HDInsight Service. To learn more, see:

-   [Analyze flight delay data using HDInsight][Analyze flight delay data using HDInsight]
-   [Upload Data to HDInsight][Upload Data to HDInsight]
-   [Use Sqoop with HDInsight][Use Sqoop with HDInsight]

  [Connect Excel to HDInsight with Power Query]: ../hdinsight-connect-excel-power-query/
  [Get started with Azure HDInsight]: ../hdinsight-get-started/
  [Install the Microsoft Hive ODBC Driver]: #InstallHiveODBCDriver
  [Create a Hive ODBC Data Source]: #CreateHiveODBCDataSource
  [Import data into Excel from an HDInsight cluster]: #ImportData
  [Next steps]: #nextsteps
  [Download Center]: http://go.microsoft.com/fwlink/?LinkID=286698
  [OBDC data source administrator]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
  [Advanced options]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
  [Open data connection wizard]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
  [Use Hive with HDInsight]: ../hdinsight-use-hive/
  [Connection Properties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
  [Analyze flight delay data using HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Upload Data to HDInsight]: ../hdinsight-upload-data/
  [Use Sqoop with HDInsight]: ../hdinsight-use-sqoop/
