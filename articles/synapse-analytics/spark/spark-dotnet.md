---
title: A .NET használata Apache Sparkhoz az Azure szinapszis Analytics használatával
description: Ismerje meg, hogyan használhatja a .NET és a Apache Spark a kötegelt feldolgozásra, a valós idejű folyamatos átvitelre, a gépi tanulásra és az Azure szinapszis Analytics jegyzetfüzetekben az alkalmi lekérdezések írására.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 3882352c7e1d484818a58d7bd4410cbd66bd6637
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587799"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>A .NET használata Apache Sparkhoz az Azure szinapszis Analytics használatával

A [.net for Apache Spark](https://dot.net/spark) ingyenes, nyílt forráskódú és platformfüggetlen .net-támogatás a sparkhoz. A .NET for Apache Spark olyan .NET-kötéseket biztosít a Sparkhoz, amelyek lehetővé teszik a Spark API-k C# és F # használatával való elérését. A .NET for Apache Spark lehetővé teszi, hogy a .NET használatával a Spark felhasználó által definiált függvényeit tudja írni és végrehajtani. A Sparkhoz készült .NET API-k lehetővé teszik a Spark összes aspektusának elérését, amely megkönnyíti az adatelemzést, beleértve a Spark SQL és a Structured streaming szolgáltatásait is.

Az adatok elemzéséhez a .NET-tel Apache Spark a Spark batch-feladatdefiníciók vagy az interaktív Azure szinapszis Analytics-jegyzetfüzetek használatával. Ebből a cikkből megtudhatja, hogyan használhatja a .NET-et az Azure szinapszis-vel való Apache Sparkére mindkét módszer használatával.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Batch-feladatok elküldése a Spark-feladat definíciójának használatával

Az oktatóanyagból megtudhatja, hogyan [hozhat létre Apache Spark-feladatdefiníciók létrehozásához a szinapszis Spark-készleteket](apache-spark-job-definitions.md)az Azure szinapszis Analytics használatával. Ha nem csomagolta be az alkalmazást az Azure Szinapszisba való küldéshez, hajtsa végre az alábbi lépéseket.

1. Az alkalmazás közzétételéhez futtassa az alábbi parancsokat. Ügyeljen arra, hogy a *mySparkApp* az alkalmazás elérési útjára cserélje.

   **Windows rendszeren:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Linux rendszeren:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET Apache Spark az Azure szinapszis Analytics-jegyzetfüzetekben

Új jegyzetfüzet létrehozásakor ki kell választania az üzleti logikát kifejező nyelvi kernelt. Több nyelven is van kernel-támogatás, beleértve a C#-ot is.

Ha a .NET-et szeretné használni az Azure szinapszis Analytics-jegyzetfüzetben lévő Apache Sparkhoz, válassza a **.net Spark (C#)** lehetőséget kernelként, és csatolja a notebookot egy meglévő Spark-készlethez.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET Apache Spark az Azure szinapszis Analytics-jegyzetfüzetekben 

A jegyzetfüzetek nagyszerű lehetőséget biztosítanak a .NET Apache Spark folyamatokhoz és forgatókönyvekhez való prototípusának megadására. Megkezdheti az adatfeldolgozást, az ismeretek megismerését, szűrését, megjelenítését és megjelenítését gyorsan és hatékonyan. Az adatmérnökök, az adatszakértők, az üzleti elemzők és a gépi tanulási mérnökök képesek együttműködni egy megosztott, interaktív dokumentumon. Láthatja az adatok feltárásának azonnali eredményeit, és megtekintheti az adatait ugyanabban a jegyzetfüzetben.

### <a name="how-to-use-notebooks"></a>Jegyzetfüzetek használata

Új jegyzetfüzet létrehozásakor ki kell választania az üzleti logikát kifejező nyelvi kernelt. Több nyelven is van kernel-támogatás, beleértve a C#-ot is.

Ha a .NET-et szeretné használni az Azure szinapszis Analytics-jegyzetfüzetben lévő Apache Sparkhoz, válassza a **.net Spark (C#)** lehetőséget kernelként, és csatolja a notebookot egy meglévő Spark-készlethez.

A .net Spark notebook a .NET interaktív felületén alapul, és interaktív C#-élményt biztosít, amely lehetővé teszi a .NET for Spark használatát a `spark` már előre definiált Spark-munkamenet változóval rendelkező dobozon.

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# kernel-funkciók

A következő szolgáltatások érhetők el, ha a .NET-et használja a Apache Sparkhoz az Azure szinapszis Analytics jegyzetfüzetben:

* Deklaratív HTML: kimenetet hozhat létre a cellákból HTML-szintaxissal, például fejlécekkel, listajeles listákkal, sőt képeket is megjelenítve.
* Egyszerű C#-utasítások (például hozzárendelések, konzolra történő nyomtatás, kivételek ledobása stb.).
* Többsoros C# kódok (például ha utasítások, foreach hurkok, osztály-definíciók stb.).
* Hozzáférés a szabványos C#-könyvtárhoz (például rendszer, LINQ, Enumerables stb.).
* A [C# 8,0 nyelvi funkcióinak](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)támogatása.
* a "Spark" egy előre definiált változó, amely hozzáférést biztosít a Apache Spark-munkamenethez.
* A [Apache Sparkon belül futtatható .net-felhasználó által definiált függvények](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)definiálásának támogatása.
* A Spark-feladatok kimenetének a különböző diagramok (például a vonal, a sáv vagy a hisztogram) és az elrendezés (például egyetlen, átfedésben lévő stb.) használatával történő megjelenítésének támogatása a `XPlot.Plotly` könyvtár segítségével.
* NuGet-csomagok belefoglalása a C# jegyzetfüzetbe.

## <a name="next-steps"></a>További lépések

* [.NET Apache Spark dokumentációhoz](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET interaktív](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)