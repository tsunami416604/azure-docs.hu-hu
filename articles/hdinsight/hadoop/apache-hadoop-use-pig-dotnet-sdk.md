---
title: Apache Pig feladatok futtatásához a .NET SDK-val a Hadoop - Azure HDInsight |} Microsoft Docs
description: Megtudhatja, hogyan használhatja a .NET SDK a Hadoop-elküldeni a Pig-feladatokhoz és a hdinsight Hadoop.
services: hdinsight
documentationcenter: .net
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: fa11d49a-328c-47e7-b16d-e7ed2a453195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 986e6fe0e71c4e1361814e22d89fa5121341cc79
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>A .NET SDK használatával a hdinsight Hadoop Pig feladatok futtatása

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Megtudhatja, hogyan használható a .NET SDK a Hadoop on Azure HDInsight Hadoop Apache Pig feladatok elküldéséhez.

A HDInsight .NET SDK .NET ügyféloldali kódtáraknál, amely megkönnyíti a HDInsight-fürtök a .NET-együttműködve biztosítja. A Pig hozhat létre MapReduce műveletek modellezési adatátalakítást sorozata alapján. Ebből a dokumentumból megismerheti, hogyan elküldeni a Pig feladatot egy HDInsight-fürthöz egy alapszintű C#-alkalmazás használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépéseket a következőkre lesz szüksége.

* (A HDInsight Hadoop) Azure HDInsight-fürtök (vagy a Windows vagy Linux-alapú).

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* A Visual Studio 2012, 2013, 2015-öt vagy 2017.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

A HDInsight .NET SDK biztosít a .NET ügyféloldali kódtáraknál, így azokat könnyebben működéséhez a .NET-HDInsight-fürtökkel.

1. Az a **fájl** elemét a Visual Studióban, válassza ki **új** majd **projekt**.

2. Az új projektbe futnak írja be vagy válassza ki a következő értékeket:

   | Tulajdonság | Érték |
   | ------ | ------ |
   | Kategória | Sablonok/Visual C#/Windows |
   | Sablon | Konzolalkalmazás |
   | Name (Név) | SubmitPigJob |

3. A projekt létrehozásához kattintson az **OK** gombra.

4. Az a **eszközök** menüjében válassza **Kódtárcsomag-kezelő** vagy **NuGet-Csomagkezelő**, majd válassza ki **Csomagkezelő konzol**.

5. A .NET SDK-csomagok telepítéséhez használja a következő parancsot:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. A Megoldáskezelőben kattintson duplán a **Program.cs** való megnyitásához. Cserélje le a meglévő kódot a következő.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
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

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. Az alkalmazás elindításához nyomja le az **F5**.

8. Kilép az alkalmazásból, nyomja le az **ENTER**.

## <a name="next-steps"></a>További lépések

Információk a hdinsight Pig: [a Pig használata a hdinsight Hadoop](hdinsight-use-pig.md).

A HDInsight Hadoop használatával kapcsolatos további információkért lásd a következő dokumentumokat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
