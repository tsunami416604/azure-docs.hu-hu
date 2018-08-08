---
title: Az Apache Pig-feladatok futtatása Hadoop – Azure HDInsight .NET SDK-val
description: Ismerje meg, hogyan használhatja a .NET SDK a Hadoophoz Hadoop on HDInsight Pig-feladatok elküldése.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: jasonh
ms.openlocfilehash: e781d5d6cf9b1e2ce3c79e138c7a82c780ce8df4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594814"
---
# <a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>A .NET SDK használatával a hadoop együttes használata a HDInsight Pig-feladatok futtatása

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ismerje meg, hogyan használhatja a .NET SDK a Hadoophoz Hadoop on Azure HDInsight az Apache Pig-feladatok elküldése.

A HDInsight .NET SDK-t biztosít a .NET-ügyfélkönyvtárak, amelyek segítségével könnyebben működik a HDInsight-fürtökkel a .NET használatával. A Pig MapReduce operations hozzon létre egy sorozatát adatátalakítások modellezés teszi lehetővé. Ebből a dokumentumból megismerheti, hogyan egy egyszerű C#-alkalmazás használatával küldjön el egy Pig feladatot egy HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

A jelen cikkben ismertetett lépések a következőkre lesz szüksége.

* Egy Azure HDInsight (Hadoop on HDInsight)-fürt (bármelyik Windows vagy Linux-alapú).

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* A Visual Studio 2012, 2013, 2015 vagy 2017.

## <a name="create-the-application"></a>Az alkalmazás létrehozása

A HDInsight .NET SDK-t biztosít a .NET-ügyfélkönyvtárak, ami jó hír működik a HDInsight-fürtökkel a .NET használatával.

1. Az a **fájl** a Visual Studióban, válassza a menü **új** , majd **projekt**.

2. Írja be az új projekt, vagy válassza ki a következő értékeket:

   | Tulajdonság | Érték |
   | ------ | ------ |
   | Kategória | Sablonok/Visual C#/Windows |
   | Sablon | Konzolalkalmazás |
   | Name (Név) | SubmitPigJob |

3. A projekt létrehozásához kattintson az **OK** gombra.

4. Az a **eszközök** menüjében válassza **Kódtárcsomag-kezelő** vagy **NuGet-Csomagkezelő**, majd válassza ki **Package Manager Console**.

5. A .NET SDK-csomagok telepítéséhez használja a következő parancsot:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. A Megoldáskezelőben kattintson duplán a **Program.cs** való megnyitásához. Cserélje le a meglévő kód a következő.

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

7. Az alkalmazás elindításához nyomja le az ENTER **F5**.

8. Az alkalmazás a kilépéshez nyomja le az ENTER **ENTER**.

## <a name="next-steps"></a>További lépések

A HDInsight Pig kapcsolatos tudnivalókat lásd: [a Pig használata a hadooppal a HDInsight](hdinsight-use-pig.md).

További információ a HDInsight a Hadoop használatával a következő dokumentumokban talál:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
