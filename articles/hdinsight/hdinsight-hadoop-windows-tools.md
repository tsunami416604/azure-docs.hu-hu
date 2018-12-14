---
title: Egy Windows PC-s használata a Hadooppal a HDInsight – Azure
description: A HDInsight a Hadoop-Windows PC működnek. Kezelheti és lekérdezés-fürt a PowerShell, a Visual Studio és a Linux rendszerű eszközök. .NET-tel big data-megoldások fejlesztése.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/17/2017
ms.openlocfilehash: a522d436328a3aeb1ed365baa2ec1e3b9d3387ed
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385023"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>A HDInsight Windows PC-ről az Apache Hadoop-ökoszisztéma működik

Ismerje meg a fejlesztési és felügyeleti lehetőségeket, a HDInsight az Apache Hadoop-ökoszisztéma használatához a Windows számítógépen. 

HDInsight az Apache Hadoop és a Hadoop-összetevők, nyílt forráskódú technológiák fejlesztett Linux rendszeren alapul. HDInsight 3.4-es és újabb verzióját használja az Ubuntu Linux-disztribúció az alapul szolgáló operációs rendszer a fürt. Azonban használhatja a HDInsight egy Windows ügyfél vagy a Windows fejlesztési környezetet.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Üzembe helyezési és kezelési feladatok a powershellel
Az Azure PowerShell-parancsfájl-kezelési környezet, amellyel szabályozhatja és automatizálhatja az üzembe helyezési és kezelési feladatok a HDInsight, a Windows.

A PowerShell használatával elvégezhető feladatok példái:

