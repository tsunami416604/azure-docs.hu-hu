---
title: Küldje el a MapReduce-feladatok használata a HDInsight .NET SDK - Azure |} Microsoft Docs
description: Megtudhatja, hogyan elküldeni a MapReduce-feladatok az Azure HDInsight Hadoop HDInsight .NET SDK használatával.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 0ea6e559cd9fd857f66ca1d9a7ed91d5f9c238e4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>A HDInsight .NET SDK használatával MapReduce-feladatok futtatása
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Megtudhatja, hogyan elküldeni a MapReduce-feladatok HDInsight .NET SDK használatával. HDInsight fürtök rendelkeznek egy bizonyos MapReduce-minták a jar-fájlra. A jar-fájlra van */example/jars/hadoop-mapreduce-examples.jar*.  A minták egyik *wordcount*. A C# Konzolalkalmazás a wordcount feladat elküldése fejleszt.  A feladat beolvassa a */example/data/gutenberg/davinci.txt* fájlt, és az eredmények */example/data/davinciwordcount*.  Ha szeretné újra futtatni az alkalmazást, a kimeneti mappa kell tisztítása.

> [!NOTE]
> A cikkben leírt lépéseket egy Windows ügyfél kell elvégezni. Kapcsolatos információkért használata a Linux, OS X vagy UNIX rendszerű ügyfélnél Hive használata a lap választó, a cikk tetején látható.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk megkezdése előtt rendelkeznie kell a következő elemek:

* **Hdinsight Hadoop-fürthöz**. Lásd: [hdinsight Linux-alapú Hadoop használatának megkezdésében](apache-hadoop-linux-tutorial-get-started.md).
* **A Visual Studio 2013 vagy 2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>A HDInsight .NET SDK használatával MapReduce-feladatok elküldése
A HDInsight .NET SDK biztosít a .NET ügyféloldali kódtáraknál, így azokat könnyebben működéséhez a .NET-HDInsight-fürtökkel. 

**Feladatok küldéséhez**

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.
2. A NuGet-Csomagkezelő konzolról futtassa a következő parancsot:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. A következő kód használható:

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string existingClusterUsername = "<Cluster Username>";
            private const string existingClusterPassword = "<Cluster User Password>";

            private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";

            private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
            private const string outputFolder = "/example/data/davinciwordcount";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);

                SubmitMRJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                        defaultStorageAccountName + 
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }
    ```

4. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Futtassa újra a feladatot, módosítania kell a feladat kimeneti mappa nevét, a példában az "/ Példa/data/davinciwordcount".

Ha a feladat sikeresen befejeződik, az alkalmazás a kimeneti fájl "rész-r-00000" tartalom nyomtatása.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta rendelkezik többféle módon hozhat létre HDInsight-fürtöt. További tudnivalókért tekintse meg a következő cikkeket:

* A Hive feladat elküldése, lásd: [HDInsight .NET SDK használatával futtassa Hive lekérdezések](apache-hadoop-use-hive-dotnet-sdk.md).
* A HDInsight-fürtök létrehozására, lásd: [hdinsight létrehozása Linux-alapú Hadoop-fürtök](../hdinsight-hadoop-provision-linux-clusters.md).
* A HDInsight-fürtök kezelése, lásd: [Hadoop kezelése a HDInsight-fürtök](../hdinsight-administer-use-portal-linux.md).
* A HDInsight .NET SDK tanulási, lásd: [HDInsight .NET SDK referenciáit](https://msdn.microsoft.com/library/mt271028.aspx).
* A nem interaktív hitelesítés az Azure-ba, lásd: [.NET HDInsight-alkalmazások létrehozása a nem interaktív hitelesítés](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

