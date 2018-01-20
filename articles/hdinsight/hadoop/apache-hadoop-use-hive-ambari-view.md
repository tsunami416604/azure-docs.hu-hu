---
title: "Az Ambari nézetek használhatja a Hive hdinsight (Hadoop) - Azure |} Microsoft Docs"
description: "Útmutató a Hive View webböngészőből elküldeni a Hive-lekérdezéseket. A Hive nézet az Ambari webes felhasználói felületén megadott a Linux-alapú HDInsight-fürt része."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 5f66e60249af489e695029cbb072f3cc881bb039
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Ambari Hive nézete használata a hadooppal a Hdinsightban

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Hive-lekérdezéseket Ambari Hive nézet használatával. Ambari egy felügyeleti és figyelési segédprogram Linux-alapú HDInsight-fürtökkel. Az Ambari keresztül elérhető szolgáltatások egyik webes felhasználói Felületet, amely segítségével futtathat Hive-lekérdezéseket.

> [!NOTE]
> Ambari rendelkezik számos lényeges képességét, hogy ez a dokumentum nem ismerteti. További információkért lásd: [kezelése HDInsight-fürtök az Ambari webes felhasználói felület használatával](../hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú HDInsight-fürtöt. Fürtök létrehozásával kapcsolatos további információkért lásd: [Hadoop használatának megkezdésében a HDInsight](apache-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések az Azure HDInsight-fürt által használt Linux igényelnek. Linux az egyetlen operációs rendszer használt a HDInsight 3.4 vagy újabb verziója. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="open-the-hive-view"></a>Nyissa meg a Hive nézete

Az Ambari nézetek megnyithatja az Azure-portálról. A HDInsight-fürthöz, majd válassza ki és **Ambari nézetek** a a **Gyorshivatkozások** szakasz.

![a portál Gyorshivatkozások szakasz](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

Válassza ki a listáról a nézetek, __Hive View__.

![A kiválasztott Hive nézete](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> Ambari próbál hozzáférni, amikor a program kéri, a helyrendszer felé történő hitelesítésre. Adja meg a rendszergazdai (alapértelmezett `admin`) nevét és jelszavát, amely a fürt létrehozásakor használt fiókot.

Az alábbi képen hasonló lap kell megjelennie:

![A lekérdezés munkalap a Hive nézet képe](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="run-a-query"></a>A lekérdezés futtatása

Hive-lekérdezések futtatásához használja a következő lépéseket a Hive nézetből.

1. Az a __lekérdezés__ lapon, a következő hiveql illessze be a munkalapra:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * `DROP TABLE`: Törli a táblázat és az adatfájl, abban az esetben, ha a tábla már létezik.

   * `CREATE EXTERNAL TABLE`: Táblát hoz létre egy új "external" struktúra.
   Külső táblák csak a tábladefiníció Hive tárolja. Az adatok marad az eredeti helyen.

   * `ROW FORMAT`: Az adatok formázását mutatja. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.

   * `STORED AS TEXTFILE LOCATION`: Az adatok tárolására és szövegként tárolt mutatja.

   * `SELECT`: Kiválasztja az adott oszlop t4 értéke [hiba] összes sorok számát.

     > [!NOTE]
     > Külső tábla használja, ha egy külső forrásból, frissítenie kell az alapul szolgáló adatokat várt például egy automatizált adatok feltöltése a folyamat vagy egy másik MapReduce művelet. A külső tábla eldobása does *nem* törli az adatokat, csak a tábla definíciójában.

    > [!IMPORTANT]
    > Hagyja a __adatbázis__ kiválasztottat __alapértelmezett__. Az ebben a dokumentumban a példákban HDInsight tartozó alapértelmezett adatbázishoz.

2. A lekérdezés indításához használja a **Execute** a munkalap gombra. A gomb narancssárga lesz, és a szöveg a következőre változik **leállítása**.

3. A lekérdezés befejeződését követően a **eredmények** lap megjeleníti a művelet eredménye. A következő szöveget a lekérdezés eredménye:

        sev       cnt
        [ERROR]   3

    Használhatja a **naplók** fülre kattintva megtekintheti a naplózási adatok, a feladat hozott létre.

   > [!TIP]
   > Töltse le, vagy a-eredményeket menteni a **-eredményeket menteni** a bal felső legördülő párbeszédpanelén a **lekérdezési folyamat eredményei** szakasz.

4. Ez a lekérdezés első négy sornyi, majd válassza ki és **Execute**. Figyelje meg, hogy nincsenek eredmény a feladat befejezése után. Használja a **Execute** gomb, amikor a lekérdezés van kijelölve, csak a kijelölt utasításokat futtat. Ebben az esetben a kijelölés nem tartozik a végső utasítás, kiolvassa a sorokat a táblában. Ha csak a sor választja, és használjon **Execute**, láthatja, hogy a kívánt eredmény elérése érdekében.

5. A munkalap hozzáadásához használja a **új munkalapra lesznek** gomb alján a **Lekérdezésszerkesztő**. Az új munkalapra adja meg a következő hiveql:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * **Hozzon létre Ha nem létezik táblázat**: egy táblát hoz létre, ha egy nem létezik. Mivel a **külső** kulcsszó nem használható, egy belső tábla jön létre. Egy belső tábla a Hive-adatraktárban tárolja, és teljesen kezeli a struktúra. Eltérően külső táblák egy belső tábla eldobása törli az alapul szolgáló adatokat.

   * **TÁROLT AS ORC**: az adatok optimalizált sor oszlopos (ORC) formátumban tárolja. ORC formátuma egy magas optimalizált és hatékony Hive adatainak tárolásához.

   * **ÍRJA FELÜL AZ INSERT... Válassza ki**: sorát kiválasztja a **log4jLogs** tartalmazó `[ERROR]`, majd beilleszti az adatokat a **errorLogs** tábla.

Használja a **Execute** gombra a lekérdezés futtatására. A **eredmények** lap tartalmaz adatokat, ha a lekérdezés nulla sort adja vissza. A állapota kell **sikeres** a lekérdezés befejeződése után.

### <a name="visual-explain"></a>Visual ismertetik.

A lekérdezésterv a képi megjelenítés megjelenítéséhez jelölje ki a **Visual ismertetik** alatt a munkalap fülre.

A **Visual ismertetik** lehet, hogy a nézet a lekérdezés a folyamatot, az összetett lekérdezések hasznos információkat. Megjelenik a szöveges megfelelőjét e nézet használatával a **magyarázat** gombra a lekérdezés-szerkesztő.

### <a name="tez-ui"></a>Tez felhasználói felület

A lekérdezés a Tez felhasználói felület megjelenítéséhez jelölje ki a **Tez** alatt a munkalap fülre.

> [!IMPORTANT]
> Tez nem használt összes lekérdezéseket. Több lekérdezést oldhatja Tez használata nélkül. 

Ha a Tez a lekérdezés feloldásához használt, az irányított aciklikus diagramhoz (DAG) jelenik meg. Ha meg szeretné tekinteni a lekérdezések futtatását a múltban DAG, vagy ha azt szeretné, a debug a Tez folyamat használja a [Tez nézet](../hdinsight-debug-ambari-tez-view.md) helyette.

## <a name="view-job-history"></a>Feladatelőzmények megtekintése

A __feladatok__ lapon Hive-lekérdezések előzményeit jeleníti meg.

![A feladatelőzmények képe](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Adatbázistáblák

Használhatja a __táblák__ lapon Hive adatbázisban lévő táblák együttműködni.

![A táblák lap képe](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Mentett lekérdezések

Az a **lekérdezés** lapon lekérdezések mentése nem kötelező. A lekérdezés mentése után újra felhasználhatja azt a __mentett lekérdezések__ fülre.

![Lekérdezések lap képe](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

## <a name="user-defined-functions"></a>Felhasználó által definiált függvények

Hive felhasználói függvény (UDF) keresztül is kiterjeszthető. Egy UDF segítségével HiveQL funkció vagy logika, amely könnyen modellezve nem valósít meg.

Deklarálja, és mentse egy felhasználó által megadott függvények készletét a **UDF** lapon a Hive View tetején. A felhasználó által megadott függvények használhatók a **Lekérdezésszerkesztő**.

![Az UDF lap képe](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

A Hive nézetet egy UDF hozzáadása után egy **helyezze be a felhasználó által megadott függvények** gomb alján megjelenik a **Lekérdezésszerkesztő**. Látható értesítések valamelyikének kiválasztásakor Ez a bejegyzés a Hive nézetben megadott felhasználó által megadott függvények legördülő listája. Az UDF-ben ahhoz, hogy a lekérdezés egy UDF kiválasztása HiveQL utasítás hozzá.

Például, ha meghatározta a UDF-ben a következő tulajdonságokkal:

* Az erőforrásnév: myudfs

* Erőforrás elérési útja: /myudfs.jar

* Az UDF-name: myawesomeudf

* Az UDF osztálynév: com.myudfs.Awesome

Használatával a **helyezze be a felhasználó által megadott függvények** gomb megjelenik egy bejegyzés nevű **myudfs**, egy másik legördülő listával minden UDF-ben az adott erőforrás definiálva. Ebben az esetben **myawesomeudf**. A lekérdezés elejére válassza ezt a bejegyzést ad a következő:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Az UDF használhatja a lekérdezésben. Például: `SELECT myawesomeudf(name) FROM people;`.

A felhasználó által megadott függvények használata a HDInsight Hive további információkért tekintse meg a következő cikkeket:

* [Python használata a Hive és a Pig a Hdinsightban](python-udf-hdinsight.md)
* [HDInsight egy egyéni Hive UDF felvétele](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive-beállítások

Különböző Hive-beállítások, például a-végrehajtó motor a Hive Tez (alapértelmezett) a MapReduce történő módosítása módosíthatja.

## <a id="nextsteps"></a>Következő lépések

A HDInsight Hive általános tájékoztatást:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Az egyéb lehetőségeiről a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
