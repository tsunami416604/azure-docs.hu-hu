---
title: "A HDInsight .NET SDK - Azure használatával Hive-lekérdezések futtatása |} Microsoft Docs"
description: "Útmutató a Hadoop-feladatok Azure HDInsight Hadoop HDInsight .NET SDK használatával való elküldéséhez."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 93649c51c6dd6c158e3870a2fa75d92ae4579b7e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>A HDInsight .NET SDK használatával Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan elküldeni a Hive-lekérdezéseket a HDInsight .NET SDK használatával. A C# programban elküldeni a Hive táblák listázása a Hive-lekérdezések írása, és az eredmények megjelenítéséhez.

> [!NOTE]
> A cikkben leírt lépéseket egy Windows ügyfél kell elvégezni. Kapcsolatos információkért használata a Linux, OS X vagy UNIX rendszerű ügyfélnél Hive használata a lap választó, a cikk tetején látható.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk megkezdése előtt rendelkeznie kell a következő elemek:

* **Hdinsight Hadoop-fürthöz**. Lásd: [hdinsight Linux-alapú Hadoop használatának megkezdésében](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > 2017. szeptember 15. frissítésétől a HDInsight .NET SDK csak adatszolgáltató Hive lekérdezés eredményeit az Azure Storage-fiókokat támogatja. Ha ez a példa egy HDInsight-fürt által használt Azure Data Lake Store elsődleges tárolóként használ, nem lehet lekérdezni a találatok között a .NET SDK használatával.

* **A Visual Studio 2013 vagy 2015/2017**.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása
A HDInsight .NET SDK biztosít a .NET ügyféloldali kódtáraknál, így azokat könnyebben működéséhez a .NET-HDInsight-fürtökkel. 

**Feladatok küldéséhez**

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.
2. A Nuget-Csomagkezelő konzolról futtassa a következő parancsot:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. A következő kód használható:

    ```csharp
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
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
                
                // Only Azure Storage accounts are supported by the SDK
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
    ```
4. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Kell, hogy az alkalmazás kimenete hasonló:

![HDInsight Hadoop Hive feladat kimenetére](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta rendelkezik többféle módon hozhat létre HDInsight-fürtöt. További tudnivalókért tekintse meg a következő cikkeket:

* [Ismerkedés az Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Hdinsight Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
* [Hdinsight Hadoop-fürtök kezelése az Azure-portál használatával](../hdinsight-administer-use-management-portal.md)
* [A HDInsight .NET SDK-referencia](https://msdn.microsoft.com/library/mt271028.aspx)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [Use Sqoop with HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Nem interaktív hitelesítéssel ellátott .Net HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


