---
title: "A Hive használata a hadooppal a webhelynapló elemzése - Azure HDInsight |} Microsoft Docs"
description: "Útmutató a Hive használata a Hdinsightban elemezhet. Egy naplófájlt használja bemenetként egy HDInsight táblába lesz, és a lekérdezést a HiveQL használatával."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: e1cdb786bb6049980aafc0213abf53013e342618
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>A Hive használata a Windows-alapú hdinsight eszközzel webhelyek naplóinak elemzése
Útmutató a HDInsight HiveQL ezen webhelyek naplóinak elemzése. A webhelynapló elemzése is használható, szegmentálhatja a célközönséget, hasonló tevékenységek alapján, a látogatók demográfiai szerinti kategóriák, valamint tudja meg a tartalom azokat nézet, a webhelyek származnak, és így tovább.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtök dolgozhat. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ez a példa a HDInsight-fürtök használandó webhelynaplókat látogatások gyakoriságáról a webhelyet a külső webhelyekről egy nap webhely naplófájlok elemzése. Lesz generálhat, hogy a felhasználók webhely hibák összegzését. Megtudhatja, hogyan:

* Egy Azure Blob Storage tárolóban, amely webhely naplófájlokat tartalmazó csatlakozni.
* Lesznek a naplók lekérdezni HIVE táblák létrehozása.
* Az adatok elemzése a HIVE-lekérdezések létrehozása.
* A Microsoft Excelben HDInsight csatlakozás (elemzett adatokat (ODBC). Nyissa meg az adatbázis-kapcsolat használatával.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

## <a name="prerequisites"></a>Előfeltételek
* Az Azure HDInsight Hadoop-fürthöz van kiépítve. Útmutatásért lásd: [Provision HDInsight-fürtök][hdinsight-provision].
* Rendelkeznie kell Microsoft Excel 2013 vagy az Excel 2010 telepítve.
* Rendelkeznie kell [Microsoft Hive ODBC-illesztő](http://www.microsoft.com/download/details.aspx?id=40886) Hive adatok importálása Excelbe.

## <a name="to-run-the-sample"></a>A minta futtatásához
1. Az a [Azure Portal](https://portal.azure.com/), a kezdőpultról (ha rögzítette a fürt nincs), kattintson a fürt csempéjére, amelyeken a minta futtatásához kívánja.
2. A fürt paneljén alatt **Gyorshivatkozások**, kattintson a **fürt irányítópult**, majd a **fürt irányítópult** panelen kattintson a **HDInsight-fürt Irányítópult**. Másik megoldásként közvetlenül megnyitásához az irányítópulton a következő URL-cím segítségével:

         https://<clustername>.azurehdinsight.net

    Amikor a rendszer kéri, a rendszergazdai felhasználónév és jelszó használatával a fürt létesítésekor használatával hitelesíteni.
3. A megnyíló weblapon, kattintson a **Getting Started gyűjteménye** fülre, majd a a **mintaadatokkal megoldások** kategória, kattintson a **Webhelynapló elemzése** minta.
4. Kövesse a megjelenő utasításokat a weblap a minta befejezéséhez.

## <a name="next-steps"></a>Következő lépések
Próbálja meg a következő mintát: [Érzékelőadatok elemzése a HDInsight Hive eszközzel](hdinsight-hive-analyze-sensor-data.md).

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
