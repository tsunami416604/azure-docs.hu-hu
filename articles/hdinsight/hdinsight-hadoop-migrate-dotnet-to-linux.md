---
title: A .NET és a Hadoop MapReduce használata Linux-alapú HDInsight – Azure
description: Ismerje meg, hogyan használhatók a .NET-alkalmazások a streaming MapReduce Linux-alapú HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382133"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>.NET-megoldások migrálása Windows-alapú HDInsight Linux-alapú HDInsight

A Linux-alapú HDInsight-fürtök a [mono (https://mono-project.com)](https://mono-project.com) használatával futtatják a .NET-alkalmazásokat. A Mono lehetővé teszi a .NET-összetevők, például a MapReduce-alkalmazások Linux-alapú HDInsight való használatát. Ebből a dokumentumból megtudhatja, hogyan telepíthet át Windows-alapú HDInsight-fürtökhöz létrehozott .NET-megoldásokat a Mono használatával a Linux-alapú HDInsight.

## <a name="mono-compatibility-with-net"></a>Mono-kompatibilitás a .NET-tel

A HDInsight 3,6-es verziója tartalmazza a Mono 4.2.1-es verzióját. További információ a HDInsight által tartalmazott mono-verzióról: HDInsight- [összetevő verziója](hdinsight-component-versioning.md).

További információ a mono és a .NET közötti kompatibilitásról: [monó kompatibilitási (https://www.mono-project.com/docs/about-mono/compatibility/)](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentum.

> [!IMPORTANT]  
> A SCP.NET keretrendszer kompatibilis a Mono-val. További információ a SCP.NET és a Mono használatával történő használatáról: a [Visual Studio C# használata a HDInsight Apache Stormához szükséges topológiák fejlesztéséhez](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatizált hordozhatóság elemzése

A [.net-hordozhatósági elemző](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) segítségével az alkalmazás és a Mono közötti inkompatibilitási jelentések készíthetők. A következő lépésekkel konfigurálhatja az elemzőt az alkalmazás monó-hordozhatóságra való vizsgálatához:

1. Telepítse a [.net-hordozhatósági elemzőt](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). A telepítés során válassza ki a használni kívánt Visual Studio-verziót.

2. A Visual Studio 2015-es verziójában válassza a > __hordozhatósági elemző beállításainak__ __elemzése__ lehetőséget, és győződjön meg arról, hogy a __4,5__ be van jelölve a __mono__ szakaszban.

    ![4,5 az analizátor beállításainál a Mono szakaszban be van jelölve](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    A konfiguráció mentéséhez kattintson __az OK gombra__ .

3. Válassza az __elemzés__ > a __szerelvény hordozhatóságának elemzése__lehetőséget. Válassza ki a megoldást tartalmazó szerelvényt, majd válassza a __Megnyitás__ lehetőséget az elemzés megkezdéséhez.

4. Az elemzés befejezése __után válassza az__ elemzés > az __elemzési jelentések megtekintése__lehetőséget. A __hordozhatósági elemzés eredményei__között válassza a __jelentés megnyitása__ lehetőséget a jelentés megnyitásához.

    ![A hordozhatósági elemző eredményei párbeszédpanel](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Az analizátor nem tud minden problémát megfogni a megoldásával. Például `c:\temp\file.txt` fájljának elérési útja rendben van, ha a Mono Windows rendszeren fut. Ugyanez az elérési út nem érvényes Linux platformon.

## <a name="manual-portability-analysis"></a>Manuális hordozhatóság elemzése

Hajtsa végre a kód manuális vizsgálatát az [alkalmazás-hordozhatóság (https://www.mono-project.com/docs/getting-started/application-portability/)](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentum) információi alapján.

## <a name="modify-and-build"></a>Módosítás és létrehozás

Továbbra is használhatja a Visual studiót a .NET-megoldások HDInsight való létrehozásához. Gondoskodnia kell azonban arról, hogy a projekt a .NET-keretrendszer 4,5-es használatára legyen konfigurálva.

## <a name="deploy-and-test"></a>Üzembe helyezés és tesztelés

Miután módosította a megoldást a .NET-hordozhatósági elemző ajánlásaival vagy manuális elemzéssel, tesztelni kell a HDInsight használatával. A megoldás Linux-alapú HDInsight-fürtön való tesztelése a kijavítani kívánt finom problémákat jelezheti. Javasoljuk, hogy a tesztelés során engedélyezze a további naplózást az alkalmazásban.

A naplók elérésével kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Hozzáférési Apache Hadoop a FONALas alkalmazások naplóihoz a Linux-alapú HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Következő lépések

* [A C# MapReduce használata a HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Felhasználó C# által definiált függvények használata Apache Hive és Apache Pig használatával](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Apache Storm C# -topológiák fejlesztése a HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
