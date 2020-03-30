---
title: Tömeges betöltés az Apache Phoenixbe a psql használatával - Azure HDInsight
description: A psql eszközzel tömeges betöltési adatokat tölthet be az Apache Phoenix-táblákba az Azure HDInsightban
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552610"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Adatok tömeges betöltése az Apache Phoenixbe a psql használatával

[Apache Phoenix](https://phoenix.apache.org/) egy nyílt forráskódú, masszívan párhuzamos relációs adatbázis épül [Apache HBase](../hbase/apache-hbase-overview.md). A Phoenix SQL-szerű lekérdezéseket biztosít a HBase-en keresztül. A Phoenix JDBC-illesztőprogramokkal teszi lehetővé a felhasználók számára az SQL-táblák, indexek, nézetek és szekvenciák, valamint a upsert sorok külön-külön és ömlesztve i. A Phoenix a MapReduce használata helyett noSQL natív összeállítást használ a lekérdezések fordításához, alacsony késleltetésű alkalmazások létrehozásához a HBase-en felül. A Phoenix társprocesszorokat ad hozzá az ügyfél által biztosított kód futtatásához a kiszolgáló címterében, és az adatokkal közösen elhelyezett kódot futtatja. Ez minimálisra csökkenti az ügyfél/kiszolgáló adatátvitelét.  Ha a HdInsight ban a Phoenix használatával szeretne adatokat használni, először hozzon létre táblákat, majd töltsön be beléjük adatokat.

## <a name="bulk-loading-with-apache-phoenix"></a>Tömeges betöltés az Apache Phoenix-szel

Többféle módon is bejuthat az adatok hbase, beleértve az ügyfél API-k használata, a MapReduce feladat TableOutputFormat, vagy az adatok bevitele manuálisan a HBase rendszerhéj használatával. A Phoenix két módszert biztosít a CSV-adatok Phoenix `psql`táblákba való betöltéséhez: egy ügyfélbetöltő eszközt, amelyet úgy hívnak, és egy MapReduce-alapú tömeges betöltési eszközt.

A `psql` szerszám egyszálas, és a legalkalmasabb megabájt vagy gigabájt nyi adat betöltésére. A betöltendő összes CSV-fájlnak a '.csv' fájlkiterjesztéssel kell rendelkeznie.  Az SQL parancsfájlokat a `psql` parancssorban is megadhatja a '.sql' fájlkiterjesztéssel.

A MapReduce tömeges betöltése sokkal nagyobb adatkötetekhez használatos, általában éles forgatókönyvekben, mivel a MapReduce több szálat használ.

Az adatok betöltésének megkezdése előtt ellenőrizze, hogy a Főnix engedélyezve van-e, és hogy a lekérdezés időtúlterhelési beállításai a várt módon vannak-e.  Férjen hozzá a HDInsight cluster [Apache Ambari](https://ambari.apache.org/) irányítópultjához, válassza a HBase lehetőséget, majd a Konfiguráció lapot.  Görgessen le, és ellenőrizze, hogy az Apache Phoenix a következőképpen `enabled` van-e beállítva:

![Az Apache Phoenix HDInsight fürt beállításai](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Táblázatok `psql` tömeges betöltésére használható

1. Hozzon létre `createCustomersTable.sql`egy nevű fájlt, és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Hozzon létre `listCustomers.sql`egy nevű fájlt, és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```sql
    SELECT * from Customers;
    ```

1. Hozzon létre `customers.csv`egy nevű fájlt, és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Hozzon létre `customers2.csv`egy nevű fájlt, és másolja az alábbi kódot a fájlba. Ezután mentse és zárja be a fájlt.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Nyisson meg egy parancssort, és módosítsa a könyvtárat az újonnan létrehozott fájlok helyére. Cserélje le az alábbi CLUSTERNAME-t a HBase-fürt tényleges nevére. Ezután hajtsa végre a kódot, hogy feltöltse a fájlokat a fürt csomópontjára:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az ssh munkamenetből módosítsa a könyvtárat a **psql** eszköz helyére. Az alábbi parancs végrehajtása:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Tömegesen töltse be az adatokat. Az alábbi kód egyaránt létrehozza az **Ügyfelek táblát,** majd feltölti az adatokat.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    A `psql` művelet befejezése után a következőhöz hasonló üzenetet kell látnia:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. A Vevők tábla `psql` tartalmának megtekintéséhez továbbra is használható. Hajtsa végre az alábbi kódot:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Másik lehetőségként használhatja [a HBase rendszerhéjat](./query-hbase-with-hbase-shell.md)vagy az [Apache Zeppelint](./apache-hbase-phoenix-zeppelin.md) az adatok lekérdezéséhez.

1. Töltsön fel további adatokat. Most, hogy a tábla már létezik, a parancs megadja a táblát. Az alábbi parancs végrehajtása:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>A MapReduce használata tömeges betöltési táblázatokhoz

A fürtön elosztott nagyobb átviteli sebességű betöltéshez használja a MapReduce load eszközt. Ez a betöltő először átalakítja az összes adatot HFiles, majd biztosítja a létrehozott HFiles a HBase.

1. Ez a szakasz az ssh munkamenettel folytatódik, és a korábban létrehozott objektumok. Szükség szerint hozza létre az **Ügyfelek** tábla és **a customers.csv** fájlt a fenti lépésekkel. Ha szükséges, hozza létre újra az ssh kapcsolatot.

1. A Vevők tábla tartalmának **csonkolása.** A megnyitott ssh munkamenetből hajtsa végre az alábbi parancsokat:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Másolja `customers.csv` a fájlt a headnode az Azure Storage.Copy the file from your headnode to Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Váltás a MapReduce tömeges betöltési parancs végrehajtási könyvtárára:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Indítsa el a CSV MapReduce `hadoop` betöltőt a parancs használatával a Phoenix ügyféljar:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    A feltöltés befejezése után a következőhöz hasonló üzenetet kell látnia:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. A MapReduce és az Azure Data Lake Storage használatához keresse `hbase.rootdir` meg `hbase-site.xml`a Data Lake Storage gyökérkönyvtárát, amely a. A következő parancsban a Data Lake `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`Storage gyökérkönyvtára . Ebben a parancsban adja meg a Data Lake Storage bemeneti és kimeneti mappáit paraméterként:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Az adatok lekérdezéséhez és megtekintéséhez használhatja a **psql-t** a korábban leírtak szerint. HBase [shell](./query-hbase-with-hbase-shell.md)vagy Apache [Zeppelin](./apache-hbase-phoenix-zeppelin.md)is használható.

## <a name="recommendations"></a>Javaslatok

* Használja ugyanazt a tárolóeszközt a bemeneti és kimeneti mappákhoz, az Azure Storage (WASB) vagy az Azure Data Lake Storage (ADL) számára. Az azure storage-ból a Data Lake Storage-ba történő adatátvitelhez használja a `distcp` következő parancsot:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Használjon nagyobb méretű munkavégző csomópontokat. A MapReduce tömeges másolás leképezési folyamatai nagy mennyiségű ideiglenes kimenetet eredményeznek, amelyek kitöltik a rendelkezésre álló nem DFS-területet. Nagy mennyiségű tömeges betöltéséhez használjon több és nagyobb méretű munkavégző csomópontokat. A fürthöz lefoglaló munkavégző csomópontok száma közvetlenül befolyásolja a feldolgozási sebességet.

* A bemeneti fájlok felosztása ~10 GB-os adattömbökre. A tömeges betöltés nagy tárolóigényes művelet, így a bemeneti fájlok több adattömbre bontása jobb teljesítményt eredményez.

* Kerülje a régiókiszolgálói elérési pontokat. Ha a sorkulcs monoton módon növekszik, a HBase szekvenciális írásai régiókiszolgálóhotspottingot okozhatnak. A sorkulcs *sózása* csökkenti a szekvenciális írásokat. Phoenix lehetővé teszi, hogy átláthatóan sózott a sor gombot egy sózás byte egy adott táblázat, az alábbiakban említett.

## <a name="next-steps"></a>További lépések

* [Tömeges adatbetöltés az Apache Phoenix segítségével](https://phoenix.apache.org/bulk_dataload.html)
* [Az Apache Phoenix használata Linux-alapú Apache HBase fürtökkel a HDInsightban](../hbase/apache-hbase-query-with-phoenix.md)
* [Sózott asztalok](https://phoenix.apache.org/salted.html)
* [Apache Phoenix nyelvtan](https://phoenix.apache.org/language/index.html)
