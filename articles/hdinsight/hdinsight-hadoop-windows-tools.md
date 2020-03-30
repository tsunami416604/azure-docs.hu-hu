---
title: Windows pc használata a Hadoop segítségével a HDInsighton – Azure
description: Windows rendszerű számítógépről dolgozhat a HDInsight hadoopi számítógépén. Fürtök kezelése és lekérdezése PowerShell, Visual Studio és Linux eszközökkel. Big data-megoldásokat fejleszthet a .NET segítségével.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933930"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Munka az Apache Hadoop ökoszisztémában a HDInsight-on Windows rendszerű számítógépről

Ismerje meg a Windows PC fejlesztési és kezelési lehetőségeit az Apache Hadoop ökoszisztémában való munkához a HDInsight-on.

A HDInsight az Apache Hadoop és a Hadoop összetevőkön alapul, a Linuxon kifejlesztett nyílt forráskódú technológiákon. A HDInsight 3.4-es és újabb verziója az Ubuntu Linux disztribúciót használja a fürt alapjául szolgáló operációs rendszerként. A HDInsight szolgáltatással azonban windowsos ügyfél- vagy Windows-fejlesztői környezetből is dolgozhat.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>A PowerShell használata telepítési és felügyeleti feladatokhoz

Az Azure PowerShell egy parancsfájl-kezelési környezet, amely segítségével szabályozhatja és automatizálhatja a központi telepítési és felügyeleti feladatokat a Windows HDInsight-ban.

Példák a PowerShell használatával elvégezhető feladatokra:

* [Fürtök létrehozása a PowerShell használatával.](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Futtassa az Apache Hive-lekérdezéseket a PowerShell használatával.](hadoop/apache-hadoop-use-hive-powershell.md)
* [Fürtök kezelése a PowerShell segítségével.](hdinsight-administer-use-powershell.md)

