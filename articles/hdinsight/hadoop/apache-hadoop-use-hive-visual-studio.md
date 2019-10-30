---
title: Apache Hive & Data Lake Tools for Visual Studio – Azure HDInsight
description: Ismerje meg, hogy a Visual studióhoz készült Data Lake Tools segítségével hogyan futtathat Apache Hive-lekérdezéseket az Azure HDInsight Apache Hadoop használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 37652a8ca9750e6b33bd2744bda386eaba92b025
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044528"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Apache Hive lekérdezések futtatása a Visual studióhoz készült Data Lake Tools használatával

Megtudhatja, hogyan használhatja a Visual studióhoz készült Data Lake-eszközöket a Apache Hive lekérdezéséhez. A Data Lake-eszközök lehetővé teszik a kaptár-lekérdezések egyszerű létrehozását, beküldését és figyelését az Azure HDInsight Apache Hadoop.

## <a id="prereq"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (a következő verziók egyike):

    * Visual Studio 2015, 2017 (bármilyen kiadás)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate, 4. frissítéssel

* A Visual studióhoz készült HDInsight-eszközök és a Visual studióhoz készült Azure Data Lake eszközök. Az eszközök telepítésével és konfigurálásával kapcsolatos információkért tekintse meg a [Visual Studio Hadoop-eszközök HDInsight való használatának első lépéseit](apache-hadoop-visual-studio-tools-get-started.md) ismertető témakört.

## <a id="run"></a>Apache Hive lekérdezések futtatása a Visual Studio használatával

Hive-lekérdezések létrehozására és futtatására két lehetősége van:

* Alkalmi lekérdezések létrehozása
* Hive alkalmazás létrehozása

### <a name="ad-hoc"></a>Ad hoc

Az alkalmi lekérdezések **kötegelt** vagy **interaktív** módban is végrehajthatók.

1. Nyissa meg a **Visual studiót**.

2. A **Server Explorerben**navigáljon az **Azure** > **HDInsight**.

3. Bontsa ki a **HDInsight**csomópontot, majd kattintson a jobb gombbal arra a fürtre, amelyen futtatni szeretné a lekérdezést, majd válassza a **kaptár-lekérdezés írása**lehetőséget.

