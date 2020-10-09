---
title: A .NET használata Apache Spark
description: Ismerje meg, hogyan használhatja a .NET és a Apache Spark a kötegelt feldolgozásra, a valós idejű folyamatos átvitelre, a gépi tanulásra és az Azure szinapszis Analytics jegyzetfüzetekben az alkalmi lekérdezések írására.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 4b512d0817c56bc2daabff057c8bc4aa1afa1dee
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826505"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>A .NET használata Apache Sparkhoz az Azure szinapszis Analytics használatával

[A .net for Apache Spark](https://dot.net/spark) ingyenes, nyílt forráskódú és platformfüggetlen .net-támogatást biztosít a Spark számára. 

.NET-kötéseket biztosít a Sparkhoz, ami lehetővé teszi a Spark API-k elérését a C# és az F # használatával. A .NET for Apache Spark esetében a .NET-ben írt Spark felhasználó által definiált függvényeit is megírhatja és végrehajthatja. A Sparkhoz készült .NET API-k lehetővé teszik a Spark DataFrames összes olyan aspektusának elérését, amely segít az adatelemzésben, beleértve a Spark SQL, a Delta Lake és a Structured streaming szolgáltatásait.

Az adatok elemzéséhez a .NET-tel Apache Spark a Spark batch-feladatdefiníciók vagy az interaktív Azure szinapszis Analytics-jegyzetfüzetek használatával. Ebből a cikkből megtudhatja, hogyan használhatja a .NET-et az Azure szinapszis-vel való Apache Sparkére mindkét módszer használatával.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Batch-feladatok elküldése a Spark-feladat definíciójának használatával

Az oktatóanyagból megtudhatja, hogyan [hozhat létre Apache Spark-feladatdefiníciók létrehozásához a szinapszis Spark-készleteket](apache-spark-job-definitions.md)az Azure szinapszis Analytics használatával. Ha nem csomagolta be az alkalmazást az Azure Szinapszisba való küldéshez, hajtsa végre az alábbi lépéseket.

1. Az alkalmazás közzétételéhez futtassa az alábbi parancsokat. Ügyeljen arra, hogy a *mySparkApp* az alkalmazás elérési útjára cserélje.

   **Windows rendszeren:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **Linux rendszeren:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. Az 1. lépés eredményeképpen létrehozott közzétételi mappa tartalmának zip- `publish.zip` fájlja. Az összes szerelvénynek a ZIP-fájl első rétegében kell lennie, és nem lehet köztes mappa réteg. Ez azt jelenti, hogy a kicsomagoláskor a rendszer az `publish.zip` összes szerelvényt kinyeri az aktuális munkakönyvtárba.

    **Windows rendszeren:**

A kinyerési program, például a [7-zip](https://www.7-zip.org/) vagy a [WinZip](https://www.winzip.com/)használatával kinyerheti a fájlt a bin könyvtárba az összes közzétett bináris fájllal.

     **On Linux:**

     1. Open a bash shell and cd into the bin directory with all the published binaries and run the following command.

       ```bash
       zip -r publish.zip
       ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET Apache Spark az Azure szinapszis Analytics-jegyzetfüzetekben 

A jegyzetfüzetek nagyszerű lehetőséget biztosítanak a .NET Apache Spark folyamatokhoz és forgatókönyvekhez való prototípusának megadására. Megkezdheti az adatfeldolgozást, az ismeretek megismerését, szűrését, megjelenítését és megjelenítését gyorsan és hatékonyan. 

Az adatmérnökök, az adatszakértők, az üzleti elemzők és a gépi tanulási mérnökök képesek együttműködni egy megosztott, interaktív dokumentumon. Láthatja az adatok feltárásának azonnali eredményeit, és megtekintheti az adatait ugyanabban a jegyzetfüzetben.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>A .NET használata Apache Spark notebookokhoz

Új jegyzetfüzet létrehozásakor ki kell választania az üzleti logikát kifejező nyelvi kernelt. A kernel támogatása több nyelven is elérhető, beleértve a C# nyelvet is.

Ha a .NET-et szeretné használni az Azure szinapszis Analytics-jegyzetfüzetben lévő Apache Sparkhoz, válassza a **.net Spark (C#)** lehetőséget kernelként, és csatolja a notebookot egy meglévő Spark-készlethez.

A .net Spark notebook a .NET interaktív felületén alapul, és interaktív C#-élményt biztosít, amely lehetővé teszi a .NET for Spark használatát a `spark` már előre definiált Spark-munkamenet változóval rendelkező dobozon.

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET Apache Spark C# kernel-funkciókhoz

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

## <a name="next-steps"></a>Következő lépések

* [.NET Apache Spark dokumentációhoz](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET interaktív](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