Kövesse az [Azure Powershell telepítésével és konfigurálásával](https://docs.microsoft.com/powershell/azure/install-az-ps) a legújabb verzió beszerezhető lépéseit.

## <a name="utilities-you-can-run-in-a-browser"></a>Böngészőben futtatható segédprogramok

A következő segédprogramok böngészőben futó webes felhasználói felülettel rendelkeznek:
* **[Az Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** egy interaktív, parancssori rendszerhéj, amely a böngészőben és az Azure Portalon belül fut.

* **[Az Apache Ambari Web felhasználói felülete](hdinsight-hadoop-manage-ambari.md)** az Azure Portalon elérhető felügyeleti és figyelési segédprogram, amely különböző típusú feladatok kezelésére használható, például:
    * [Az Apache Ambari használata a REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive nézet az Apache Ambariban](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez nézet az Apache Ambari-ban](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) eszközök a Visual Studio

A Data Lake Tools for Visual Studio segítségével telepítheti és kezelheti a Storm-topológiákat. A Data Lake Tools telepíti az SDK SCP.NET is, amely lehetővé teszi a C# Storm topológiák fejlesztését a Visual Studio segítségével.

Mielőtt az alábbi példákra lépne, [telepítse és próbálja ki a Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md)alkalmazást.

Példák a Visual Studio és a Data Lake Tools for Visual Studio segítségével elvégzett feladatokra:
* [Storm-topológiák telepítése és kezelése a Visual Studióiból](storm/apache-storm-deploy-monitor-topology-linux.md)
* [C# topológiák](storm/apache-storm-develop-csharp-visual-studio-topology.md)at Storm segítségével Visual Studio . A bitek például a Storm-topológiák hoz adatbázisokkal, például az Azure Cosmos DB és az SQL Database segítségével használható példasablonokat tartalmaznak.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio és a .NET SDK

A .NET SDK-val rendelkező Visual Studio segítségével kezelheti a fürtöket és fejleszthet big data-alkalmazásokat. A következő feladatokhoz más azonosítókat is használhat, de a példák a Visual Studióban jelennek meg.

Példák a Visual Studio .NET SDK-jával elvégzett feladatokra:
* [Azure HDInsight SDK a .NET-hez.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)
* [Futtassa az Apache Hive-lekérdezéseket a .NET SDK használatával.](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [C# felhasználó által definiált függvények használata Apache Hive és Apache Pig streaming segítségével az Apache Hadoop on](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA és Eclipse IDE Spark klaszterek

Mind [az Intellij IDEA,](https://www.jetbrains.com/idea/download) mind az [Eclipse IDE](https://www.eclipse.org/downloads/) használható:
* Scala Spark-alkalmazást fejleszthet és küldhet el egy HDInsight Spark-fürtön.
* Access Spark-fürt erőforrásait.
* Scala Spark-alkalmazást fejleszthet és futtathat helyileg.

Ezek a cikkek bemutatják, hogyan:
* Intellij IDEA: [Apache Spark-alkalmazások létrehozása az Azure Toolkit for Intellij plug-in és a Scala SDK használatával.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE vagy Scala IDE for Eclipse: [Apache Spark-alkalmazások és az Eclipse-hez készült Azure-eszközkészlet létrehozása](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Jegyzetfüzetek a Sparkon adatszakértőknek

A HDInsight Apache Spark-fürtjei apache Zeppelin-jegyzetfüzeteket és -magokat tartalmaznak, amelyek Jupyter-jegyzetfüzetekkel használhatók.

* [Ismerje meg, hogyan használhatja a kerneleket az Apache Spark-fürtökön a Jupyter notebookokkal a Spark-alkalmazások teszteléséhez](spark/apache-spark-zeppelin-notebook.md)
* [Ismerje meg, hogyan használhatja az Apache Zeppelin notebookokat az Apache Spark-fürtökön a Spark-feladatok futtatásához](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Linux alapú eszközök és technológiák futtatása Windows rendszeren

Ha olyan helyzettel találkozik, amikor olyan eszközt vagy technológiát kell használnia, amely csak Linuxon érhető el, vegye figyelembe a következő lehetőségeket:

* **Bash az Ubuntu a Windows 10** egy Linux alrendszer a Windows. Bash lehetővé teszi, hogy közvetlenül futtatni Linux segédprogramok anélkül, hogy fenntartsák a dedikált Linux telepítés. A windows [10-es Windows-alrendszer telepítési útmutatójában](https://docs.microsoft.com/windows/wsl/install-win10) a windows 10-es operációs rendszer telepítési útmutatójában talál.  Más [Unix héjak](https://www.gnu.org/software/bash/) is működni fog.
* **A Docker for Windows** számos Linux-alapú eszközhöz biztosít hozzáférést, és közvetlenül a Windows rendszerről futtatható. A Docker segítségével például közvetlenül a Windowsból futtathatja a Hive-hoz készült Beeline-ügyfélfuttatást. A Docker segítségével helyi Jupyter-jegyzetfüzetet is futtathat, és távolról csatlakozhat a Sparkhoz a HDInsighton. [Ismerkedés a Windows Docker-rel](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** lehetővé teszi, hogy grafikusan böngészhet a fürt fájlrendszer egy SSH kapcsolat.

## <a name="cross-platform-tools"></a>Platformfüggetlen eszközök

Az Azure parancssori felülete (CLI) a Microsoft platformfüggetlen parancssori felülete, amely Azure-erőforrások felügyeletére szolgál.  További információ: [Azure command-line interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>További lépések

Ha most dolgozik Linux-alapú fürtökben, olvassa el az alábbi cikkeket:
* [Az Apache Hadoop, az Apache Kafka, az Apache Spark vagy más fürtök beállítása](hdinsight-hadoop-provision-linux-clusters.md)
* [Tippek a LINUX ON HDInsight-fürtökhöz](hdinsight-hadoop-linux-information.md)
