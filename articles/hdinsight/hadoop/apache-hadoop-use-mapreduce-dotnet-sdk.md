---
title: HDInsight .NET SDK-t – Azure MapReduce-feladatok elküldése
description: Útmutató a HDInsight .NET SDK használata Azure HDInsight Hadoop MapReduce-feladatok elküldése.
editor: jasonwhowell
services: hdinsight
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: a22a2c75f5f520fb74d7ef6a57ff519e95ad273e
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596482"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK használatával a MapReduce-feladatok futtatása
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ismerje meg, hogyan lehet elküldeni a MapReduce-feladatok HDInsight .NET SDK használatával. HDInsight fürtök rendelkeznek egy bizonyos MapReduce-minták a jar-fájlt. A jar-fájlra van */example/jars/hadoop-mapreduce-examples.jar*.  A minták egyik *wordcount*. Elkészít egy C# konzolalkalmazást wordcount feladatok elküldéséhez.  A feladat beolvassa az adatokat a */example/data/gutenberg/davinci.txt* fájlt, és kiírathatja a eredményeket */example/data/davinciwordcount*.  Ha szeretné újra futtatni az alkalmazást, akkor a kimeneti mappa kell törölni.

> [!NOTE]
> A jelen cikkben ismertetett lépések egy Windows ügyfél kell elvégezni. Információ a Hive használata a Linux, OS X or Unix-ügyfél használatával a cikk tetején látható lapon választómezőt használja.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk elkezdéséhez a következőkkel kell rendelkeznie:

* **A HDInsight Hadoop-fürt**. Lásd: [HDInsight Linux-alapú Hadoop használatának első lépései](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK használatával a MapReduce-feladatok elküldése
A HDInsight .NET SDK-t biztosít a .NET-ügyfélkönyvtárak, ami jó hír működik a HDInsight-fürtökkel a .NET használatával. 

**A feladatok elküldése**

1. Hozzon létre egy C# konzolalkalmazást a Visual Studióban.
2. A NuGet-Csomagkezelő konzolról futtassa a következő parancsot:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```
3. A következő kód használatával:

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

Futtassa újra a feladatot, módosítania kell a feladat kimeneti mappa nevét, a példában a "/ Példa/data/davinciwordcount".

Ha a feladat sikeresen befejeződik, az alkalmazás a kimeneti fájl "rész – az r-00000" tartalom nyomtatása.

## <a name="next-steps"></a>További lépések
Ebben a cikkben, hogyan hozhat létre HDInsight-fürtöt többféleképpen. További tudnivalókért tekintse meg a következő cikkeket:

* Egy Hive-feladat elküldése, lásd: [futtatása Hive-lekérdezéseket a HDInsight .NET SDK használatával](apache-hadoop-use-hive-dotnet-sdk.md).
* HDInsight-fürtök létrehozására, tekintse meg a [Linux-alapú Hadoop-fürtök a HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* HDInsight-fürtök kezeléséhhez lásd: [kezelése Hadoop-fürtök a HDInsight](../hdinsight-administer-use-portal-linux.md).
* A HDInsight .NET SDK tanulás, lásd: [HDInsight .NET SDK-referenciában](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* A nem interaktív hitelesítéséhez az Azure-ba, lásd: [nem interaktív hitelesítéssel .NET HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

