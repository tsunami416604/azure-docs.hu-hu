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
ms.openlocfilehash: c80c7a075b8a7735a4e9d2438c94eaac89f2b50e
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006967"
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Munka a Windows rendszerű számítógépek a HDInsight Hadoop-ökoszisztéma

Ismerje meg a fejlesztési és felügyeleti lehetőségeket, a HDInsight Hadoop-ökoszisztéma használatához a Windows számítógépen. 

HDInsight az Apache Hadoop és a Hadoop-összetevők, nyílt forráskódú technológiák fejlesztett Linux rendszeren alapul. HDInsight 3.4-es és újabb verzióját használja az Ubuntu Linux-disztribúció az alapul szolgáló operációs rendszer a fürt. Azonban használhatja a HDInsight egy Windows ügyfél vagy a Windows fejlesztési környezetet.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Üzembe helyezési és kezelési feladatok a powershellel
Az Azure PowerShell-parancsfájl-kezelési környezet, amellyel szabályozhatja és automatizálhatja az üzembe helyezési és kezelési feladatok a HDInsight, a Windows.

A PowerShell használatával elvégezhető feladatok példái:

* [PowerShell-lel fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Hive-lekérdezések futtatásához PowerShell-lel](hadoop/apache-hadoop-use-hive-powershell.md)
* [A PowerShell-fürtök kezelése](hdinsight-administer-use-powershell.md)

Kövesse a lépéseket [Azure Powershell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a legújabb verzió beszerzéséhez. Ha rendelkezik olyan parancsprogramjai, amelyeket módosítani kell az új parancsmagokat használhatja az Azure Resource Manager, [fejlesztőeszközeinek Azure Resource Manager-alapú HDInsight-fürtök áttelepítése](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Segédprogramok futtatása böngészőben
Az alábbi segédprogramokat webes felhasználói Felületet a böngészőben futó rendelkezik:
* **[Az Azure Cloud Shell (előzetes verzió)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  egy interaktív, a parancssori felület, amely a böngészőben, és belül fut az Azure Portalon.
* **[Az Ambari webes felhasználói felület](hdinsight-hadoop-manage-ambari.md)**  van egy felügyeleti és monitorozási segédprogramot elérhető az Azure Portalon, amelyek segítségével kezelheti a különféle feladatok, például:
    * [Használja az Ambari REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Az Ambari Hive nézete](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Az Ambari Tez megtekintése](hdinsight-debug-ambari-tez-view.md)

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
* [Hive-lekérdezések futtatásához a .NET SDK használatával](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [C# felhasználó által definiált függvények használata a Hive és a hadoop streamelési Piggel](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

> Tipp .NET – megoldások futtatja a Windows-alapú HDInsight-fürtökkel, esetén felfedeznie a Linux-alapú fürtök áttelepítést végezni. További információkért lásd: [át .NET megoldást kínál a Linux-alapú HDInsight Windows-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Az Intellij IDEA és a Spark-fürtök az Eclipse ide-vel
Mindkét [az Intellij IDEA](https://www.jetbrains.com/idea/download) és a [Eclipse IDE](https://www.eclipse.org/downloads/) a következőkre használható:
* Fejlesztés, és küldje el a Scala Spark-alkalmazását egy HDInsight Spark-fürtön.
* Spark-fürt erőforrásainak eléréséhez.
* Fejlesztés és a Scala Spark-alkalmazás helyileg történő futtatása.

Ezek a cikkek bemutatják hogyan: 
* Intellij IDEA: [létrehozása Spark-alkalmazások az Azure Toolkit for Intellij beépülő modul és a Scala SDK-val.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE- vagy az Eclipse-hez Scala IDE: [létrehozása Spark-alkalmazások és az Eclipse-hez készült Azure-eszközkészlet](spark/apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>A Spark jegyzetfüzeteket adatszakértők számára 
HDInsight az Apache Spark-fürtök tartalmazzák a Zeppelin-jegyzetfüzetek és a Jupyter notebookok használható kernelt. 

* [Ismerje meg, hogyan Spark-fürtök Jupyter notebookok a kernelekkel követve tesztelheti a Spark-alkalmazások](spark/apache-spark-zeppelin-notebook.md)
* [Ismerje meg, hogyan Zeppelin notebookok használata Spark-fürtökön futó Spark-feladatok futtatásához](spark/apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Futtassa a Linux-alapú eszközök és technológiák a Windows

Ha olyan helyzet, amikor egy eszköz vagy technológia, amely csak akkor érhető el Linux rendszeren használni kell, vegye figyelembe a következő beállításokat:

* **A Windows 10-es (bétaverzió) bash** Ez a témakör a Linux-alrendszer Windows. A bash lehetővé teszi, hogy közvetlenül, egy dedikált Linux rendszerhez – telepítés kezelése nélkül futtathat a Linux-segédeszközöket. [Telepítse és futtassa a Bash béta a Windows 10-es](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **A Windows docker** számos Linux-alapú eszközök hozzáférést biztosít, és közvetlenül a Windows is futtathatók. A Docker használatával például a Hive a Beeline-ügyfél futtatása közvetlenül a Windows. Is használhatja a Docker helyi Jupyter notebook futtatásához, és távolról csatlakozhat a Spark on HDInsight. [A Windows Docker használatának első lépései](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  lehetővé teszi, hogy grafikusan keresse meg a fürt fájlrendszer egy SSH-kapcsolaton keresztül.

## <a name="next-steps"></a>További lépések
Ha most ismerkedik a Linux-alapú fürtökön működik, tekintse meg a következő cikkeket:
* [Állítsa be a Hadoop, a Kafka, a Spark vagy egyéb fürtök](hdinsight-hadoop-provision-linux-clusters.md)
* [Tippek a HDInsight-fürtök a Linuxon](hdinsight-hadoop-linux-information.md)
