---
title: Az Apache Hive, a (az Apache Hadoop) a Data Lake tools for Visual Studio – Azure HDInsight
description: Megtudhatja, hogyan futtathat Apache Hive-lekérdezések az Apache Hadoop Azure HDInsight a Data Lake tools for Visual Studio használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 2b059c9a8808db3b2426db0d997df15196be604d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012079"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>A Data Lake tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása

Útmutató a Data Lake tools for Visual Studio segítségével az Apache Hive-lekérdezést. A Data Lake-eszközök lehetővé teszik egyszerűen létrehozása, küldése és monitorozása az Azure HDInsight az Apache Hadoop Hive-lekérdezéseket.

## <a id="prereq"></a>Előfeltételek

* Egy Azure HDInsight (a HDInsight Apache Hadoop) fürtön

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (a következő verziók egyike):

    * A Visual Studio 2013 Community/Professional/Premium/Ultimate 4. frissítéssel

    * A Visual Studio 2015 (bármely kiadás esetén)

    * A Visual Studio 2017 (bármely kiadás esetén)

* HDInsight tools for Visual Studio vagy az Azure Data Lake tools for Visual Studio. Lásd: [Visual Studio Hadoop tools for HDInsight használatának első lépései](apache-hadoop-visual-studio-tools-get-started.md) információ telepítésével és konfigurálásával az eszközöket.

## <a id="run"></a> A Visual Studio használatával Hive-lekérdezések futtatása

1. Nyissa meg **Visual Studio** válassza **új** > **projekt** > **az Azure Data Lake**  >   **HIVE-** > **Hive alkalmazás**. Adja meg a projekt nevét.

2. Nyissa meg a **Script.hql** jön létre a projektet, és illessze be a következő hiveql:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * `DROP TABLE`: Ha a tábla létezik, a jelen nyilatkozatban törli őket.

   * `CREATE EXTERNAL TABLE`: Egy új táblát hoz létre "külső" struktúrában. Külső táblák csak Hive (az adatok az eredeti helyen van balra) a tábladefiníció tárolja.

     > [!NOTE]
     > Külső táblák kell használni, amikor várhatóan az alapul szolgáló adatokat egy külső forrás frissíteni kell. Ha például egy MapReduce-feladatot vagy Azure-szolgáltatás.
     >
     > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.

   * `ROW FORMAT`: Arra utasítja a Hive, az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.

   * `STORED AS TEXTFILE LOCATION`: Arra utasítja a Hive, hogy az adatok a példaadatokat/könyvtárban tárolja, és szövegként tárolt.

   * `SELECT`: Válassza ki az összes sor számát ahol oszlop `t4` értéket tartalmazza `[ERROR]`. A jelen nyilatkozat egy értékét adja vissza `3` mert három sort, amely tartalmazza ezt az értéket.

   * `INPUT__FILE__NAME LIKE '%.log'` -Arra utasítja a Hive, hogy azt kell csak adatokat adja vissza a végződésű fájlokból. napló. Ehhez a záradékhoz korlátozza a keresést a sample.log fájlt, amely tartalmazza az adatokat.

3. Az eszköztáron válassza ki a **HDInsight-fürt** , amely ehhez a lekérdezéshez használandó. Válassza ki **küldés** fiókként való futtatásra az utasításokat egy Hive-feladatot.

   ![Küldje el sáv](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. A **Hive feladat összegzése** jelenik meg, és megjeleníti a futó feladat adatait. Használja a **frissítése** frissítse a feladat adatait, amíg a hivatkozás a **feladatállapot** vált **befejezve**.

   ![Feladat összegzése feladat megjelenítése](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Használja a **Feladatkimenet** hivatkozásra a feladat kimenetének megtekintéséhez. Megjeleníti `[ERROR] 3`, azaz a lekérdezés által visszaadott értéket.

6. Hive-lekérdezések a projekt létrehozása nélkül is futtathatja. Használatával **Server Explorer**, bontsa ki a **Azure** > **HDInsight**, kattintson a jobb gombbal a HDInsight-kiszolgáló, és válassza **egy Hive-lekérdezés írása** .

7. Az a **temp.hql** dokumentum, amely akkor jelenik meg, adja hozzá a következő hiveql:

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * `CREATE TABLE IF NOT EXISTS`: Egy táblát hoz létre, ha ezt még nem létezik. Mivel a `EXTERNAL` kulcsszó nem használható, ez az utasítás létrehoz egy belső táblázatban. A belső táblázatok Hive adattárházban tárolódnak, és Hive kezeli.

     > [!NOTE]
     > Ellentétben `EXTERNAL` táblákat, egy belső tábla elvetését is törli az alapul szolgáló adatokat.

   * `STORED AS ORC`: A adatokat tároló optimalizált sor Oszlopalapú (ORC formátumban). ORC Hive-adatok tárolására szolgáló nagymértékben optimalizált és hatékony formátumban.

   * `INSERT OVERWRITE ... SELECT`: A sorok kiválasztása a `log4jLogs` tartalmazó tábla `[ERROR]`, majd beszúrja az adatokat a `errorLogs` tábla.

8. Az eszköztáron válassza **küldés** futtatja a feladatot. Használja a **feladatállapot** meghatározni, hogy a feladat sikeresen befejeződött.

9. Győződjön meg arról, hogy a feladat létrehozása a táblában, használja a **Server Explorer** csomópontot **Azure** > **HDInsight** > a HDInsight-fürt >  **Hive-adatbázisok** > **alapértelmezett**. A **hibanaplókat** tábla és a **log4jLogs** táblázatban szerepelnek.

## <a id="nextsteps"></a>Következő lépések

Amint láthatja, a HDInsight tools for Visual Studio a HDInsight Hive-lekérdezések dolgozhat egyszerű módot biztosítanak.

Általános információk a HDInsight Hive-ról:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)

* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)

Kapcsolatos további részletekért a HDInsight tools for Visual Studio:

* [Ismerkedés a HDInsight tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
