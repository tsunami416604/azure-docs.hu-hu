---
title: "Sqoop feladatok futtatásához a .NET és a HDInsight - Azure használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan használhatja a HDInsight .NET SDK futtatása Sqoop importálása és exportálása a Hadoop fürtök és az Azure SQL-adatbázis között."
keywords: sqoop feladat
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jgao
ms.openlocfilehash: 27bd93896718bf54ea3d483671b11070e8c274ec
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>A hdinsight Hadoop .NET SDK használatával Sqoop feladatok futtatása
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

További tudnivalók az Azure HDInsight .NET SDK használata a Hdinsightban történő importálására és exportálására HDInsight-fürtök és egy Azure SQL database vagy az SQL Server-adatbázis közötti Sqoop feladatok futtatásához.

> [!NOTE]
> Bár ebben a cikkben ismertetett sem a Windows-alapú vagy Linux-alapú HDInsight-fürtöt, csak a Windows ügyfél működnek. Egyéb módszerek kiválasztásához használja a lap választó Ez a cikk tetején.
> 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez a következő elemet kell tartalmaznia:

* Hdinsight Hadoop-fürthöz. További információkért lásd: [fürt és az SQL-adatbázis létrehozása](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Sqoop használja a .NET SDK-val a HDInsight-fürtök
A HDInsight .NET SDK biztosít a .NET ügyféloldali kódtáraknál, úgy, hogy egyszerűbbé teszik a .NET-HDInsight-fürtök. Ebben a szakaszban hozzon létre egy C#-konzolalkalmazást a hivesampletable exportálása az Azure SQL Database-táblázatot, amely ebben az oktatóanyagban korábban létrehozott.

## <a name="submit-a-sqoop-job"></a>Sqoop feladat elküldése

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.

2. A Visual Studio Csomagkezelő konzolról a csomag importálása a következő NuGet-parancs futtatásával:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. A következő kódot használja a Program.cs fájlban:
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
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
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. A program futtatásához jelölje ki a **F5** kulcs. 

## <a name="limitations"></a>Korlátozások
Linux-alapú HDInsight mutatja be a következő korlátozások vonatkoznak:

* Tömeges exportálás: A Sqoop összekötő használt Microsoft SQL Server vagy az Azure SQL Database adatainak exportálása jelenleg nem támogatja a tömeges beszúrások.

* Kötegelés: használatával a `-batch` kapcsoló mikor Beszúrások végez, a Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="next-steps"></a>Következő lépések
Most megtanulhatta, hogyan használható a Sqoop. További tudnivalókért lásd:

* [Oozie használata a HDInsight](../hdinsight-use-oozie.md): egy Oozie munkafolyamat használja Sqoop műveletét.
* [HDInsight eszközzel repülési késleltetés adatok elemzése](../hdinsight-analyze-flight-delay-data.md): használja struktúra elemzése repülési késleltetés az adatok, és a Sqoop segítségével exportál adatokat az Azure SQL-adatbázis.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): található adatok feltöltése a HDInsight- vagy Azure Blob Storage más módszerrel.

