---
title: HDInsight .NET SDK - az Azure használatával Hive-lekérdezések futtatása
description: Ismerje meg, hogyan lehet elküldeni a HDInsight .NET SDK használata Azure HDInsight Hadoop, Hadoop-feladatokat.
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: cc7eb82e36ecc03e17b7abcf38f6bbe5c754ee5b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599435"
---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK használatával Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Útmutató a HDInsight .NET SDK használatával Hive-lekérdezések elküldéséhez. C# program írása elküldeni a Hive-lekérdezést futtathat Hive-táblák listázásához, és megjeleníti az eredményeket.

> [!NOTE]
> A jelen cikkben ismertetett lépések egy Windows ügyfél kell elvégezni. Információ a Hive használata a Linux, OS X or Unix-ügyfél használatával a cikk tetején látható lapon választómezőt használja.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk elkezdéséhez a következőkkel kell rendelkeznie:

* **A HDInsight Hadoop-fürt**. Lásd: [HDInsight Linux-alapú Hadoop használatának első lépései](apache-hadoop-linux-tutorial-get-started.md).

    > [!WARNING]
    > 2017. szeptember 15. a HDInsight .NET SDK vracející Hive lekérdezés eredményeit az Azure Storage-fiókok csak támogatja. Ebben a példában egy HDInsight-fürtöt, amely az Azure Data Lake Store használja elsődleges tárolóként használja, ha nem lehet lekérdezni a keresési eredmények a .NET SDK használatával.

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
* [A HDInsight Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
* [A HDInsight Hadoop-fürtök kezelése az Azure portal használatával](../hdinsight-administer-use-management-portal.md)
* [HDInsight .NET SDK-referencia](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [A Sqoop használata a HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Nem interaktív hitelesítéssel ellátott .Net HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
 


