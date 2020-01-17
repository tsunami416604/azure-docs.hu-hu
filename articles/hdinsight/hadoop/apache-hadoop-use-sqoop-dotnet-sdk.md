---
title: Apache Sqoop-feladatok futtatása a .NET és a HDInsight-Azure használatával
description: Ismerje meg, hogyan futtathatja az Apache Sqoop importálását és exportálását a HDInsight .NET SDK használatával egy Apache Hadoop-fürt és egy Azure SQL Database között.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157066"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Apache Sqoop-feladatok futtatása a HDInsight .NET Apache Hadoop SDK használatával

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogy az Azure HDInsight .NET SDK-val hogyan futtathat Apache Sqoop-feladatokat a HDInsight-ben egy HDInsight-fürt és egy Azure SQL Database vagy SQL Server-adatbázis közötti importáláshoz és exportáláshoz.

## <a name="prerequisites"></a>Előfeltételek

* A [tesztkörnyezet üzembe](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) állításának befejezése az [Apache Sqoop és a Hadoop használatával a HDInsight-ben](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Az Sqoop ismerete. További információ: [Sqoop felhasználói útmutató](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>A Sqoop használata HDInsight-fürtökön a .NET SDK-val

A HDInsight .NET SDK .NET-ügyfélszoftvereket biztosít, így könnyebben használható a .NET-alapú HDInsight-fürtökkel. Ebben a szakaszban egy olyan C# konzol-alkalmazást hoz létre, amely a `hivesampletable` az előfeltételekből létrehozott Azure SQL Database táblába exportálja.

## <a name="set-up"></a>Beállítás

1. Indítsa el a Visual studiót C# , és hozzon létre egy Console-alkalmazást.

1. Navigáljon az **eszközök** > **NuGet Package Manager** > **csomagkezelő konzolra** , és futtassa a következő parancsot:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop-exportálás

A kaptárból a SQL Serverba.  Ez a példa a struktúra `hivesampletable` táblázat adatait exportálja a SQL Database `mobiledata` táblájába.

1. Használja a következő kódot a Program.cs fájlban. Szerkessze a kódot `ExistingClusterName`és `ExistingClusterPassword`értékének megadásához.

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

## <a name="sqoop-import"></a>Sqoop importálása

SQL Server az Azure Storage-ba. Ez a példa a fenti exportálási művelettől függ.  Ez a példa a SQL Database `mobiledata` táblából importálja az adatait a fürt alapértelmezett Storage-fiókjának `wasb:///tutorials/usesqoop/importeddata` könyvtárába.

1. Cserélje le a fenti kódot a `//sqoop start //sqoop end` blokkban a következő kódra:

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

* Tömeges exportálás: a Sqoop-összekötő, amely az adatexportálás Microsoft SQL Server vagy Azure SQL Database jelenleg nem támogatja a tömeges beszúrásokat.

* Kötegelt feldolgozás: a `-batch` kapcsoló használatával a Sqoop több beszúrást hajt végre az INSERT művelet végrehajtása helyett.

## <a name="next-steps"></a>Következő lépések

Most már megtanulta, hogyan használhatja a Sqoop-t. További tudnivalókért lásd:

* Az [Apache Oozie és a HDInsight használata](../hdinsight-use-oozie-linux-mac.md): Sqoop művelet használata Oozie-munkafolyamatokban.
* [Adatok feltöltése a HDInsight-be](../hdinsight-upload-data.md): további módszereket talál az adatok HDInsight vagy Azure Blob Storage-ba való feltöltéséhez.
