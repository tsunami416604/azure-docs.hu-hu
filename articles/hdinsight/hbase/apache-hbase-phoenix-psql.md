---
title: "Apache Phoenix psql - Azure HDInsight segítségével vezérlőbe való betöltés tömeges |} Microsoft Docs"
description: "Tömeges betöltési adatok betöltése az Phoenix táblák a psql eszközzel."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 0a623113ee9f3fe2c0f5f616ecd79b8311a8ffc1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>Tömeges betöltési adatok Phoenix psql használatával

[Apache Phoenix](http://phoenix.apache.org/) egy nyílt forráskódú, épülő nagymértékben párhuzamos relációs adatbázis [HBase](../hbase/apache-hbase-overview.md). Phoenix SQL-szerű lekérdezéseket biztosít a HBase-en. Phoenix felhasználók létrehozása, törlése, és módosítsa az SQL táblák, indexek, nézetek és sorozatok és upsert sorok egyesével és csoportosan JDBC illesztőprogramokat használja. Phoenix fordítása a lekérdezések MapReduce használata helyett natív noSQL-fordítási segítségével hozza létre a kis késleltetésű alkalmazások a HBase felett. Phoenix hozzáadja az adatok közös elhelyezése a kód végrehajtása társprocesszorok történő ügyfél által megadott kód futtatásához a kiszolgáló, a címterületen belülre. Ez minimalizálja az ügyfél-kiszolgáló az adatátvitelt.  Phoenix használata a Hdinsightban adatokkal dolgozni, először hozzon létre táblák, és majd az adatok betöltése az őket.

## <a name="bulk-loading-with-phoenix"></a>A Phoenix betöltése tömeges

Az adatok lekérése a HBase, beleértve az ügyfél API-k, a MapReduce feladatot TableOutputFormat, vagy az adatok a HBase rendszerhéj segítségével manuálisan beírása több módja van. Phoenix rendszerben a CSV-adatok Phoenix táblába töltéséhez két módszert kínál: nevű eszköz betöltése ügyfél `psql`, és a MapReduce-alapú tömeges betöltési eszköz.

A `psql` eszköz egyszálas és leginkább megabájtban vagy gigabájtban az adatok betöltésekor. Minden CSV-fájl betöltését a ".csv" fájlnévkiterjesztéssel kell rendelkeznie.  Azt is megadhatja az SQL-parancsprogramok a `psql` ".sql" kiterjesztésű parancssor.

A MapReduce betöltése tömeges használatos sokkal nagyobb adatkötetnél, általában a termelési környezetben MapReduce több szál használja.

Adatok betöltése előtt győződjön meg arról, hogy engedélyezve van-e a Phoenix és, hogy lekérdezés időtúllépési beállítások legyenek-e a várt módon.  A HDInsight fürt Ambari irányítópult eléréséhez, válassza ki a HBase, és a konfiguráció lapon.  Görgessen le, hogy ellenőrizze, hogy Apache Phoenix `enabled` látható módon:

![Apache Phoenix HDInsight-fürt beállításai](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Használjon `psql` tömeges betöltési táblákhoz

1. Új tábla létrehozása, majd mentse a lekérdezést Fájlnév `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. A CSV-fájl (például tartalma látható) másolási `customers.csv` be egy `/tmp/` betöltését az újonnan létrehozott táblába a könyvtár.  Használja a `hdfs` helyre való másolásához a CSV-fájlt a kívánt forrás parancs.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Ellenőrizze a bemeneti adatok megfelelően betölteni az SQL SELECT lekérdezés létrehozása, majd mentse a lekérdezést Fájlnév `listCustomers.sql`. SQL-lekérdezést is használhatja.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Tömeges nyissa meg az adatok betöltésére egy *új* Hadoop parancsablakot. A végrehajtási directory helyre előbb módosítsa a `cd` parancsot, és használja a `psql` eszköz (Python `psql.py` parancs). 

    A következő példa a másolt vár a `customers.csv` tárfiókból fájlt a helyi ideiglenes könyvtárba használatával `hdfs` beállítani, mint a fenti 2. lépés.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Meghatározásához a `ZookeeperQuorum` nevét, a fájlban keresse meg a zookeeper kvórum karakterlánc `/etc/hbase/conf/hbase-site.xml` tulajdonságnévvel `hbase.zookeeper.quorum`.

5. Miután a `psql` művelet befejeződött, megjelenik egy üzenet a parancsablakba:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Használhatja a MapReduce-tömeges betöltési táblákhoz

Nagyobb átviteli sebesség betöltése a fürt elosztva, az eszközzel a MapReduce betöltése. A betöltési először az összes adat alakítja HFiles, majd a létrehozott HFiles a HBase.

1. Indítsa el a fürt megosztott kötetei szolgáltatás MapReduce betöltő használatával a `hadoop` a Phoenix ügyfél jar parancsot:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Új tábla létrehozása az SQL-utasítás a `CreateCustomersTable.sql` az előző lépésben 1.

3. Ellenőrizze a sémát a tábla, futtassa a `!describe inputTable`.

4. Határozza meg a bemeneti adatok, például a példa a hely elérési útja `customers.csv` fájlt. A bemeneti fájlok WASB/ADLS-tárfiókba lehet. Az ebben a példaforgatókönyvben a bemeneti fájlok vannak a `<storage account parent>/inputFolderBulkLoad` könyvtár.

5. Módosítsa a MapReduce tömeges betöltési utasítás végrehajtása könyvtára:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Keresse meg a `ZookeeperQuorum` értéket `/etc/hbase/conf/hbase-site.xml`, tulajdonságnévvel `hbase.zookeeper.quorum`.

7. Állítsa be a classpath, és futtassa a `CsvBulkLoadTool` parancs eszköz:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. MapReduce használata ADLS, keresse meg a ADLS gyökérkönyvtár, amely a `hbase.rootdir` értéket `hbase-site.xml`. Az alábbi parancs a ADLS gyökérkönyvtár van `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Ebben a parancsban adja meg a ADLS bemeneti és kimeneti paraméterként mappák:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Javaslatok

* Használja az ugyanazon adattárolóra bemeneti és a kimeneti mappa WASB vagy ADLS. Adatok átvitele WASB ADLS, használhatja a `distcp` parancs:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Használjon nagyobb méretű munkavégző csomópontokhoz. A térkép folyamatokat a MapReduce tömeges másolás ideiglenes kimeneti nagy mennyiségű feltöltve a rendelkezésre álló nem DFS-terület eredményez. A tömeges betöltés nagy mennyiségű több és nagyobb méretű munkavégző csomópontokhoz használja. Közvetlenül lefoglalni a fürt feldolgozó csomópontjainak számát hatással van a feldolgozási sebességet.

* A bemeneti fájlok felosztása ~ 10 GB-os adattömböket. A tömeges betöltés egy tároló-igényes művelet, így a bemeneti fájlok bontani jobb teljesítményt eredményez több adattömböket.

* Kerülje a régió kiszolgáló csatlakozási pontokhoz. Ha a sorkulcs monoton módon növekvő, HBase szekvenciális írási műveletek régió server hotspotting elő. *Sózás* a sorkulcs csökkenti a szekvenciális írási műveleteket. A Phoenix a sorkulcs egy adott tábla alább hivatkozott sózás bájt a transzparens módon sója lehetőséget biztosít.

## <a name="next-steps"></a>További lépések

* [Tömeges adatok betöltése az Apache Phoenix](http://phoenix.apache.org/bulk_dataload.html)
* [Hdinsight Linux-alapú HBase-fürtökkel Apache Phoenix használata](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Sózott táblák](https://phoenix.apache.org/salted.html)
* [Phoenix nyelvtan](http://phoenix.apache.org/language/index.html)
