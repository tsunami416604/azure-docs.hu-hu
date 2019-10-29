---
title: Az Apache Ambari kaptár nézet használata az Azure HDInsight Apache Hadoop
description: Megtudhatja, hogyan használhatja a kaptár nézetet a webböngészőjéből a kaptár-lekérdezések elküldéséhez. A kaptár nézet a Linux-alapú HDInsight-fürthöz elérhető Ambari webes felhasználói felület részét képezi.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 5063be247b2ad51dc8888f8512f523ccf2b0174c
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044812"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Az Apache Ambari kaptár nézet használata a HDInsight Apache Hadoop

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat kaptár-lekérdezéseket az Apache Ambari kaptár nézet használatával. A kaptár nézet lehetővé teszi kaptár-lekérdezések létrehozását, optimalizálását és futtatását a böngészőben.

## <a name="prerequisites"></a>Előfeltételek

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).
* Egy webböngésző

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. A [Azure Portal](https://portal.azure.com/)válassza ki a fürtöt.  Útmutatásért lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) . A fürt megnyílik egy új portálon.

2. A **fürt irányítópultok**területen válassza a **Ambari nézetek**elemet. Amikor a rendszer felszólítja a hitelesítésre, használja a fürt létrehozásakor megadott bejelentkezési (alapértelmezett `admin`) fióknevet és jelszót.

3. A nézetek listájában válassza a __struktúra nézet__lehetőséget.

    ![Apache Ambari – Apache Hive nézet kiválasztása](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    A kaptár nézet oldal a következő képhez hasonló:

    ![A struktúra nézet lekérdezési munkalapjának képe](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

4. A __lekérdezés__ lapon illessze be az alábbi HiveQL-utasításokat a munkalapra:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Ezek az utasítások a következő műveleteket hajtják végre:

   * `DROP TABLE`: törli a táblát és az adatfájlt arra az esetre, ha a tábla már létezik.

   * `CREATE EXTERNAL TABLE`: új "külső" táblát hoz létre a kaptárban.
     A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.

   * `ROW FORMAT`: az adatformátumok megjelenítése. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.

   * `STORED AS TEXTFILE LOCATION`: megjeleníti az adattárolás helyét, valamint azt, hogy a szövegként van tárolva.

   * `SELECT`: kiválasztja az összes olyan sor számát, ahol a T4 oszlop tartalmazza a [ERROR] értéket.

   > [!IMPORTANT]  
   > Hagyja meg az __adatbázis__ __alapértelmezett__beállítását. A jelen dokumentumban szereplő példák a HDInsight alapértelmezett adatbázisát használják.

5. A lekérdezés elindításához válassza a **végrehajtás** a munkalap alatt lehetőséget. A gomb a narancssárga színűre változik, és a szöveg **leáll**.

6. A lekérdezés befejeződése után az **eredmények** lap a művelet eredményét jeleníti meg. A lekérdezés eredménye a következő szöveg:

        loglevel       count
        [ERROR]        3

    A **napló** lapon megtekintheti a feladatok által létrehozott naplózási adatokat.

   > [!TIP]  
   > Töltse le vagy mentse az eredményeket az **eredmények** lap **műveletek** legördülő párbeszédpanelén.

### <a name="visual-explain"></a>Vizualizáció magyarázata

A lekérdezési terv vizualizációjának megjelenítéséhez válassza a munkalap alatti **vizuális magyarázat** lapot.

A lekérdezés **vizuális magyarázatot** ábrázoló nézete hasznos lehet az összetett lekérdezések folyamatának megismeréséhez.

### <a name="tez-ui"></a>Tez felhasználói felület

A lekérdezés TEZ felhasználói felületének megjelenítéséhez válassza a **TEZ felhasználói felület** fület a munkalap alatt.

> [!IMPORTANT]  
> A TEZ nem az összes lekérdezés feloldására szolgál. A TEZ használata nélkül is megoldhat sok lekérdezést.

## <a name="view-job-history"></a>Feladatelőzmények megtekintése

A __feladatok__ lap a struktúra-lekérdezések előzményeit jeleníti meg.

![Apache Hive a feladatok lap előzményeinek megtekintése](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Adatbázistáblák

A __Tables (táblák__ ) lapon egy struktúra-adatbázisban lévő táblákkal dolgozhat.

![A Apache Hive Tables lap képe](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Mentett lekérdezések

A **lekérdezés** lapon igény szerint mentheti a lekérdezéseket. A lekérdezés mentése után újra felhasználhatja azt a __mentett lekérdezések__ lapról.

![Apache Hive mentett lekérdezések lap megtekintése](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> A mentett lekérdezések az alapértelmezett fürtöt tárolóban tárolódnak. A mentett lekérdezéseket az elérési út `/user/<username>/hive/scripts`alatt találja. Ezeket egyszerű szöveges `.hql` fájlokként tárolja a rendszer.
>
> Ha törli a fürtöt, de megtartja a tárolót, használhat olyan segédprogramot, mint a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) vagy a Data Lake Storage Explorer (az [Azure Portalról](https://portal.azure.com)) a lekérdezések lekéréséhez.

## <a name="user-defined-functions"></a>Felhasználó által meghatározott függvények

A struktúrát a felhasználó által definiált függvények (UDF) használatával is kiterjesztheti. Az UDF használatával olyan funkciót vagy logikát alkalmazhat, amely nem könnyen modellezhető a HiveQL-ben.

Deklaráljon és mentsen egy UDF a kaptár nézet tetején található **UDF** lapon. Ezek a UDF a **lekérdezés-szerkesztővel**is használhatók.

![Apache Hive megtekintés UDF lap megjelenítése](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Miután hozzáadta az UDF-t a kaptár nézethez, megjelenik egy **beszúrási UDF** gomb a **lekérdezési szerkesztő**alján. Ennek a bejegyzésnek a kiválasztásával megjelenítheti a kaptár nézetben meghatározott UDF legördülő listáját. Az UDF kiválasztásával HiveQL-utasítások adhatók hozzá a lekérdezéshez az UDF engedélyezéséhez.

Ha például meghatározta az UDF-t a következő tulajdonságokkal:

* Erőforrás neve: myudfs

* Erőforrás elérési útja:/myudfs.jar

* UDF-név: myawesomeudf

* UDF-osztálynév: com. myudfs. Awesome

A **UDF beszúrása** gomb a **myudfs**nevű bejegyzést jeleníti meg, és az adott erőforráshoz definiált összes UDF számára egy másik legördülő listát. Ebben az esetben ez a **myawesomeudf**. A bejegyzés kiválasztásával hozzáadja a következőt a lekérdezés elejéhez:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Ezután használhatja az UDF-t a lekérdezésben. Például: `SELECT myawesomeudf(name) FROM people;`.

A UDF és a HDInsight struktúrával való használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [A Python használata Apache Hive és Apache Pig használatával a HDInsight-ben](python-udf-hdinsight.md)
* [Egyéni Apache Hive UDF hozzáadása a HDInsight-hez](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Struktúra beállításai

Módosíthatja a kaptárak különböző beállításait, például megváltoztathatja a kaptár végrehajtó motorját a TEZ (az alapértelmezett) értékről a MapReduce.

## <a id="nextsteps"></a>Következő lépések

A HDInsight struktúra általános információi:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

További információ a HDInsight-beli Hadoop használható egyéb módszerekről:

* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)
