---
title: Az Apache Hive, a (az Apache Hadoop) a Data Lake tools for Visual Studio – Azure HDInsight
description: Megtudhatja, hogyan futtathat Apache Hive-lekérdezések az Apache Hadoop Azure HDInsight a Data Lake tools for Visual Studio használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861599"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>A Data Lake tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása

Útmutató a Data Lake tools for Visual Studio segítségével az Apache Hive-lekérdezést. A Data Lake-eszközök lehetővé teszik egyszerűen létrehozása, küldése és monitorozása az Azure HDInsight az Apache Hadoop Hive-lekérdezéseket.

## <a id="prereq"></a>Előfeltételek

* Egy HDInsight az Apache Hadoop-fürtöt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (a következő verziók egyike):

    * Visual Studio 2015, 2017 (any edition)
    * A Visual Studio 2013 Community/Professional/Premium/Ultimate 4. frissítéssel

* HDInsight tools for Visual Studio vagy az Azure Data Lake tools for Visual Studio. Lásd: [Visual Studio Hadoop tools for HDInsight használatának első lépései](apache-hadoop-visual-studio-tools-get-started.md) információ telepítésével és konfigurálásával az eszközöket.

## <a id="run"></a> A Visual Studio használatával, az Apache Hive-lekérdezések futtatása

Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

### <a name="ad-hoc"></a>Ad hoc

Ad hoc lekérdezéseket is végrehajtható vagy **Batch** vagy **interaktív** mód.

1. Nyissa meg **a Visual Studio**.

2. A **Server Explorer**, navigáljon a **Azure** > **HDInsight**.

3. Bontsa ki a **HDInsight**, és a fürt, ahol szeretné futtatni a lekérdezést, és kattintson a jobb gombbal **egy Hive-lekérdezés írása**.

4. Adja meg a következő hive-lekérdezés:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Válassza a **Végrehajtás** lehetőséget. Vegye figyelembe, hogy a végrehajtási mód alapértelmezés szerint a **interaktív**.

    ![Képernyőkép az interaktív Hive-lekérdezések végrehajtásáról](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Ugyanabból a lekérdezés futtatásához **Batch** mód, a váltógomb a legördülő lista **interaktív** való **Batch**. Vegye figyelembe, hogy módosítja a végrehajtási gomb **Execute** való **küldés**.

    ![Képernyőkép Hive-lekérdezés futtatásáról](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Például, ha beírja `SELECT * FROM`, IntelliSense listázza az összes javasolt táblanevet. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják. Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 1. példájáról](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 2. példájáról](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Kattintson a **Submit** (Küldés) vagy a **Submit (Advanced)** (Küldés (Speciális)) elemre.

   A speciális küldési lehetőséggel konfigurálja a szkript **Job Name** (Feladat neve), **Arguments** (Argumentumok), **Additional Configurations** (További konfigurációk) és **Status Directory** (Állapot könyvtár) elemeit:

    ![Képernyőkép egy HDInsight Hadoop Hive-lekérdezésről](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Lekérdezések küldése")

### <a name="hive-application"></a>Hive-alkalmazás

1. Nyissa meg **a Visual Studio**.

2. Lépjen a menüsoron **fájl** > **új** > **projekt**.

3. Az a **új projekt** ablakában navigáljon a **sablonok** > **az Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive alkalmazás**. 

4. Adja meg a projekt nevét, majd **OK**.

5. Nyissa meg a **Script.hql** jön létre a projektet, és illessze be a következő hiveql:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * `DROP TABLE`: Ha a tábla létezik, ez az utasítás törli őket.

   * `CREATE EXTERNAL TABLE`: A Hive egy új "külső" táblát hoz létre. Külső táblák csak Hive (az adatok az eredeti helyen van balra) a tábladefiníció tárolja.

     > [!NOTE]  
     > Külső táblák kell használni, amikor várhatóan az alapul szolgáló adatokat egy külső forrás frissíteni kell. Ha például egy MapReduce-feladatot vagy Azure-szolgáltatás.
     >
     > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.

   * `ROW FORMAT`: Arra utasítja a Hive, az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.

   * `STORED AS TEXTFILE LOCATION`: Hive arról tájékoztatja, hogy az adatokat a példaadatokat/könyvtárban tárolja, és szövegként tárolt.

   * `SELECT`: Válassza ki az összes sor számát ahol oszlop `t4` értéket tartalmazza `[ERROR]`. A jelen nyilatkozat egy értékét adja vissza `3` mert három sort, amely tartalmazza ezt az értéket.

   * `INPUT__FILE__NAME LIKE '%.log'` -Arra utasítja a Hive, hogy azt kell csak adatokat adja vissza a végződésű fájlokból. napló. Ehhez a záradékhoz korlátozza a keresést a sample.log fájlt, amely tartalmazza az adatokat.

6. Az eszköztáron válassza ki a **HDInsight-fürt** , amely ehhez a lekérdezéshez használandó. Válassza ki **küldés** fiókként való futtatásra az utasításokat egy Hive-feladatot.

   ![Küldje el sáv](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. A **Hive feladat összegzése** jelenik meg, és megjeleníti a futó feladat adatait. Használja a **frissítése** frissítse a feladat adatait, amíg a hivatkozás a **feladatállapot** vált **befejezve**.

   ![Feladat összegzése feladat megjelenítése](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Használja a **Feladatkimenet** hivatkozásra a feladat kimenetének megtekintéséhez. Megjeleníti `[ERROR] 3`, azaz a lekérdezés által visszaadott értéket.

### <a name="additional-example"></a>További példa

Ebben a példában támaszkodik a `log4jLogs` az előző lépésben létrehozott táblában.

1. A **Server Explorer**, kattintson a jobb gombbal a fürt, és válassza ki **egy Hive-lekérdezés írása**.

2. Adja meg a következő hive-lekérdezés:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások hajtsa végre a következő műveleteket:

    * `CREATE TABLE IF NOT EXISTS`: Egy táblát hoz létre, ha azt nem létezik. Mivel a `EXTERNAL` kulcsszó nem használható, ez az utasítás létrehoz egy belső táblázatban. A belső táblázatok Hive adattárházban tárolódnak, és Hive kezeli.
    
    > [!NOTE]  
    > Ellentétben `EXTERNAL` táblákat, egy belső tábla elvetését is törli az alapul szolgáló adatokat.

    * `STORED AS ORC`: Tárolja az adatokat optimalizált sor Oszlopalapú (ORC formátumban). ORC Hive-adatok tárolására szolgáló nagymértékben optimalizált és hatékony formátumban.
    
    * `INSERT OVERWRITE ... SELECT`: A sorok kiválasztása a `log4jLogs` tartalmazó tábla `[ERROR]`, majd beszúrja az adatokat a `errorLogs` tábla.

3. Hajtsa végre a lekérdezést a **Batch** mód.

4. Győződjön meg arról, hogy a feladat létrehozása a táblában, használja a **Server Explorer** csomópontot **Azure** > **HDInsight** > a HDInsight-fürt >  **Hive-adatbázisok** > **alapértelmezett**. A **hibanaplókat** tábla és a **log4jLogs** táblázatban szerepelnek.

## <a id="nextsteps"></a>Következő lépések

Amint láthatja, a HDInsight tools for Visual Studio a HDInsight Hive-lekérdezések dolgozhat egyszerű módot biztosítanak.

Általános információk a HDInsight Hive-ról:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)

* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Kapcsolatos további részletekért a HDInsight tools for Visual Studio:

* [Ismerkedés a HDInsight tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)