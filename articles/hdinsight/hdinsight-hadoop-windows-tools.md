---
title: "A Windows-számítógépek használata a HDInsight - Azure hadooppal |} Microsoft Docs"
description: "HDInsight Hadoop Windows PC működnek. Kezelése és lekérdezés fürtök PowerShell, a Visual Studio és a Linux-eszközökkel. A .NET big data-megoldások fejlesztése."
services: hdinsight
keywords: windows, a hadooppal Windows hadoop
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>A Windows-számítógép HDInsight a Hadoop ökoszisztémájának működik

További tudnivalók a fejlesztési és a Hadoop ökoszisztémájának a HDInsight használatához a Windows rendszerű számítógépen lévő beállítások. 

HDInsight az Apache Hadoop és a Hadoop-összetevők, nyílt forráskódú technológiák fejlesztett Linux rendszeren alapul. HDInsight 3.4 és újabb verzióját használja az Ubuntu Linux terjesztési az alapul szolgáló operációs rendszer a fürt. Azonban dolgozhat HDInsight egy Windows ügyfél vagy a Windows fejlesztői környezetre.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Használja a Powershellt üzembe helyezési és kezelési feladatok
Az Azure PowerShell egy parancsfájl-kezelési környezet, melyekkel automatizálhatja a központi telepítési és felügyeleti feladatokat a Hdinsightban a Windows és szabályozhatja.

A PowerShell használatával elvégezhető feladatok példái:

* [PowerShell-fürtök létrehozása](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [PowerShell-lel Hive-lekérdezések futtatása](hdinsight-hadoop-use-hive-powershell.md)
* [A PowerShell-lel fürtök kezelése](hdinsight-administer-use-powershell.md)

Lépések segítségével [Azure Powershell telepítése és konfigurálása](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) a letöltéséhez. Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új parancsmagok használatát az Azure Resource Manager című [Fejlesztőeszközök Azure Resource Manager-alapú HDInsight-fürtök az áttelepítés](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Futtatása böngészőben segédprogramok
Az alábbi segédprogramokat rendelkezik egy webes felhasználói felület, amelyen a böngészőben:
* **[Azure Cloud rendszerhéj (előzetes verzió)](https://docs.microsoft.com/azure/cloud-shell/quickstart)**  egy interaktív, parancssori rendszerhéj és belül a böngészőben futó Azure-portálon.
* **[Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md)**  van egy kezelési és figyelési segédprogram használható az Azure-portál különböző feladatok, például a kezelésére használható:
    * [Használja az Ambari REST API-val](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Az Ambari Hive nézete](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Az Ambari Tez megtekintése](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>(Hadoop) a Data Lake Tools for Visual Studio
A Data Lake Tools for Visual Studio használatával telepítését és kezelését a Storm-topológiák. A Data Lake Tools is telepíti a SCP.NET SDK, amely lehetővé teszi a Visual Studio C# Storm-topológiák fejlesztése.

Mielőtt továbblép a következő példák [telepítése, és próbálja meg a Data Lake Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md). 

A Visual Studio és a Data Lake Tools for Visual Studio elvégezhető feladatok példái:
* [Telepítését és kezelését a Storm-topológiák a Visual Studio eszközből](hdinsight-storm-deploy-monitor-topology-linux.md)
* [C#-topológiák fejlesztése a Visual Studio használatával alatt futó Storm](hdinsight-storm-develop-csharp-visual-studio-topology.md). A bits csatlakozhat például az Azure Cosmos adatbázis és az SQL Database-adatbázisok Storm-topológiák például sablonokat tartalmaznak.

## <a name="visual-studio-and-the-net-sdk"></a>A Visual Studio és a .NET SDK 

Visual Studio .NET SDK-t használhatja a fürtök és a big data-alkalmazások fejlesztését. Más IDEs használható a következő feladatokat, de a Visual Studio példák.

A Visual Studio .NET SDK-val elvégezhető feladatok példái:
* [Fürtök létrehozása és a HDInsight .NET-keretrendszer alkalmazás használata](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [A .NET SDK használatával Hive-lekérdezések futtatása](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [A Hive és a Hadoop streamelési Pig C# felhasználó által definiált függvények használata](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> Tipp .NET megoldások a Windows-alapú HDInsight-fürtök használ, akkor egy időben, a Linux-alapú fürtökön szeretne áttelepítést végezni. További információkért lásd: [át .NET megoldást a Windows-alapú HDInsight Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA és Eclipse IDE Spark-fürtök
Mindkét [Intellij IDEA](https://www.jetbrains.com/idea/download) és a [Eclipse IDE](https://www.eclipse.org/downloads/) a következőkre használható:
* Fejleszthet, és küldje el a Scala Spark alkalmazás egy HDInsight Spark-fürtön.
* A Spark-fürt erőforrások eléréséhez.
* Fejleszthet, és egy Scala Spark alkalmazás helyileg történő futtatása.

Ezek a cikkek megjelenítése módját: 
* Intellij IDEA: [Create Spark-alkalmazások az Azure-eszközkészlet az Intellij beépülő modul és a Scala SDK használatával.](hdinsight-apache-spark-intellij-tool-plugin.md)
* IDE vagy az Eclipse Scala IDE Holdas: [létrehozása Spark-alkalmazások és az Eclipse Azure eszköztára](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>A Spark on az adatszakértőkön notebookok 
Az Apache Spark on hdinsight fürtök Zeppelin notebookok és a Jupyter notebookok használható kernelt. 

* [Ismerje meg, kernelek a Spark-fürtök a Jupyter notebookok használata Spark-alkalmazások tesztelése](hdinsight-apache-spark-zeppelin-notebook.md)
* [Zeppelin notebookok használata Spark-fürtön Spark-feladatok futtatása](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Futtassa a Linux-alapú eszközök és technológiák a Windows

Ha olyan helyzet, amikor egy eszköz vagy technológia, amely csak akkor érhető el a Linux használni kell, vegye figyelembe a következő lehetőségeket:

* **A Windows 10 (béta) bash** Ez a témakör a Linux alrendszer Windows. Bash Linux segédprogramok futtathatja közvetlenül egy dedikált Linux rendszerhez – telepítés karbantartása nélkül teszi lehetővé. [Telepítése és futtatása a Bash beta Windows 10 rendszeren](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **A Windows docker** számos Linux-alapú eszközök hozzáférést biztosít, és a Windows-ről futtatható. Docker segítségével például Hive Beeline ügyfél közvetlenül Windows futtathatja. Is használhatja a Docker helyi Jupyter notebook futtatásához, és távolról csatlakozni a Spark on HDInsight. [Ismerkedés a Windowshoz készült Docker](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  lehetővé teszi, hogy grafikusan keresse meg a fürt fájlrendszer SSH-kapcsolaton keresztül.

## <a name="next-steps"></a>Következő lépések
Ha most ismerkedik a Linux-alapú fürtökön dolgozik, tekintse meg a következő cikkeket:
* [Hadoop, Kafka, Spark vagy más fürtök beállítása](hdinsight-hadoop-provision-linux-clusters.md)
* [Tippek a HDInsight-fürtök Linux rendszeren](hdinsight-hadoop-linux-information.md)