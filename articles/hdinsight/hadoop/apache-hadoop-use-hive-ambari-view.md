---
title: Az Ambari nézetek használhatja a Hive hdinsight (Hadoop) - Azure |} Microsoft Docs
description: Útmutató a Hive View webböngészőből elküldeni a Hive-lekérdezéseket. A Hive nézet az Ambari webes felhasználói felületén megadott a Linux-alapú HDInsight-fürt része.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: larryfr
ms.openlocfilehash: 78fee8e3b3e4c0e0c02fa5e1c85bdef58c9cd543
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Ambari Hive nézete használata a hadooppal a Hdinsightban

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Hive-lekérdezéseket Ambari Hive nézet használatával. A Hive nézet lehetővé teszi, optimalizálása, és futtathat Hive-lekérdezéseket a webböngészőben.

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú Hadoop a HDInsight fürt 3.4 vagy újabb verziója.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Webböngésző

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. Nyissa meg az [Azure portált](https://portal.azure.com).

2. A HDInsight-fürthöz, majd válassza ki és **Ambari nézetek** a a **Gyorshivatkozások** szakasz.

    ![a portál Gyorshivatkozások szakasz](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Amikor a rendszer kéri, hitelesítéshez, használja a fürt bejelentkezési (alapértelmezett `admin`) fiók nevét és jelszavát, amely a fürt létrehozásakor megadott.

3. Válassza ki a listáról a nézetek, __Hive View__.

    ![A kiválasztott Hive nézete](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    A Hive-megtekintési lapján az alábbi képen hasonlít:

    ![A lekérdezés munkalap a Hive nézet képe](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Az a __lekérdezés__ lapon, a következő hiveql illessze be a munkalapra:

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

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

   * `DROP TABLE`: Törli a táblázat és az adatfájl, abban az esetben, ha a tábla már létezik.

   * `CREATE EXTERNAL TABLE`: Táblát hoz létre egy új "external" struktúra.
   Külső táblák csak a tábladefiníció Hive tárolja. Az adatok marad az eredeti helyen.

   * `ROW FORMAT`: Az adatok formázását mutatja. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.

   * `STORED AS TEXTFILE LOCATION`: Az adatok tárolására és szövegként tárolt mutatja.

   * `SELECT`: Kiválasztja az adott oszlop t4 értéke [hiba] összes sorok számát.

    > [!IMPORTANT]
    > Hagyja a __adatbázis__ kiválasztottat __alapértelmezett__. Az ebben a dokumentumban a példákban HDInsight tartozó alapértelmezett adatbázishoz.

5. A lekérdezés indításához használja a **Execute** a munkalap gombra. A gomb narancssárga lesz, és a szöveg a következőre változik **leállítása**.

6. A lekérdezés befejeződését követően a **eredmények** lap megjeleníti a művelet eredménye. A következő szöveget a lekérdezés eredménye:

        loglevel       count
        [ERROR]        3

    Használhatja a **naplók** fülre kattintva megtekintheti a naplózási adatok, a feladat hozott létre.

   > [!TIP]
   > Töltse le, vagy a-eredményeket menteni a **-eredményeket menteni** a bal felső legördülő párbeszédpanelén a **lekérdezési folyamat eredményei** szakasz.

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

> [!TIP]
> Az alapértelmezett fürttároló lekérdezések tárolja. A lekérdezések a elérési úton található `/user/<username>/hive/scripts`. Ezek egyszerű szövegként tárolt `.hql` fájlokat.
>
> Ha törölheti a fürtöt, de a tárolás, a segédprogram például használható [Azure Tártallózó](https://azure.microsoft.com/features/storage-explorer/) vagy Data Lake Tártallózó (a a [Azure Portal](https://portal.azure.com)) a lekérdezések beolvasása.

## <a name="user-defined-functions"></a>Felhasználó által definiált függvények

Kiterjesztheti a Hive felhasználói függvény (UDF) keresztül. Egy UDF segítségével HiveQL funkció vagy logika, amely könnyen modellezve nem valósít meg.

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
