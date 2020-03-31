---
title: Apache Sqoop-feladatok futtatása a .NET és a HDInsight használatával – Azure
description: Ismerje meg, hogyan futtathatja a HDInsight .NET SDK használatával az Apache Sqoop importálását és exportálását egy Apache Hadoop-fürt és egy Azure SQL-adatbázis között.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157066"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Apache Sqoop-feladatok futtatása a .NET SDK apache hadoop-hoz a HDInsightban

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan futtathatja az Azure HDInsight .NET SDK használatával az Apache Sqoop-feladatok futtatását a HDInsight-fürtök és egy Azure SQL Database vagy SQL Server adatbázis között.

## <a name="prerequisites"></a>Előfeltételek

* A [Tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) az [Apache Sqoop használata a Hadoop segítségével a HDInsight ban](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* A Sqoop ismerete. További információ: [Sqoop User Guide](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Sqoop használata HDInsight-fürtökön a .NET SDK-val

A HDInsight .NET SDK .NET ügyféltárakat biztosít, így könnyebben dolgozhat a .NET-ről származó HDInsight-fürtökkel. Ebben a szakaszban hozzon létre egy `hivesampletable` C# konzolalkalmazást az előfeltételekből létrehozott Azure SQL Database-táblába való exportáláshoz.

## <a name="set-up"></a>Beállítás

1. Indítsa el a Visual Studio alkalmazást, és hozzon létre egy C# konzolalkalmazást.

1. Nyissa meg az **Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolját,** és futtassa a következő parancsot:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop export

A Hive-tól az SQL Serverkiszolgálóig.  Ez a példa exportálja `hivesampletable` az `mobiledata` adatokat a Hive táblából az SQL Database táblájába.

1. Használja a következő kódot a Program.cs fájlban. A kód szerkesztése a `ExistingClusterName`és `ExistingClusterPassword`a érték értékének beállításához.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. A program futtatásához válassza az **F5** billentyűt.

## <a name="sqoop-import"></a>Sqoop import

Az SQL Servertől az Azure Storage-ig. Ez a példa attól függ, hogy a fenti exportálás történt-e.  Ez a példa `mobiledata` adatokat importál az `wasb:///tutorials/usesqoop/importeddata` SQL Database táblájából a fürt alapértelmezett tárfiókjának könyvtárába.

1. Cserélje le a `//sqoop start //sqoop end` fenti kódot a blokkban a következő kódra:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. A program futtatásához válassza az **F5** billentyűt.

## <a name="limitations"></a>Korlátozások

A Linux-alapú HDInsight a következő korlátozásokat mutatja be:

* Tömeges exportálás: A Sqoop-összekötő, amely adatok Microsoft SQL Server vagy Azure SQL Database exportálására szolgál, jelenleg nem támogatja a tömeges beszúrásokat.

* Kötegelés: A `-batch` kapcsoló használatával a Sqoop több lapkát hajt végre a lapkaműveletek kötegelése helyett.

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan kell használni Sqoop. További tudnivalókért lásd:

* [Az Apache Oozie használata a HDInsight segítségével:](../hdinsight-use-oozie-linux-mac.md)Sqoop művelet használata Oozie-munkafolyamatokban.
* [Adatok feltöltése a HDInsightba:](../hdinsight-upload-data.md)Más módszereket is kereshet az adatok HDInsightba vagy az Azure Blob storage-ba való feltöltéséhez.
