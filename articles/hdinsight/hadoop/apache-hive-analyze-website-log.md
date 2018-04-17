---
title: A Hive használata a hadooppal a webhelynapló elemzése - Azure HDInsight |} Microsoft Docs
description: Útmutató a Hive használata a Hdinsightban elemezhet. Egy naplófájlt használja bemenetként egy HDInsight táblába lesz, és a lekérdezést a HiveQL használatával.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6fb7b5c2-8df4-40b1-a9e2-6815080004f9
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 53a0560d3bc5a52069d5829b9c3bd353e0c37ef3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>A Hive használata a Windows-alapú hdinsight eszközzel webhelyek naplóinak elemzése
Útmutató a HDInsight HiveQL ezen webhelyek naplóinak elemzése. A webhelynapló elemzése is használható, szegmentálhatja a célközönséget, hasonló tevékenységek alapján, a látogatók demográfiai szerinti kategóriák, valamint tudja meg a tartalom azokat nézet, a webhelyek származnak, és így tovább.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtök dolgozhat. HDInsight csak érhető el a Windows korábbi, mint a HDInsight 3.4-es verziójához. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ez a példa a HDInsight-fürtök webhelynaplókat látogatások gyakoriságáról a webhelyet a külső webhelyekről egy nap webhely naplófájlok elemzése használhatja. Generálhat, hogy a felhasználók webhely hibák összegzését. Az alábbiak végrehajtásának módját ismerheti meg:

* Egy Azure Blob Storage tárolóban, amely webhely naplófájlokat tartalmazó csatlakozni.
* Lesznek a naplók lekérdezni HIVE táblák létrehozása.
* Az adatok elemzése a HIVE-lekérdezések létrehozása.
* A Microsoft Excelben HDInsight csatlakozás (elemzett adatokat (ODBC). Nyissa meg az adatbázis-kapcsolat használatával.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Előfeltételek
* Az Azure HDInsight Hadoop-fürthöz van kiépítve. Útmutatásért lásd: [Provision HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md).
* Rendelkeznie kell Microsoft Excel 2013 vagy az Excel 2010 telepítve.
* Rendelkeznie kell [Microsoft Hive ODBC-illesztő](http://www.microsoft.com/download/details.aspx?id=40886) Hive adatok importálása Excelbe.

## <a name="to-run-the-sample"></a>A minta futtatásához
1. Az a [Azure-portálon](https://portal.azure.com/), a kezdőpultról (ha rögzítette a fürt nincs), kattintson a fürt csempéjére, amelyeken a minta futtatásához kívánja.
2. A fürt paneljén alatt **Gyorshivatkozások**, kattintson a **fürt irányítópult**, majd a **fürt irányítópult** panelen kattintson a **HDInsight-fürt Irányítópult**. Másik megoldásként közvetlenül megnyitásához az irányítópulton a következő URL-cím segítségével:

         https://<clustername>.azurehdinsight.net

    Amikor a rendszer kéri, a rendszergazdai felhasználónév és jelszó használatával a fürt létesítésekor használatával hitelesíteni.
3. A megnyíló weblapon, kattintson a **Getting Started gyűjteménye** fülre, majd a a **mintaadatokkal megoldások** kategória, kattintson a **Webhelynapló elemzése** minta.
4. Kövesse a megjelenő utasításokat a weblap a minta befejezéséhez.

## <a name="next-steps"></a>További lépések
Próbálja meg a következő mintát: [Érzékelőadatok elemzése a HDInsight Hive eszközzel](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