4. Adja meg a következő kaptár-lekérdezést:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Válassza a **Végrehajtás** lehetőséget. Vegye figyelembe, hogy a végrehajtási mód alapértelmezés szerint **interaktív**.

    ![Képernyőkép az interaktív Hive-lekérdezések végrehajtásáról](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Ha ugyanezt a lekérdezést **Batch** módban szeretné futtatni, a legördülő listát az **Interaktívról** a **kötegbe**kapcsolhatja. Vegye figyelembe, hogy a végrehajtás gomb **a végrehajtásról** a **Küldés**elemre változik.

    ![Képernyőkép Hive-lekérdezés futtatásáról](./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png)

    A Hive szerkesztője támogatja az IntelliSense-t. A Data Lake Tools for Visual Studio támogatja a távoli metaadatok betöltését a Hive-szkript szerkesztésekor. Ha például beírja a `SELECT * FROM`, az IntelliSense a javasolt táblanév összes nevét listázza. Amikor megad egy táblanevet, az IntelliSense listázza az oszlopneveket. Az eszközök a legtöbb Hive DML-utasítást, -segédlekérdezést és beépített UDF-et támogatják. Az IntelliSense csak a HDInsight eszköztáron kijelölt fürt metaadatait javasolja.

    ![Képernyőkép egy HDInsight Visual Studio-eszközökről, IntelliSense-példa 1](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Képernyőkép a HDInsight Visual Studio Tools IntelliSense 2. példáról](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Kattintson a **Submit** (Küldés) vagy a **Submit (Advanced)** (Küldés (Speciális)) elemre.

   A speciális küldési lehetőséggel konfigurálja a szkript **Job Name** (Feladat neve), **Arguments** (Argumentumok), **Additional Configurations** (További konfigurációk) és **Status Directory** (Állapot könyvtár) elemeit:

    ![Képernyőkép egy HDInsight Hadoop-struktúra lekérdezéséről](./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png "Lekérdezések beküldése")

### <a name="hive-application"></a>Struktúra alkalmazása

1. Nyissa meg a **Visual studiót**.

2. A menüsávban navigáljon a **fájl** > **új** > **projekt**elemre.

3. Az **új projekt** ablakban navigáljon a **sablonok** > **Azure Data Lake** > **kaptár (HDInsight)**  > **kaptár alkalmazáshoz**. 

4. Adja meg a projekt nevét, majd kattintson **az OK gombra**.

5. Nyissa meg a projekttel létrehozott **script. HQL** fájlt, és illessze be a következő HiveQL-utasításokat:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Ezek az utasítások a következő műveleteket hajtják végre:

   * `DROP TABLE`: Ha a tábla létezik, az utasítás törli.

   * `CREATE EXTERNAL TABLE`: új "külső" táblát hoz létre a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják (az eredeti helyen maradnak).

     > [!NOTE]  
     > Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy MapReduce-feladatot vagy egy Azure-szolgáltatást.
     >
     > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

   * `ROW FORMAT`: az adat formázásának elolvasása. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.

   * `STORED AS TEXTFILE LOCATION`: azt jelzi, hogy a struktúra a példában/adatkönyvtárban tárolja az adattárolási struktúrát, és azt szövegként tárolja.

   * `SELECT`: válassza ki az összes olyan sor számát, amelyben az oszlop `t4` a `[ERROR]`értéket tartalmazza. Ez az utasítás `3` értéket ad vissza, mert három sor tartalmazza ezt az értéket.

   * `INPUT__FILE__NAME LIKE '%.log'` – arra utasítja a kaptárt, hogy csak a. log fájlban végződő fájlokból származó adatok legyenek visszaadva. Ez a záradék korlátozza a keresést a sample. log fájlban, amely tartalmazza az adathalmazt.

6. Az eszköztáron válassza ki a lekérdezéshez használni kívánt **HDInsight-fürtöt** . Válassza a **Submit (Küldés** ) lehetőséget az utasítások struktúra-feladatokként való futtatásához.

   ![Az Azure HDInsight eszköztárának elküldése](./media/apache-hadoop-use-hive-visual-studio/hdinsight-toolbar-submit.png)

7. Megjelenik a **kaptár-feladatok összegzése** , és megjeleníti a futó feladattal kapcsolatos információkat. A **frissítés** hivatkozásra kattintva frissítse a feladatokat, amíg a **feladatok állapota** **Befejezve**nem változik.

   ![befejezett feladatot megjelenítő feladatok összegzése](./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png)

8. A feladatok kimenetének megtekintéséhez használja a **feladatok kimeneti** hivatkozását. Megjeleníti `[ERROR] 3`, amely a lekérdezés által visszaadott érték.

### <a name="additional-example"></a>További példa

Ez a példa az előző lépésben létrehozott `log4jLogs` táblára támaszkodik.

1. A **Server Explorerben**kattintson a jobb gombbal a fürtre, és válassza a **kaptár-lekérdezés írása**lehetőséget.

2. Adja meg a következő kaptár-lekérdezést:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások a következő műveleteket hajtják végre:

    * `CREATE TABLE IF NOT EXISTS`: létrehoz egy táblát, ha még nem létezik. Mivel a `EXTERNAL` kulcsszó nincs használatban, ez az utasítás belső táblát hoz létre. A belső táblákat a rendszer a struktúra adattárházában tárolja, és a struktúra kezeli.
    
    > [!NOTE]  
    > A `EXTERNAL` tábláktól eltérően a belső tábla eldobása a mögöttes adatokat is törli.

    * `STORED AS ORC`: az adatok az optimalizált sorok oszlopos (ORK) formátumban vannak tárolva. Az ork kiválóan optimalizált és hatékony formátum a kaptárak adatok tárolására.
    
    * `INSERT OVERWRITE ... SELECT`: kiválasztja a `[ERROR]`t tartalmazó `log4jLogs` táblázat sorait, majd beszúrja az adatait a `errorLogs` táblába.

3. Futtassa a lekérdezést **Batch** módban.

4. Annak ellenőrzéséhez, hogy a feladatot hozta-e létre, használja a **Server Explorert** , és bontsa ki az **Azure** > **HDInsight** > a HDInsight-fürt > **kaptár-adatbázisok** > **alapértelmezett beállítást**. Megjelenik a **alkalmazásnaplókat** tábla és a **log4jLogs** tábla.

## <a id="nextsteps"></a>Következő lépések

Amint láthatja, a Visual studióhoz készült HDInsight Tools egyszerű módszert biztosít a HDInsight-beli kaptár-lekérdezésekkel való együttműködésre.

Általános információk a HDInsight-struktúrával kapcsolatban:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)

* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)

További információ a Visual studióhoz készült HDInsight-eszközökről:

* [A HDInsight Tools for Visual Studio első lépései](apache-hadoop-visual-studio-tools-get-started.md)