* [PowerShell-lel fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [PowerShell-lel, az Apache Hive-lekérdezések futtatása](hadoop/apache-hadoop-use-hive-powershell.md)
* [A PowerShell-fürtök kezelése](hdinsight-administer-use-powershell.md)

Kövesse a lépéseket [Azure Powershell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a legújabb verzió beszerzéséhez. Ha rendelkezik olyan parancsprogramjai, amelyeket módosítani kell az új parancsmagokat használhatja az Azure Resource Manager, [fejlesztőeszközeinek Azure Resource Manager-alapú HDInsight-fürtök áttelepítése](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Segédprogramok futtatása böngészőben
Az alábbi segédprogramokat webes felhasználói Felületet a böngészőben futó rendelkezik:
* **[Az Azure Cloud Shell (előzetes verzió)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  egy interaktív, a parancssori felület, amely a böngészőben, és belül fut az Azure Portalon.
* **[Az Apache Ambari webes Kezelőfelületen](hdinsight-hadoop-manage-ambari.md)**  van egy felügyeleti és monitorozási segédprogramot elérhető az Azure Portalon, amelyek segítségével kezelheti a különféle feladatok, például:
    * [Használja az Apache Ambari REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Az Apache Hive, az Apache Ambari megtekintése](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Az Apache Ambari az Apache Tez megtekintése](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>(Hadoop) a Data Lake Tools for Visual Studio
A Data Lake Tools for Visual Studio használatával üzembe helyezés és kezelés a Storm-topológiák. A Data Lake Tools is telepíti az SCP.NET SDK-t, amely lehetővé teszi, hogy a Visual Studio használatával C# Storm-topológiák fejlesztése a.

Mielőtt továbblépne a következő példákban [telepítése, és próbálja meg a Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md). 

A Visual Studio és a Data Lake Tools for Visual Studio elvégezhető feladatok példái:
* [Üzembe helyezés és kezelés a Storm-topológiák a Visual Studióból](storm/apache-storm-deploy-monitor-topology-linux.md)
* [A Storm, a Visual Studio használatával C#-topológiák fejlesztése](storm/apache-storm-develop-csharp-visual-studio-topology.md). A bits tartozó adatbázisok, például az Azure Cosmos DB és az SQL Database csatlakozhat Storm-topológiák közé tartozik.

## <a name="visual-studio-and-the-net-sdk"></a>A Visual Studio és a .NET SDK-val 

Használhatja a Visual Studio a .NET SDK használatával kezelheti a fürtöket és big data-alkalmazások fejlesztéséhez. A következő feladatokat is használhat más ide-ket, de példák láthatók a Visual Studióban.

A Visual Studióban a .NET SDK-val elvégezhető feladatok példái:
* [Fürtök létrehozása és használata a HDInsight .NET-keretrendszer-alkalmazás](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [A .NET SDK használatával, az Apache Hive-lekérdezések futtatása](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [Használat C# az Apache Hive- és Apache hadoop streamelési Apache Pig, felhasználó által definiált függvények](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> TIPP   
> .NET – megoldások a Windows-alapú HDInsight-fürtökkel rendszert használ, célszerű felfedeznie a Linux-alapú fürtök áttelepítést végezni. További információkért lásd: [át .NET megoldást kínál a Linux-alapú HDInsight Windows-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Az Intellij IDEA és a Spark-fürtök az Eclipse ide-vel
Mindkét [az Intellij IDEA](https://www.jetbrains.com/idea/download) és a [Eclipse IDE](https://www.eclipse.org/downloads/) a következőkre használható:
* Fejlesztés, és küldje el a Scala Spark-alkalmazását egy HDInsight Spark-fürtön.
* Spark-fürt erőforrásainak eléréséhez.
* Fejlesztés és a Scala Spark-alkalmazás helyileg történő futtatása.

Ezek a cikkek bemutatják hogyan: 
* Intellij IDEA: [Hozzon létre Apache Spark-alkalmazások az Azure Toolkit for Intellij beépülő modul és a Scala SDK-val.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE- vagy a Scala IDE az Eclipse-hez: [Az Apache Spark-alkalmazások és az Azure Toolkit for Eclipse létrehozása](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>A Spark jegyzetfüzeteket adatszakértők számára 
Az Apache Spark-fürtökön a HDInsight Apache Zeppelin-jegyzetfüzetek és a Jupyter notebookok használható kernelt tartalmazza. 

* [Útmutató kernelekkel az Apache Spark-fürtök Jupyter notebookok a Spark-alkalmazások tesztelése](spark/apache-spark-zeppelin-notebook.md)
* [Ismerje meg, az Apache Spark-fürtökön futó Apache Zeppelin notebookok használata Spark-feladatok futtatása](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Futtassa a Linux-alapú eszközök és technológiák a Windows

Ha olyan helyzet, amikor egy eszköz vagy technológia, amely csak akkor érhető el Linux rendszeren használni kell, vegye figyelembe a következő beállításokat:

* **A Windows 10-es (bétaverzió) bash** Ez a témakör a Linux-alrendszer Windows. A bash lehetővé teszi, hogy közvetlenül, egy dedikált Linux rendszerhez – telepítés kezelése nélkül futtathat a Linux-segédeszközöket. [Telepítse és futtassa a Bash béta a Windows 10-es](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **A Windows docker** számos Linux-alapú eszközök hozzáférést biztosít, és közvetlenül a Windows is futtathatók. A Docker használatával például a Hive a Beeline-ügyfél futtatása közvetlenül a Windows. Is használhatja a Docker helyi Jupyter notebook futtatásához, és távolról csatlakozhat a Spark on HDInsight. [A Windows Docker használatának első lépései](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  lehetővé teszi, hogy grafikusan keresse meg a fürt fájlrendszer egy SSH-kapcsolaton keresztül.

## <a name="next-steps"></a>További lépések
Ha most ismerkedik a Linux-alapú fürtökön működik, tekintse meg a következő cikkeket:
* [Az Apache Hadoop, az Apache Kafka, Apache Spark vagy egyéb fürtök beállítása](hdinsight-hadoop-provision-linux-clusters.md)
* [Tippek a HDInsight-fürtök a Linuxon](hdinsight-hadoop-linux-information.md)
