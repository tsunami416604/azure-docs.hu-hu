---
title: A Visual Studio - Azure HDInsight (Hadoop) a Data Lake tools a Hive |} Microsoft Docs
description: Útmutató Apache Hive-lekérdezések futtatása az Apache Hadoop on Azure HDInsight a Data Lake tools for Visual Studio használatával.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 2b3e672a-1195-4fa5-afb7-b7b73937bfbe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 862a2aae2e9d417ccf9daf336177b23842dd3db7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201788"
---
# <a name="run-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>A Data Lake tools for Visual Studio használatával Hive-lekérdezések futtatása

Ismerje meg, hogyan használható a Data Lake tools for Visual Studio Apache Hive lekérdezés. A Data Lake tools engedélyezi, hogy könnyen létrehozása, elküldésére és megfigyelésére és a on Azure HDInsight Hadoop Hive-lekérdezéseket.

## <a id="prereq"></a>Előfeltételek

* (A HDInsight Hadoop) Azure HDInsight-fürtök

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (a következő verziók egyike):

    * A Visual Studio 2013 Community/Professional/Premium/Ultimate Update 4

    * A Visual Studio 2015 (minden kiadás)

    * A Visual Studio 2017 (minden kiadás)

* A HDInsight tools for Visual Studio vagy az Azure Data Lake tools for Visual Studio. Lásd: [első lépések a Visual Studio Hadoop tools for HDInsight használatával](apache-hadoop-visual-studio-tools-get-started.md) telepítése és konfigurálása az eszközök olvashat.

## <a id="run"></a> A Visual Studio használatával Hive-lekérdezések futtatása

1. Nyissa meg **Visual Studio** válassza **új** > **projekt** > **Azure Data Lake** > **HIVE** > **Hive alkalmazás**. Adja meg a projekt nevét.

2. Nyissa meg a **Script.hql** jön létre a projektet, és illessze be a következő hiveql:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * `DROP TABLE`: Ha a tábla létezik, a jelen nyilatkozatban törli őket.

   * `CREATE EXTERNAL TABLE`: Táblát hoz létre egy új "külső" struktúra. Külső táblák csak tárolja a tábladefiníció struktúra (az adatok marad az eredeti helyen).

     > [!NOTE]
     > Külső táblák kell használni, amikor külső forrásból frissítenie kell az alapul szolgáló adatokat várt. Például a MapReduce feladatot vagy Azure-szolgáltatás.
     >
     > A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában.

   * `ROW FORMAT`: Az adatok formázását Hive jelzi. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.

   * `STORED AS TEXTFILE LOCATION`: Hive közli, hogy az adatok a példaadatokat/könyvtárban tárolja, és szövegként tárolt.

   * `SELECT`: Jelölje ki az összes sorok számát ahol oszlop `t4` értéke `[ERROR]`. A jelen nyilatkozat értéket ad vissza, `3` mert három ezt az értéket tartalmazó sorok.

   * `INPUT__FILE__NAME LIKE '%.log'` -Közli struktúra, hogy azt kell csak vissza adatokat fájlok végződése. napló. Ehhez a záradékhoz korlátozza a keresési a sample.log fájlt, amely tartalmazza az adatokat.

3. Az eszköztáron válassza ki a **HDInsight-fürt** ehhez a lekérdezéshez használni kívánt. Válassza ki **Submit** fiókként szándékozik futtatni az utasításokat egy Hive-feladatot.

   ![Küldje el sáv](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. A **Hive feladat összegzése** jelenik meg, és a futó feladat információit jeleníti meg. Használja a **frissítése** hivatkozás frissíteni a feladat adatait, amíg a **feladatállapot** vált **befejezve**.

   ![a befejezett feladatok megjelenítése a feladat összegzése](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Használja a **Job Output** hivatkozásra a feladat eredményének megtekintéséhez. Megjeleníti `[ERROR] 3`, vagyis a lekérdezés által visszaadott érték.

6. Hive-lekérdezéseket a projekt létrehozása nélkül is futtathatja. Használatával **Server Explorer**, bontsa ki a **Azure** > **HDInsight**, kattintson a jobb gombbal a HDInsight-kiszolgáló, és válassza **Hive lekérdezés írása**.

7. Az a **temp.hql** dokumentumot, amely akkor jelenik meg, vegye fel a következő hiveql:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * `CREATE TABLE IF NOT EXISTS`: Létrehoz egy táblát, ha még nem létezik. Mivel a `EXTERNAL` kulcsszó nem használható, a jelen nyilatkozat egy belső táblát hoz létre. Belső táblák a Hive-adatraktárban tárolja, és Hive kezeli.

     > [!NOTE]
     > Eltérően `EXTERNAL` táblák, egy belső tábla eldobása is törli az alapul szolgáló adatokat.

   * `STORED AS ORC`: Tárolja az adatokat optimalizált sor oszlopos (ORC) formátumban. ORC formátuma egy magas optimalizált és hatékony Hive adatainak tárolásához.

   * `INSERT OVERWRITE ... SELECT`: Azon sorait kiválasztja a `log4jLogs` tartalmazó tábla `[ERROR]`, majd szúrja be az adatokat a `errorLogs` tábla.

8. Az eszköztáron válassza **Submit** a feladat futtatásához. Használja a **feladatállapot** annak meghatározásához, hogy a feladat sikeresen befejeződött.

9. Győződjön meg arról, hogy a feladat létrehozása a táblázat, használja a **Server Explorer** és bontsa ki a **Azure** > **HDInsight** > a HDInsight-fürt > **Hive adatbázisokat** > **alapértelmezett**. A **errorLogs** tábla és a **log4jLogs** táblázatban vannak felsorolva.

## <a id="nextsteps"></a>Következő lépések

Ahogy látja, a HDInsight tools for Visual Studio használata a HDInsight Hive-lekérdezések segítségével egyszerűen adja meg.

Általános információk a hdinsight Hive:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Más módszerekkel kapcsolatos információk a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)

* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

A HDInsight tools további információt a Visual Studio:

* [Kezdeti lépések a HDInsight tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
