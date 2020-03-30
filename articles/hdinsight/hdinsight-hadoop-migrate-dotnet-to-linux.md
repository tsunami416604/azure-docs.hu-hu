---
title: A .NET és a Hadoop MapReduce használata Linux-alapú HDInsight - Azure
description: Ismerje meg, hogyan használhatja a .NET alkalmazásokat a MapReduce streameléséhez Linux-alapú HDInsight-alapú.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272369"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>A Windows-alapú HDInsight .NET megoldásainak áttelepítése Linux alapú HDInsight-ra

A Linux-alapú HDInsight-fürtök [a Mono (ahttps://mono-project.com) ](https://mono-project.com) .NET alkalmazások futtatásához) használatosak. A Monó lehetővé teszi a .NET-összetevők, például a MapReduce alkalmazások használatát Linux-alapú HDInsight-mal. Ebből a dokumentumból megtudhatja, hogyan telepítheti át a Windows-alapú HDInsight-fürtökhöz létrehozott .NET-megoldásokat, hogy a Monóval működjön Linux alapú HDInsight-alapú.

## <a name="mono-compatibility-with-net"></a>Monó kompatibilitás a .NET-tel

A 4.2.1-es monó a HDInsight 3.6-os verziójához tartozik. A HDInsight részét képező Monó verziójáról a [HDInsight összetevő-verziói](hdinsight-component-versioning.md)című témakörben talál további információt.

A Mono és a .NET közötti kompatibilitásról a [Mono compatibility (https://www.mono-project.com/docs/about-mono/compatibility/) ](https://www.mono-project.com/docs/about-mono/compatibility/) dokumentum.

> [!IMPORTANT]  
> A SCP.NET keretrendszer kompatibilis a Monóval. A SCP.NET monival való használatáról a [Visual Studio használata Az Apache Storm C# topológiáinak fejlesztéséhez a HDInsight-on](storm/apache-storm-develop-csharp-visual-studio-topology.md)című témakörben talál további információt.

## <a name="automated-portability-analysis"></a>Automatizált hordozhatóság-elemzés

A [.NET hordozhatóság-elemző](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) segítségével jelentést készíthet az alkalmazás és a Monó közötti inkompatibilitásról. Az alábbi lépésekkel konfigurálhatja az analizátort az alkalmazás monó hordozhatóságának ellenőrzésére:

1. Telepítse a [.NET hordozhatósági elemzőt](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). A telepítés során válassza ki a Visual Studio használni kívánt verzióját.

2. A Visual Studio 2015-ben válassza a__Hordozhatósági elemző beállításainak__ __elemzése__ > lehetőséget, és győződjön meg arról, hogy a __4.5__ be van jelölve a __Mona__ szakaszban.

    ![4.5 ellenőrzött Mono részben az analizátor beállításait](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    A konfiguráció mentéséhez válassza az __OK gombot.__

3. Válassza __az Elemző__ > __elemző elemének hordozhatósága__lehetőséget. Jelölje ki a megoldást tartalmazó szerelvényt, majd az elemzés megkezdéséhez kattintson a __Megnyitás__ gombra.

4. Az elemzés befejezése után válassza __az Elemzési__ > __jelentések elemzése__lehetőséget. A __Hordozhatóság-elemzés eredményei között__válassza a Jelentés __megnyitása__ lehetőséget a jelentés megnyitásához.

    ![A hordozhatóság elemzőjének eredménypárbeszédpanele](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Az analizátor nem tud minden problémát elkapni a megoldással. Ha például a `c:\temp\file.txt` Monó windowson fut, a fájl elérési útja rendben van. Ugyanez az elérési út nem érvényes Linux platformon.

## <a name="manual-portability-analysis"></a>Kézi hordozhatóság-elemzés

Végezze el a kód manuális ellenőrzését az [alkalmazáshttps://www.mono-project.com/docs/getting-started/application-portability/) hordozhatósága (](https://www.mono-project.com/docs/getting-started/application-portability/) dokumentum) adatainak felhasználásával.

## <a name="modify-and-build"></a>Módosítás és összeállítás

Továbbra is használhatja a Visual Studio alkalmazást a .NET-megoldások létrehozásához a HDInsight számára. A projektnek azonban a .NET Framework 4.5 használatára van konfigurálva.

## <a name="deploy-and-test"></a>Telepítés és tesztelés

Miután módosította a megoldást a .NET hordozhatósági elemző ajánlásaival vagy egy manuális elemzéssel, tesztelnie kell azt a HDInsight segítségével. A megoldás Linux-alapú HDInsight-fürtön való tesztelése olyan finom problémákat tárhat fel, amelyeket ki kell javítani. Azt javasoljuk, hogy a tesztelés során engedélyezze a további naplózást az alkalmazásban.

A naplók eléréséről további információt az alábbi dokumentumokban talál:

* [Az Apache Hadoop YARN alkalmazásnaplók elérése Linux-alapú HDInsight-alapú](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>További lépések

* [C# használata a MapReduce használatával a HDInsighton](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [C# felhasználó által definiált függvények használata Apache Hive és Apache Pig használatával](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [C# topológiák fejlesztése apache stormhoz a HDInsighton](storm/apache-storm-develop-csharp-visual-studio-topology.md)
