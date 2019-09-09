---
title: Tömeges betöltés Apache Phoenix a psql használatával – Azure HDInsight
description: A psql eszközzel tömeges betöltési adatmennyiségeket tölthet be Apache Phoenix táblákba az Azure HDInsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 15bb65e004de916862297f91278328cddb16487d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810415"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Adatok tömeges betöltése az Apache Phoenixbe a psql használatával

A [Apache Phoenix](https://phoenix.apache.org/) egy [Apache HBase](../hbase/apache-hbase-overview.md)-ra épülő, nyílt forráskódú, nagymértékben párhuzamos, összehasonlítható, integrált adatbázis. A Phoenix SQL-szerű lekérdezéseket biztosít a HBase-en keresztül. A Phoenix a JDBC-illesztőprogramokat használva lehetővé teszi a felhasználók számára az SQL-táblák,-indexek,-nézetek és-folyamatok létrehozását, törlését és módosítását, valamint a upsert-sorok egyenkénti és tömeges megváltoztatását. A Phoenix noSQL natív fordítást használ ahelyett, hogy a MapReduce-t használja a lekérdezések fordításához, hogy alacsony késleltetésű alkalmazásokat hozzon létre a HBase-on. A Phoenix közös processzorokkal támogatja az ügyfél által megadott kód futtatását a-kiszolgáló címterület használatával, amely az adathalmazban található kódot hajtja végre. Ez lekicsinyíti az ügyfél/kiszolgáló adatátvitelt.  Ha az HDInsight-ben Phoenix használatával szeretne dolgozni az adataival, először hozzon létre táblákat, majd töltse be őket.

## <a name="bulk-loading-with-apache-phoenix"></a>Tömeges betöltés Apache Phoenix

Több módon is beszerezhetők az adatok a HBase-be, például az ügyféloldali API-k, a MapReduce-feladatok és a TableOutputFormat használatával, illetve az adatok manuális bevitele a HBase-rendszerhéj használatával. A Phoenix két módszert biztosít a CSV-táblázatok Phoenix `psql`-táblákba való betöltéséhez: egy ügyfél-betöltési eszközt és egy MapReduce-alapú tömeges betöltési eszközt.

Az `psql` eszköz egyszálas, és a legmegfelelőbb a megabájt vagy gigabájt adat betöltéséhez. A betöltendő CSV-fájloknak a ". csv" kiterjesztéssel kell rendelkezniük.  Az SQL- `psql` parancsfájlokat a parancssorban is megadhatja az ". SQL" fájlkiterjesztés használatával.

A tömeges betöltés a MapReduce használatával sokkal nagyobb adatmennyiségekhez használatos, jellemzően éles környezetben, mivel a MapReduce több szálat használ.

Az adattöltés megkezdése előtt ellenőrizze, hogy a Phoenix engedélyezve van-e, és hogy a lekérdezés időtúllépési beállításai a vártak.  Nyissa meg a HDInsight-fürt [Apache Ambari](https://ambari.apache.org/) irányítópultját, válassza a HBase, majd a konfiguráció lapot.  Görgessen lefelé, és ellenőrizze, hogy a `enabled` Apache Phoenix a következőre van-e beállítva:

![Apache Phoenix HDInsight](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Táblázatok `psql` tömeges betöltésére használható

1. Hozzon létre egy új táblát, majd mentse a lekérdezést `createCustomersTable.sql`a filename paranccsal.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Másolja a CSV-fájlt (például a látható tartalmakat `customers.csv` ) `/tmp/` egy könyvtárba az újonnan létrehozott táblázatba való betöltéshez.  `hdfs` A parancs használatával másolja a CSV-fájlt a kívánt forrás helyére.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Hozzon létre egy SQL SELECT lekérdezést a bemeneti adatok megfelelő betöltésének ellenőrzéséhez, majd mentse `listCustomers.sql`a lekérdezést a fájlnévvel. Bármilyen SQL-lekérdezést használhat.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Az *új* Hadoop-parancssorablak megnyitásával tömeges betöltéssel töltheti le az adatmennyiséget. Először váltson a végrehajtási könyvtár helyére `cd` a paranccsal, majd használja az `psql` eszközt (Python `psql.py` -parancs). 

    A következő példa azt várja, hogy a `customers.csv` fájlt egy Storage-fiókból a helyi Temp `hdfs` könyvtárba másolta a fenti 2. lépésben leírtak szerint.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > A `ZookeeperQuorum` név meghatározásához keresse meg a [Apache ZooKeeper](https://zookeeper.apache.org/) kvórum karakterláncot a tulajdonság `/etc/hbase/conf/hbase-site.xml` nevét `hbase.zookeeper.quorum`tartalmazó fájlban.

5. A `psql` művelet befejezését követően a következő üzenet jelenik meg a parancsablakban:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce használata tömeges betöltési táblákhoz

A fürtön keresztül terjesztett nagyobb átviteli sebességű betöltéshez használja a MapReduce Load eszközt. Ez a betöltő először átalakítja az összes adatmennyiséget a HFiles, majd a létrehozott HFiles a HBase.

1. Indítsa el a CSV-MapReduce betöltőjét `hadoop` a (z) paranccsal a Phoenix Client jar használatával:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Hozzon létre egy új táblázatot egy SQL `CreateCustomersTable.sql` -utasítással, az előző 1. lépésben leírtak szerint.

3. A tábla sémájának ellenőrzéséhez futtassa a parancsot `!describe inputTable`.

4. Határozza meg a bemeneti adatok helyének elérési útját, például `customers.csv` például a fájlt. Előfordulhat, hogy a bemeneti fájlok a WASB/ADLS Storage-fiókban találhatók. Ebben a példában a bemeneti fájlok a `<storage account parent>/inputFolderBulkLoad` könyvtárban vannak.

5. Váltson a MapReduce tömeges betöltési parancs végrehajtási könyvtárába:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Keresse meg `ZookeeperQuorum` a `/etc/hbase/conf/hbase-site.xml`értékét a (z) `hbase.zookeeper.quorum`tulajdonság nevével.

7. Állítsa be a osztályútvonal, és futtassa `CsvBulkLoadTool` az eszköz parancsot:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Ha a MapReduce-t a Azure Data Lake Storage használatával szeretné használni, keresse meg a Data Lake Storage `hbase.rootdir` gyökérkönyvtárat `hbase-site.xml`, amely a értéke. A következő parancsban a Data Lake Storage gyökérkönyvtára `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Ebben a parancsban adja meg a bemeneti és kimeneti mappák Data Lake Storage paraméterként:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Javaslatok

* Ugyanazt a tárolóeszközt használja a bemeneti és a kimeneti mappákhoz, vagy az Azure Storage (WASB) vagy a Azure Data Lake Storage (ADL) számára. Az adatok Azure Storage-ból Data Lake Storageba való átviteléhez a `distcp` következő parancsot használhatja:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Használjon nagyobb méretű munkavégző csomópontokat. A MapReduce tömeges másolásának leképezési folyamatai nagy mennyiségű ideiglenes kimenetet hoznak létre, amely kitölti az elérhető nem elosztott fájlrendszerbeli területet. Nagy mennyiségű tömeges betöltés esetén használjon több és nagyobb méretű munkavégző csomópontot. A fürthöz hozzárendelt munkavégző csomópontok száma közvetlenül befolyásolja a feldolgozási sebességet.

* A bemeneti fájlok felosztása ~ 10 GB-os adattömbökbe. A tömeges betöltés egy nagy tárolási igényű művelet, így a bemeneti fájlok több adattömbbe való felosztása jobb teljesítményt eredményez.

* Kerülje a régió-kiszolgálói hozzáférési pontok elérését. Ha a sor kulcsa monoton módon növekszik, a HBase szekvenciális írások a régió-kiszolgáló hotspotting okozhatják. A sor kulcsának *sói* csökkentik a szekvenciális írásokat. A Phoenix lehetővé teszi, hogy a sor kulcsát transzparens módon sózzuk egy adott tábla egy adott táblára vonatkozóan, az alábbi hivatkozással.

## <a name="next-steps"></a>További lépések

* [Tömeges betöltés Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Apache Phoenix használata Linux-alapú Apache HBase-fürtökkel a HDInsight-ben](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Sózott táblák](https://phoenix.apache.org/salted.html)
* [Apache Phoenix nyelvtan](https://phoenix.apache.org/language/index.html)
