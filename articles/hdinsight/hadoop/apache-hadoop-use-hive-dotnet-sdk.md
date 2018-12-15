---
title: HDInsight .NET SDK-t – az Azure az Apache Hive-lekérdezések futtatása
description: Útmutató a HDInsight .NET SDK használata Azure HDInsight az Apache Hadoop Apache Hadoop-feladatok elküldése.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: d3c9c8eab0ce13aee5dbe7bdb732d45555e547fb
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407167"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK használatával, az Apache Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan lehet elküldeni az Apache Hive-lekérdezéseket a HDInsight .NET SDK használatával. C# program írása elküldeni a Hive-lekérdezést futtathat Hive-táblák listázásához, és megjeleníti az eredményeket.

> [!NOTE]  
> A jelen cikkben ismertetett lépések egy Windows ügyfél kell elvégezni. Információ a Hive használata a Linux, OS X or Unix-ügyfél használatával a cikk tetején látható lapon választómezőt használja.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk elkezdéséhez a következőkkel kell rendelkeznie:

* **A HDInsight Apache Hadoop-fürt**. Lásd: [HDInsight Linux-alapú Hadoop használatának első lépései](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]  
    > 2017. szeptember 15. a HDInsight .NET SDK vracející Hive lekérdezés eredményeit az Azure Storage-fiókok csak támogatja. Ebben a példában egy HDInsight-fürtöt, amely az Azure Data Lake Storage használja elsődleges tárolóként használja, ha nem lehet lekérdezni a keresési eredmények a .NET SDK használatával.

* **Visual Studio 2013/2015/2017**.

## <a name="run-a-hive-query"></a>Hive-lekérdezés futtatása
A HDInsight .NET SDK-t biztosít a .NET-ügyfélkönyvtárak, ami jó hír működik a HDInsight-fürtökkel a .NET használatával. 

**A feladatok elküldése**

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.
2. A Nuget-Csomagkezelő konzolról futtassa a következő parancsot:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. A következő kód használatával:

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

Az alkalmazás kimenete kell hasonló lesz:

![HDInsight Hadoop Hive-feladat kimenete](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>További lépések
Ebben a cikkben, hogyan hozhat létre HDInsight-fürtöt többféleképpen. További tudnivalókért tekintse meg a következő cikkeket:

* [Az Azure HDInsight – első lépések](apache-hadoop-linux-tutorial-get-started.md)
* [A HDInsight Apache Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
* [A HDInsight Apache Hadoop-fürtök kezelése az Azure portal használatával](../hdinsight-administer-use-management-portal.md)
* [HDInsight .NET SDK-referencia](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Az Apache Pig használata a HDInsight](hdinsight-use-pig.md)
* [A HDInsight Apache Sqoop használata](apache-hadoop-use-sqoop-mac-linux.md)
* [Nem interaktív hitelesítéssel ellátott .Net HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


