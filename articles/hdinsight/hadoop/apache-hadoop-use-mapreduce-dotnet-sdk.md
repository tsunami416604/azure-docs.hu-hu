---
title: MapReduce feladatok beküldése a HDInsight .NET SDK használatával – Azure
description: Megtudhatja, hogyan küldhet mapreduce feladatokat az Azure HDInsight Apache Hadoop hdinsight .NET SDK használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157050"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce feladatok futtatása HDInsight .NET SDK segítségével

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

További információ aMapReduce feladatok elküldéséhez a HDInsight .NET SDK használatával. A HDInsight-fürtök néhány MapReduce mintával ellátott jar-fájlt tartalmaznak. A jar `/example/jars/hadoop-mapreduce-examples.jar`fájl .  Az egyik minta a **wordcount**. Egy C# konzolalkalmazást fejleszt a wordcount feladat elküldéséhez.  A feladat `/example/data/gutenberg/davinci.txt` beolvassa a fájlt, `/example/data/davinciwordcount`és az eredményeket a programba adja ki.  Ha újra szeretné futtatni az alkalmazást, meg kell tisztítania a kimeneti mappát.

> [!NOTE]  
> A cikkben ismertetett lépéseket Windows-ügyfélről kell végrehajtani. A Linux, OS X vagy Unix kliens ek ként a Hive-val való együttműködésre vonatkozó információkért használja a cikk tetején látható lapválasztót.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon.](../hdinsight-hadoop-create-linux-clusters-portal.md)

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce feladatok küldése a HDInsight .NET SDK használatával

A HDInsight .NET SDK .NET ügyféltárakat biztosít, amelyek megkönnyítik a .NET HDInsight-fürtjeivel való munkát.

1. Indítsa el a Visual Studio alkalmazást, és hozzon létre egy C# konzolalkalmazást.

1. Nyissa meg az **Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolját,** és írja be a következő parancsot:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Másolja az alábbi kódot **Program.cs**. Ezután a kód szerkesztésével a `existingClusterName` `existingClusterPassword`következők `defaultStorageAccountName` `defaultStorageAccountKey`értékét `defaultStorageContainerName`kell betatni: , , , és .

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
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
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

1. Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

A feladat ismételt futtatásához módosítania kell a feladat kimeneti `/example/data/davinciwordcount`mappa nevét, a mintában.

A feladat sikeres befejezése után az alkalmazás kinyomtatja `part-r-00000`a kimeneti fájl tartalmát.

## <a name="next-steps"></a>További lépések

Ebben a cikkben a HDInsight-fürt létrehozásának számos módját ismerteelheti. További információ: a következő cikkek:

* Hive-feladat elküldése az [Apache Hive-lekérdezések futtatása hdinsight .NET SDK használatával](apache-hadoop-use-hive-dotnet-sdk.md).
* HDInsight-fürtök létrehozásáról a [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsightban című témakörben található.](../hdinsight-hadoop-provision-linux-clusters.md)
* A HDInsight-fürtök kezeléséről az [Apache Hadoop-fürtök kezelése a HDInsightban.](../hdinsight-administer-use-portal-linux.md)
* A HDInsight .NET SDK című témakörben a [HDInsight .NET SDK – hivatkozás című témakörben nyújt elő.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* Az Azure nem interaktív hitelesítése a [nem interaktív hitelesítéslétrehozása .NET HDInsight-alkalmazások létrehozása](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)című témakörben látható.