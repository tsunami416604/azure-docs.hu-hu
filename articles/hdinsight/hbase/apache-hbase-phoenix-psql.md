---
title: Tömeges betöltési Apache phoenixbe a psql - Azure HDInsight használatával
description: A psql-jének eszközzel betölteni az adatok kötegelt betöltése a Phoenix táblákba.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 8b14550adf89f866cf3b736db049cc671db5b765
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314507"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Adatok kötegelt betöltése Apache phoenixbe a psql használatával

[Az Apache Phoenix](http://phoenix.apache.org/) egy nyílt forráskódú, nagy mértékben párhuzamosított relációs adatbázis, [Apache HBase](../hbase/apache-hbase-overview.md). A Phoenix SQL-szerű lekérdezéseket biztosít a HBase-en. A Phoenix JDBC-illesztőprogramok engedélyezése a felhasználók létrehozása, törlése, és egyenként és tömegesen az SQL táblák, indexek, nézetek és a feladatütemezések és upsert sorok alter használ. Phoenix-lekérdezések összeállításához MapReduce használata helyett natív noSQL-fordítási használja HBase felett közel valós idejű alkalmazások létrehozásához. Phoenix hozzáadja az adatok közös elhelyezése a kód végrehajtása támogatja az ügyfél által megadott kódot futtató kiszolgáló, a címtér a közös processzorral. Így minimálisra csökkenthető az ügyfél-kiszolgáló az adatátvitelt.  Phoenix használata a HDInsight adatokkal dolgozik, először hozzon létre táblák, és ezután az adatok betöltése az őket.

## <a name="bulk-loading-with-apache-phoenix"></a>Tömeges betöltés az Apache Phoenixhez

Többféle módon olvashatja be őket a HBase, beleértve az ügyfél API-k, egy MapReduce-feladatot a TableOutputFormat, vagy manuálisan, a HBase rendszerhéj segítségével az adatok bevitelével. A Phoenix a CSV-adatok betöltése a Phoenix táblákba kétféle módszert biztosít: egy ügyfél nevű eszközt betöltése `psql`, és a egy MapReduce-alapú tömeges betöltési eszközt.

A `psql` eszköz egyszálas és leginkább megabájtban vagy gigabájtban, az adatok betöltésekor. Az összes CSV-fájlok, nem tölthető be kell rendelkeznie a kiterjesztése ".csv".  Az SQL-parancsfájlok is megadhat a `psql` ".sql" kiterjesztésű parancssor.

Tömeges betöltése a MapReduce sokkal nagyobb mennyiségű adat, általában a termelési forgatókönyvekhez használható, a MapReduce használ több szálon.

Adatok betöltése előtt győződjön meg arról, hogy engedélyezve van-e a Phoenix és az, hogy lekérdezés időkorlátja beállításai vannak-e a várt módon.  A HDInsight-fürt eléréséhez [Apache Ambari](https://ambari.apache.org/) irányítópulton, válassza ki a HBase, majd a konfiguráció lapon.  Görgessen lefelé, ellenőrizze, hogy az Apache Phoenix `enabled` látható módon:

![Az Apache Phoenix HDInsight fürtbeállítások](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Használat `psql` tömeges betöltési táblákhoz

1. Hozzon létre egy új táblát, majd mentse a lekérdezést filename `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. A CSV-fájl (például tartalma látható) kopírovat jako `customers.csv` be egy `/tmp/` címtárat betöltése az újonnan létrehozott táblába.  Használja a `hdfs` parancsot a CSV-fájl átmásolása a kívánt forráshelyet.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Hozzon létre egy SQL SELECT lekérdezést, hogy ellenőrizze a bemeneti adatok megfelelően betöltve, majd mentse a lekérdezést filename `listCustomers.sql`. Bármely SQL-lekérdezést is használhatja.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Tömeges nyissa meg az adatokat betölteni egy *új* Hadoop parancssori ablakban. A végrehajtási könyvtár helyét a először módosítsa a `cd` parancsot, és használja a `psql` eszköz (Python `psql.py` parancsot). 

    Az alábbi példa vár, hogy másolja a `customers.csv` fájlt a storage-fiók a helyi átmeneti könyvtárba használatával `hdfs` hasonlóan a fenti 2. lépés.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > Meghatározni a `ZookeeperQuorum` név, keresse meg a [Apache ZooKeeper](https://zookeeper.apache.org/) kvórum karakterlánc fájlban `/etc/hbase/conf/hbase-site.xml` tulajdonságnévvel `hbase.zookeeper.quorum`.

5. Miután a `psql` művelet befejeződött, megjelenik egy üzenet a parancsablakban:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>A MapReduce eszközzel is tömeges betöltési táblák

Nagyobb átviteli sebességet betöltése elosztva a fürtben, a MapReduce betöltési eszközt használni. Ez betöltő HFiles először konvertálja az összes adat, és biztosít majd a létrehozott HFiles HBase.

1. Indítsa el a fürt megosztott kötetei szolgáltatás MapReduce betöltő használatával a `hadoop` a Phoenix ügyfél jar parancsot:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Hozzon létre egy új táblát egy SQL-utasítást a `CreateCustomersTable.sql` az előző 1. lépés.

3. A tábla sémája ellenőrzéséhez futtassa `!describe inputTable`.

4. Határozza meg a bemeneti adatokat, például a hely elérési útja `customers.csv` fájlt. A bemeneti fájlokhoz lehet a WASB vagy ADLS-tárfiókot. Ebben a példában a forgatókönyvben a bemeneti fájlok vannak a `<storage account parent>/inputFolderBulkLoad` könyvtár.

5. Módosítsa a MapReduce tömeges betöltési parancs végrehajtási könyvtárba:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Keresse meg a `ZookeeperQuorum` értékét `/etc/hbase/conf/hbase-site.xml`, tulajdonságnévvel `hbase.zookeeper.quorum`.

7. Állítsa be a osztályútvonal, és futtassa a `CsvBulkLoadTool` parancssori eszközzel:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. A MapReduce-val való használatához ADLS keresse meg az ADLS gyökérkönyvtárban, amely a `hbase.rootdir` értékét `hbase-site.xml`. Az alábbi parancsban az ADLS gyökérkönyvtárában van `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Ebben a parancsban adja meg az ADLS bemeneti és kimeneti paraméterként mappák:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Javaslatok

* A bejövő és kimenő mappákban WASB vagy az ADLS az azonos adattárolóra használja. Adatok átvitele WASB ADLS, használhatja a `distcp` parancsot:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Nagyobb méretű munkavégző csomópontok használhatók. A térkép folyamatok, a MapReduce tömeges másolás ideiglenes kimeneti nagy mennyiségű töltse fel a rendelkezésre álló, nem Elosztott helyet hozhat létre. A tömeges betöltés nagy mennyiségű több és nagyobb méretű feldolgozói-csomópontokat használjanak. Közvetlenül lefoglalni a fürt munkavégző csomópontok száma a feldolgozási sebesség hatással van.

* Bemeneti fájlok felosztása ~ 10 GB-os adattömböket. A tömeges betöltés egy storage-igényes művelet, így a bemeneti fájlok bontani jobb teljesítményt eredményez több adattömböket.

* Régió kiszolgáló kritikus pontok elkerülése. Ha a sorkulcs monoton növekvő, HBase szekvenciális írási műveletek régió kiszolgáló hotspotting idéznek elő. *Sózás* a sorkulcs csökkenti a szekvenciális írási műveleteket. A Phoenix lehetővé teszi a sorkulcs egy adott tábla, mint az alább hivatkozott sózás byte a transzparens módon sója.

## <a name="next-steps"></a>További lépések

* [Adatok kötegelt betöltése az Apache Phoenixhez](http://phoenix.apache.org/bulk_dataload.html)
* [Az Apache HBase Linux-alapú Apache Phoenix használata a HDInsight-fürtök](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Sózott táblák](https://phoenix.apache.org/salted.html)
* [A Phoenix-szintaxis](http://phoenix.apache.org/language/index.html)
