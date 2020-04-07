---
title: Az Apache Ambari Hive view használata az Apache Hadoop segítségével az Azure HDInsightban
description: Ismerje meg, hogyan használhatja a Hive nézetet a webböngészőből a Hive-lekérdezések küldéséhez. A Hive-nézet a Linux-alapú HDInsight-fürthöz mellékelt Ambari web felhasználói felület része.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: 787d88d336abcf3b0ba9b14c3d3798850b665eca
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745093"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Apache Ambari Hive-nézet használata Apache Hadooppal a HDInsightban

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan futtathatja a Hive-lekérdezéseket az Apache Ambari Hive View használatával. A Hive nézet lehetővé teszi a hive-lekérdezések létrehozását, optimalizálását és futtatását a webböngészőből.

## <a name="prerequisites"></a>Előfeltételek

Egy Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a fürtöt.  Az utasításokat a [Fürtök listája és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) című témakörben találja. A fürt új portálnézetben nyílik meg.

1. A **Fürtirányítópultok területen**válassza az **Ambari nézetek lehetőséget.** Amikor hitelesítésre van szüksége, használja a `admin`fürt bejelentkezési nevét (alapértelmezett) fióknevét és jelszavát, amelyet a fürt létrehozásakor adott meg. A böngészőben `https://CLUSTERNAME.azurehdinsight.net/#/main/views` arra is `CLUSTERNAME` navigálhat, hogy hol van a fürt neve.

