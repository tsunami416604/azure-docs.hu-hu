---
title: A .NET használata az Apache Sparkhoz az Azure Synapse Analytics segítségével
description: Ismerje meg a .NET és az Apache Spark használatával a kötegelt feldolgozást, a valós idejű streamelést, a gépi tanulást és az ad hoc lekérdezések írását az Azure Synapse Analytics-jegyzetfüzetekben.
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430512"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>A .NET használata az Apache Sparkhoz az Azure Synapse Analytics segítségével

[A .NET for Apache Spark](https://dot.net/spark) ingyenes, nyílt forráskódú és platformfüggetlen .NET támogatás a Sparkhoz. Az .NET for Apache Spark .NET-kötéseket biztosít a Spark számára, amelyek lehetővé teszik a Spark API-k c# és f# keresztüli elérését. A .NET for Apache Spark használatával a .NET használatával a Spark felhasználó által definiált függvényeit is megírhatja és végrehajthatja. A Spark .

A .NET for Apache Spark használatával elemezheti az adatokat a Spark kötegelt feldolgozásdefinícióin vagy az interaktív Azure Synapse Analytics-jegyzetfüzetek segítségével. Ebben a cikkben megtudhatja, hogyan használhatja a .NET for Apache Spark az Azure Synapse mindkét technikával. 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Kötegelt feladatok küldése a Spark feladatdefiníció használatával

Látogasson el az oktatóanyagba, amelyből megtudhatja, hogyan hozhat [létre Apache Spark-feladatdefiníciókat a Synapse Spark-készletekhez.](apache-spark-job-definitions.md) Ha nem csomagolta be az alkalmazást az Azure Synapse-nak való elküldéshez, hajtsa végre az alábbi lépéseket.

1. Az alkalmazás közzétételéhez futtassa a következő parancsokat. Ügyeljen arra, hogy cserélje le *a mySparkApp-ot* az alkalmazás elérési útjára.

   **Windows rendszerben:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **Linuxalatt:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET az Apache Sparkhoz az Azure Synapse Analytics-jegyzetfüzetekben

Új jegyzetfüzet létrehozásakor olyan nyelvi rendszermagot kell választania, amelyet az üzleti logikájának kifejezésére kíván fordítani. A kernel számos nyelvet támogat, beleértve a C#-ot is.

Ha a .NET for Apache Sparkot szeretné használni az Azure Synapse Analytics-jegyzetfüzetében, válassza a **.NET Spark (C#)** elemet kernelként, és csatolja a jegyzetfüzetet egy meglévő Spark-készlethez.

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET az Apache Sparkhoz az Azure Synapse Analytics-jegyzetfüzetekben 

A notebookok nagyszerű lehetőséget jelentenek a .NET Apache Spark-folyamatokhoz és forgatókönyvekhez való prototípusához. Az adatok gyors és hatékony működésével, megértésével, szűrésével, megjelenítésével és megjelenítésével is megkezdheti a munkát. Adatmérnökök, adatszakértők, üzleti elemzők és gépi tanulási mérnökök egy megosztott, interaktív dokumentumon keresztül is együttműködhetnek. Az adatok feltárásának azonnali eredményeit láthatja, és az adatokat ugyanabban a jegyzetfüzetben jelenítheti meg.

### <a name="how-to-use-notebooks"></a>A jegyzetfüzetek használata

Új jegyzetfüzet létrehozásakor olyan nyelvi rendszermagot választunk, amelyet az üzleti logikád kifejezésére kívánunk fordítani. A kernel számos nyelvet támogat, beleértve a C#-ot is. 

Ha a .NET for Apache Sparkot szeretné használni az Azure Synapse Analytics-jegyzetfüzetében, válassza a **.NET Spark (C#)** elemet kernelként, és csatolja a jegyzetfüzetet egy meglévő Spark-készlethez. 

A .NET Spark notebook a .NET interaktív élményen alapul, és interaktív C# élményt nyújt a .NET `spark` a Spark számára a dobozból, a Spark munkamenet-változó előre definiált használatával.

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# kernel szolgáltatásai

A következő funkciók érhetők el, ha a .NET for Apache Spark ot használja az Azure Synapse Analytics notebookban:

* Deklaratív HTML: A cellákból kimenetet hozhat létre HTML-szintaxissal, például fejlécekkel, listajeles listákkal, sőt képek megjelenítésével.
* Egyszerű C# utasítások (például hozzárendelések, nyomtatás konzolra, kivételek kidobása és így tovább).
* Többsoros C# kódblokkok (például ha utasítások, foreach hurkok, osztálydefiníciók és így tovább).
* Hozzáférés a szabványos C# könyvtárhoz (például System, LINQ, Enumerables és így tovább).
* A [C# 8.0 nyelvi funkcióinak](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)támogatása .
* "spark" egy előre definiált változó, amely hozzáférést biztosít az Apache Spark-munkamenethez.
* Az Apache [Sparkon belül futtatható .NET felhasználó által definiált függvények](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)definiálásának támogatása.
* Támogatja a Spark-feladatok kimenetének megjelenítését különböző diagramok (például vonal, sáv vagy hisztogram) és elrendezések (például egy, eltakart, és így tovább) használatával a `XPlot.Plotly` könyvtár használatával.
* NuGet csomagok beillesztése a C# noteszgépbe.

## <a name="next-steps"></a>További lépések

* [Az Apache Spark dokumentációja .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET Interaktív](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)