---
title: .NET használata a Linux-alapú HDInsight - Azure Hadoop-MapReduce |} Microsoft Docs
description: Ismerje meg, az adatfolyamként történő MapReduce a Linux-alapú HDInsight .NET-alkalmazások használatáról.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 36b8f51122bad6614e63dfc58e09e5c1ca08f83d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400068"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>A Windows-alapú HDInsight a Linux-alapú HDInsight .NET megoldások áttelepítése

Linux-alapú HDInsight-fürtök használata [monó (https://mono-project.com) ](https://mono-project.com) .NET-alkalmazások futtatására. Monó lehetővé teszi a .NET-összetevők, például a MapReduce alkalmazások használatát a Linux-alapú hdinsight eszközzel. Ez a dokumentum megtudhatja, hogyan telepítheti át a Windows-alapú HDInsight-fürtök történő monó a Linux-alapú HDInsight-on együttműködésre létrehozott .NET megoldásokat.

## <a name="mono-compatibility-with-net"></a>A .NET monó kompatibilitási

Monó verzió 4.2.1 3.6 verzió HDInsight része. Monó részét képező HDInsight-verzión további információkért lásd: [HDInsight összetevő verziók](hdinsight-component-versioning.md). Egy adott verziójához monó telepítéséhez tekintse át a [telepítse vagy frissítse a monó](hdinsight-hadoop-install-mono.md) dokumentum.

Monó és a .NET között további információkért tekintse meg a [monó kompatibilitási (http://www.mono-project.com/docs/about-mono/compatibility/) ](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentum.

> [!IMPORTANT]
> Monó SCP.NET keretében esetén. További tájékoztatást SCP.NET Monó, lásd: [használja a C#-topológiák fejlesztése hdinsighton futó Apache stormra a Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Automatizált hordozhatóság elemzés

A [.NET hordozhatóság Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) jelentést kell készítenie az alkalmazás és az monó között ügyfélrendszereken használható. Ellenőrizze az alkalmazás monó hordozhatóság analyzer konfigurálásához tegye a következőket:

1. Telepítse a [.NET hordozhatóság Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). A telepítés során válassza ki a Visual Studio használandó verzióját.

2. Visual Studio 2015-öt, válassza ki __elemzés__ > __Hordozhatósága Analyzer beállítások__, és győződjön meg arról, hogy __4.5__ beadása a __monó__ szakasz.

    ![be van jelölve, a analyzer beállítások monó részében 4.5](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Válassza ki __OK__ a konfiguráció mentéséhez.

3. Válassza ki __elemzése__ > __szerelvény hordozhatóság elemzése__. A megoldás tartalmazó szerelvényre, majd válassza ki és __nyitott__ történő elemzésének indításához.

4. Elemzés végrehajtása után válassza ki a __elemzés__ > __elemzési jelentések megtekintése__. A __Hordozhatósága az elemzés eredményeinek__, jelölje be __nyissa meg a jelentés__ jelentés megnyitásához.

    ![Hordozhatóság analyzer eredmények párbeszédpanel](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]
> Az elemző eszköz nem dolgozza fel a megoldással minden probléma. Például egy fájl elérési útja `c:\temp\file.txt` OK tekintendő, ha monó fut a Windows. Az azonos elérési út nem egy Linux platformon érvényes.

## <a name="manual-portability-analysis"></a>Manuális hordozhatóság elemzés

A kód található információk segítségével manuális naplózási hajtsa végre a [alkalmazások hordozhatóságát (http://www.mono-project.com/docs/getting-started/application-portability/) ](http://www.mono-project.com/docs/getting-started/application-portability/) dokumentum.

## <a name="modify-and-build"></a>Módosítsa és létrehozása

Továbbra is a .NET-megoldások létrehozása a HDInsight Visual Studio használatával. Azonban gondoskodnia kell arról, hogy a projekt .NET-keretrendszer 4.5 használatára van konfigurálva.

## <a name="deploy-and-test"></a>Telepítse és tesztelje

Ha módosította a használatával a javaslatok, a .NET hordozhatóság Analyzer vagy egy manuális elemzési megoldás, a hdinsight eszközzel kell tesztelni. A Linux-alapú HDInsight-fürt megoldás tesztelési felfedheti finom problémák, amelyek javítani kell. Azt javasoljuk, hogy további naplózás engedélyezése az alkalmazás azt tesztelése során.

A naplók elérése további információkért lásd a következő dokumentumokat:

* [YARN-alkalmazásnaplók elérése Linux-alapú HDInsighton](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>További lépések

* [C# használata a HDInsight MapReduce](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [C# felhasználó által definiált függvények használata a Hive és a Pig használatával](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [A HDInsight alatt futó Storm a C#-topológiák fejlesztése](storm/apache-storm-develop-csharp-visual-studio-topology.md)