1. A nézetek listájában válassza a __Hive nézet lehetőséget.__

    ![Az Apache Ambari válassza az Apache Hive nézetet](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    A Hive nézet lapja hasonló az alábbi képhez:

    ![A Hive nézet lekérdezési munkalapjának képe](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. A __Lekérdezés__ lapon illessze be a következő HiveQL-állításokat a munkalapra:

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

    Ezek a kijelentések a következő műveleteket követik:

    |Nyilatkozat | Leírás |
    |---|---|
    |TÁBLÁZAT LEDOBÁSA|Törli a táblát és az adatfájlt, ha a tábla már létezik.|
    |KÜLSŐ TÁBLA LÉTREHOZÁSA|Új "külső" táblát hoz létre a Hive-ban. A külső táblák csak a tábladefiníciót tárolják a Hive-ban. Az adatok az eredeti helyen maradnak.|
    |SOR FORMÁTUMA|Megmutatja, hogyan vannak formázva az adatok. Ebben az esetben az egyes naplók mezőit szóköz választja el egymástól.|
    |TÁROLT SZÖVEGFÁJL HELYE|Megmutatja, hogy az adatok hol tárolódnak, és hogy szövegként tárolódnak.Shows where the data is stored, and that it's stored as text.|
    |SELECT|Kijelöli az összes olyan sor számát, ahol a t4 oszlop a [HIBA] értéket tartalmazza.|

   > [!IMPORTANT]  
   > Hagyja az __Adatbázis__ beállítást __alapértelmezetten.__ A jelen dokumentumban szereplő példák a HDInsight alapértelmezett adatbázisát használják.

1. A lekérdezés elindításához válassza a Munkalap alatti **Végrehajtás** lehetőséget. A gomb narancssárgára vált, és a szöveg **Leállítás**lesz.

1. A lekérdezés befejezése után az **Eredmények** lap megjeleníti a művelet eredményeit. A következő szöveg a lekérdezés eredménye:

        loglevel       count
        [ERROR]        3

    A **NAPLÓ** lapon megtekintheti a feladat által létrehozott naplózási információkat.

   > [!TIP]  
   > Eredmények letöltése vagy mentése az **Eredmények** lap **Műveletek** legördülő párbeszédpanelén.

### <a name="visual-explain"></a>Vizuális magyarázat

A lekérdezési terv vizualizációjának megjelenítéséhez kattintson a Munkalap alatti **Vizuális magyarázat** fülre.

A lekérdezés **Vizuális magyarázat** nézete hasznos lehet az összetett lekérdezések áramlásának megértésében.

### <a name="tez-ui"></a>Tez felhasználói felület

A lekérdezés Tez felhasználói felületének megjelenítéséhez kattintson a munkalap alatti **Tez felhasználói felület** fülre.

> [!IMPORTANT]  
> A Tez nem használható az összes lekérdezés feloldására. Számos lekérdezést feloldhat a Tez használata nélkül.

## <a name="view-job-history"></a>Feladatelőzmények megtekintése

A __Feladatok__ lap a Hive-lekérdezések előzményeit jeleníti meg.

![Apache Hive nézet feladatok lap előzményei](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Adatbázistáblák

A __Táblák__ lapon a Hive-adatbázistábláival dolgozhat.

![Az Apache Hive-táblák lap képe](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Mentett lekérdezések

A **Lekérdezés** lapon tetszés szerint mentheti a lekérdezéseket. A lekérdezés mentése után a __Mentett lekérdezések__ lapon újra felhasználhatja azt.

![Az Apache Hive nézetek mentett lekérdezések lapja](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> A mentett lekérdezések az alapértelmezett fürttárolóban tárolódnak. A mentett lekérdezéseket az `/user/<username>/hive/scripts`elérési út alatt találhatja meg. Ezek egyszerű szöveges `.hql` fájlként tárolódnak.
>
> Ha törli a fürtöt, de megtartja a tárhelyet, használhatja a segédprogramot, például [az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) vagy a Data Lake Storage Explorer (az Azure [Portalról)](https://portal.azure.com)a lekérdezések lekéréséhez.

## <a name="user-defined-functions"></a>Felhasználó által meghatározott függvények

A Hive kiterjeszthető a felhasználó által definiált függvények (UDF) keresztül. UDF használatával olyan funkciókat vagy logikát valósíthat meg, amelyek nem könnyen modellezhetők a HiveQL-ben.

Deklarálja és mentse az UDF-ek készletét a Hive nézet tetején található **UDF** lapon. Ezek az UDF-ek a **Lekérdezésszerkesztővel**használhatók.

![Az Apache Hive nézet UDF-lapja](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

A **Lekérdezésszerkesztő**alján megjelenik egy **Udfs** beszúrási gomb. Ez a bejegyzés a Hive nézetben definiált UDF-ek legördülő listáját jeleníti meg. UdF kiválasztása hozzáadja a HiveQL-utasítások a lekérdezéshez, hogy az UDF.

Ha például a következő tulajdonságokkal rendelkező UDF-et definiált:

* Erőforrás neve: myudfs

* Erőforrás elérési útja: /myudfs.jar

* UDF név: myawesomeudf

* UDF osztály neve: com.myudfs.Awesome

Az **Udfs beszúrása** gombbal megjelenik egy **myudfs**nevű bejegyzés, amely az adott erőforráshoz definiált minden egyes UDF-hez egy másik legördülő listát jelenít meg. Ebben az esetben, ez **myawesomeudf**. A bejegyzés bejelölésével a következő a lekérdezés elejére:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Ezután használhatja az UDF-et a lekérdezésben. Például: `SELECT myawesomeudf(name) FROM people;`.

Az UDF-ek HIVe-vel hdinsight-on történő használatáról az alábbi cikkekben talál további információt:

* [Python használata Apache Hive-val és Apache Pig-szel a HDInsightban](python-udf-hdinsight.md)
* [Java UDF használata Apache Hive-val a HDInsightban](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Hive-beállítások

Módosíthatja a különböző Hive-beállításokat, például a Hive végrehajtási motorjának módosítása Tez-ről (az alapértelmezett) a MapReduce-re.

## <a name="next-steps"></a>További lépések

Általános információk a HIVE a HDInsight:

* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)

A Hadoop hdinsight-on való egyéb működéséről további információt talál:

* [Az Apache Pig használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-pig.md)
* [A MapReduce használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-mapreduce.md)
