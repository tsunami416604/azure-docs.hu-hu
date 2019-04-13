---
title: A Linux-alapú HDInsight - Azure Hadoop MapReduce használata a .NET
description: Ismerje meg, hogyan használja a .NET-alkalmazások a Linux-alapú HDInsight MapReduce streameléshez.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: adab50b7325be96830ee937153d110754cc0b552
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549797"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>.NET – megoldások áttelepítése Linux-alapú HDInsight a Windows-alapú HDInsight esetében

Linux-alapú HDInsight-fürtök használata [Mono (https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. Mono lehetővé teszi, hogy a .NET-összetevők, például alkalmazások MapReduce használata a Linux-alapú HDInsight. Ebből a dokumentumból megtudhatja, hogyan dolgozhat a Mono a Linux-alapú HDInsight Windows-alapú HDInsight-fürtök számára létrehozott .NET – megoldások áttelepítése.

## <a name="mono-compatibility-with-net"></a>Monó kompatibilitási .NET-tel

Monó verzió 4.2.1 megtalálható a HDInsight 3.6-os verzióját. További információ a Mono HDInsight mellékelt verzióját, lásd: [HDInsight összetevő verziók](hdinsight-component-versioning.md).

A .NET és a Mono között további információkért lásd: a [monó kompatibilitási (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentumot.

> [!IMPORTANT]  
> Az SCP.NET keretrendszer Mono esetén. A Mono való használatához készült SCP.NET további információkért lásd: [C#-topológiák fejlesztése a HDInsight az Apache stormmal történő használatát a Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatizált hordozhatóságot elemzése

A [.NET hordozhatóságot Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) létrehozhat egy jelentést, az alkalmazás és a Mono között inkompatibilitás használható. A következő lépések segítségével ellenőrizheti az alkalmazás monó hordozhatóság az elemző konfigurálása:

1. Telepítse a [.NET hordozhatóságot Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). A telepítés során válassza ki a Visual Studio használandó verzióját.

2. Visual Studio 2015-ben válassza __elemzés__ > __hordozhatóságot Analyzer beállítások__, és ellenőrizze, hogy __4.5__ be van jelölve, a __Mono__ szakaszban.

    ![4.5 be van jelölve, Monó szakaszban, az elemző beállításai](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Válassza ki __OK__ a konfiguráció mentéséhez.

3. Válassza ki __elemzése__ > __szerelvény hordozhatóságot elemzése__. Válassza ki a megoldást tartalmazó szerelvény, majd a __nyílt__ elemzési megkezdéséhez.

4. Elemzés befejezése után jelölje ki a __elemzés__ > __problémaelemző jelentések megtekintéséhez__. A __hordozhatóságot elemzési eredmények__válassza __nyissa meg a jelentés__ , nyisson meg egy jelentést.

    ![Hordozhatóságot analyzer eredmények párbeszédpanel](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Az elemző nem tényleges minden egyes probléma megoldását. Ha például egy fájl elérési útját `c:\temp\file.txt` akkor megfelelő, ha a Mono Windows fut-e. Az azonos elérési út nem egy Linux platformon érvényes.

## <a name="manual-portability-analysis"></a>Manuális hordozhatóságot elemzése

A kód található információk segítségével a manuális naplózási hajtsa végre a [alkalmazás-hordozhatóságot (https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentumot.

## <a name="modify-and-build"></a>Módosíthatja, és hozhat létre

Továbbra is hozhatja a .NET-megoldások létrehozását a HDInsight Visual Studio. Azonban biztosítania kell, hogy a projekt .NET-keretrendszer 4.5-ös verziójának használatára van konfigurálva.

## <a name="deploy-and-test"></a>Üzembe helyezéséhez és teszteléséhez

Miután módosította a használatával a javaslatokat, a .NET-hordozhatóságot elemző vagy egy manuális elemzési megoldás, tesztelni kell a HDInsight. A megoldás egy Linux-alapú HDInsight-fürtöt a tesztelési felfedheti kisebb problémák, amelyeket ki kell javítani kell. Azt javasoljuk, hogy engedélyezze a tesztelés során az alkalmazás további naplózás.

További információ a naplók elérése az alábbi dokumentumokban talál:

* [Az Apache Hadoop YARN-alkalmazásnaplók elérése Linux-alapú HDInsight a](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>További lépések

* [C# használata a HDInsight MapReduce](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Használat C# az Apache Hive- és Apache Pig, felhasználó által definiált függvények](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Fejlesztés C# a HDInsight Apache Storm-topológiák](storm/apache-storm-develop-csharp-visual-studio-topology.md)
