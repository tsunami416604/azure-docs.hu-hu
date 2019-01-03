---
title: Webhelynapló elemzése – Azure HDInsight az Apache Hadoop Hive használata
description: Ismerje meg, hogyan használhatja az Apache Hive a HDInsight webhelynaplók elemzése. Naplófájl használata egy HDInsight-táblához bemenetként fog, és az adatok lekérdezése a HiveQL használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 30bfaad8fcc1a837a37689280149a6dbe20b7c1d
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628151"
---
# <a name="use-apache-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Az Apache Hive használata a HDInsight Windows-alapú webhelyek naplóinak elemzése
Útmutató a HDInsight HiveQL használva webhelyek naplóinak elemzése. A webhelynapló elemzése használható hasonló tevékenységek alapján közönségét szegmentálhatja, kategorizálhatja a webhely látogatói demográfiai által, és tudja meg a tartalmat, megtekintése, a websites származnak, és így tovább.

> [!IMPORTANT]  
> A jelen dokumentumban leírt lépések csak a Windows-alapú HDInsight-fürtökkel működik. HDInsight csak akkor használható a Windows-verziók alacsonyabb, mint a HDInsight 3.4-es. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Ebben a példában használhatja egy HDInsight-fürtöt a webhely a külső webhelyekről érkező napi látogatások gyakoriságának információkhoz juthat a webhely naplófájljainak elemzéséhez. Is létrehozhat a felhasználók által webhely-hibák összefoglalása. Az alábbiak végrehajtásának módját ismerheti meg:

* Csatlakozhat egy Azure Blob storage, amely tartalmazza a webhely naplófájljainak.
* Ezeket a naplókat lekérdezése HIVE táblák létrehozása.
* Hozzon létre az adatok elemzése a HIVE-lekérdezéseket.
* A Microsoft Excel használatával csatlakozhat a HDInsight (elemzett adatokat (ODBC). Nyissa meg az adatbázis-kapcsolat használatával.

![HDI. Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Előfeltételek
* Az Azure HDInsight az Apache Hadoop-fürt van kiépítve. Útmutatásért lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).
* Rendelkeznie kell a Microsoft Excel 2013 vagy az Excel 2010 telepítve van.
* Rendelkeznie kell [a Microsoft Hive ODBC-illesztő](https://www.microsoft.com/download/details.aspx?id=40886) , a Hive-adatok importálása Excel formátumba.

## <a name="to-run-the-sample"></a>A minta futtatása
1. A a [az Azure portal](https://portal.azure.com/), (ha rögzítette a fürt van) kezdőpultján kattintson a fürt csempéjére, amelyen szeretné, a minta futtatásához.
2. A fürt panelén alatt **Gyorshivatkozások**, kattintson a **fürt irányítópultja**, majd a **fürt irányítópultja** panelen kattintson a **HDInsight-fürt Irányítópult**. Másik megoldásként közvetlenül megnyithatja az irányítópult a következő URL-cím használatával:

         https://<clustername>.azurehdinsight.net

    Amikor a rendszer kéri, a rendszergazdai felhasználónevet és jelszót a fürt üzembe helyezésekor használt használatával hitelesíteni.
3. A webes megnyíló lapon kattintson a **Getting Started katalógus** lapon, majd a a **mintaadatokkal megoldások** kategória, kattintson a **Webhelynapló elemzése** minta.
4. Kövesse a megjelenő utasításokat a weblapon a minta befejezéséhez.

## <a name="next-steps"></a>További lépések
Próbálja ki a következő mintát: [HDInsight Hive-val érzékelőadatok elemzése a](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
