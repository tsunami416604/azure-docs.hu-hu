---
title: Apache Hive-lekérdezések futtatása HDInsight .NET SDK használatával – Azure
description: Ismerje meg, hogyan küldhet apache Hadoop-feladatokat az Azure HDInsight Apache Hadoop ba a HDInsight .NET SDK használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552491"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Apache Hive-lekérdezések futtatása a HDInsight .NET SDK használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan küldhet Apache Hive-lekérdezéseket a HDInsight .NET SDK használatával. C# programot ír, hogy elküldje a Hive-lekérdezést a Hive-táblák listázásához, és jelenítse meg az eredményeket.

> [!NOTE]  
> A cikkben ismertetett lépéseket Windows-ügyfélről kell végrehajtani. A Linux, OS X vagy Unix kliens ek ként a Hive-val való együttműködésre vonatkozó információkért használja a cikk tetején látható lapválasztót.

## <a name="prerequisites"></a>Előfeltételek

A cikk megkezdése előtt a következő elemekkel kell rendelkeznie:

* Apache Hadoop-fürt a HDInsightban. Lásd: [A Linux-alapú Hadoop használatának első lépései a HDInsightban.](apache-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]  
    > 2017. szeptember 15-től a HDInsight .NET SDK csak az Azure Storage-fiókokból származó hive-lekérdezési eredményeket támogatja. Ha ezt a példát egy HDInsight-fürttel használja, amely az Azure Data Lake Storage-t használja elsődleges tárolóként, a .NET SDK használatával nem lehet lekérni a keresési eredményeket.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013-as és azon túl. Legalább a .NET asztali munkabehaszni a **(a) kiszolgálón** kell lennie.

## <a name="run-a-hive-query"></a>Hive-lekérdezés futtatása

A HDInsight .NET SDK .NET ügyféltárakat biztosít, így könnyebben dolgozhat a .NET-ről származó HDInsight-fürtökkel.

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.

1. A Nuget Csomagkezelő konzolon futtassa a következő parancsot:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Az alábbi kód szerkesztésével inicializálja `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`a változók értékeit: . Ezután használja a módosított kódot a Visual Studio **Program.cs** teljes tartalmaként.

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
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

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

1. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Az alkalmazás kimenetének hasonlónak kell lennie a következőkhöz:

![HDInsight Hadoop Hive-feladat kimenete](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan küldhet Apache Hive-lekérdezéseket a HDInsight .NET SDK használatával. További információ: a következő cikkek:

* [Ismerkedés az Azure HDInsight-mal](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-fürtök létrehozása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK–hivatkozás](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Az Apache Sqoop használata a HDInsight segítségével](apache-hadoop-use-sqoop-mac-linux.md)
* [Nem interaktív hitelesítéssel ellátott .Net HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
