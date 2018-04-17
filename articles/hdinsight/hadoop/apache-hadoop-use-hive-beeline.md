---
title: Apache Hive - az Azure HDInsight Beeline használata |} Microsoft Docs
description: Megtudhatja, hogyan használhatja a Beeline a HDInsight Hadoop Hive-lekérdezések futtatásához. Beeline olyan eszköz, amellyel a hiveserver2-n keresztül JDBC használata.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline struktúra, hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: b96f457bc13ae3e412580096a1f9be865e64cb74
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-the-beeline-client-with-apache-hive"></a>Használhatja a Beeline Apache Hive

Ismerje meg, hogyan használható [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) hdinsight Hive-lekérdezések futtatásához.

Beeline Hive ügyfél, amely az átjárócsomópontokkal a HDInsight-fürt része. Beeline JDBC hiveserver2-n, a HDInsight-fürt üzemeltetett szolgáltatás való kapcsolódáshoz használ. Beeline segítségével távolról fér hozzá a HDInsight Hive az interneten keresztül. Az alábbi példák megadják a leggyakrabban használt Beeline a HDInsight való kapcsolódáshoz használt kapcsolati karakterláncok:

* __Az SSH-kapcsolat Beeline segítségével headnode vagy peremhálózati csomópont__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __A HDInsight az Azure virtuális hálózaton keresztül csatlakozó ügyfél, Beeline használatával__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __A HDInsight a nyilvános interneten keresztül csatlakozó ügyfél, Beeline használatával__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]
> Cserélje le `admin` a fürt a fürt bejelentkezési fiókkal.
>
> Cserélje le `password` a fürt bejelentkezési fiók jelszavával.
>
> Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére.
>
> Amikor a fürt virtuális hálózaton keresztül kapcsolódik, cserélje le a `<headnode-FQDN>` egy fürt headnode teljes tartománynévvel.

## <a id="prereq"></a>Előfeltételek

* A Linux-alapú Hadoop a HDInsight fürt 3.4 vagy újabb verziója.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy SSH-ügyfél vagy a helyi Beeline ügyfél. A legtöbb a jelen dokumentumban leírt lépések azt feltételezik, hogy használt Beeline az SSH-munkamenetet a fürthöz. A Beeline fut a fürtön kívüli információkért lásd: a [Beeline távolról használható](#remote) szakasz.

    További információ az SSH használatával, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Hive-lekérdezések futtatása

1. Beeline indításakor adjon meg egy kapcsolati karakterláncot hiveserver2-n a HDInsight-fürt:

    * Ha a nyilvános interneten keresztül, meg kell adnia a fürt bejelentkezési fiók neve (alapértelmezett `admin`) és a jelszót. Használata esetén például Beeline ügyfél rendszerről való kapcsolódáshoz a `<clustername>.azurehdinsight.net` cím. A kapcsolat porton keresztül `443`, és titkosítása SSL használatával:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Amikor a fürt headnode szeretne csatlakozni az SSH-munkamenetet, csatlakozhat a `headnodehost` cím, port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Ha egy Azure virtuális hálózaton keresztül, a fürt átjárócsomópontjába teljesen minősített tartománynevét (FQDN) kell megadnia. Mivel ez a kapcsolat közvetlenül a fürtcsomópontok történik, a kapcsolat használja-e a port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```

2. Beeline parancsok kezdődhet egy `!` karakter, például `!help` megjeleníti a súgót. Azonban a `!` egyes parancsok kihagyható. Például `help` is működik.

    Nincs olyan `!sql`, amellyel HiveQL utasítás végrehajtásához. Azonban HiveQL ezért általában arra használják, hogy az előző elhagyható `!sql`. A következő két utasításokat egyenértékűek:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Az új fürtön csak egy tábla szerepel: **hivesampletable**.

3. A következő parancs használatával a hivesampletable sémáját meg:

    ```hiveql
    describe hivesampletable;
    ```

    Ez a parancs visszaadja a következő információkat:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Ez a témakör a tábla oszlopai.

4. Adja meg a következő utasításokat nevű tábla létrehozása **log4jLogs** a HDInsight-fürthöz megadott mintaadatokat használatával:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

    * `DROP TABLE` -Ha a tábla létezik, akkor az törlődik.

    * `CREATE EXTERNAL TABLE` -Hoz létre egy **külső** Hive táblát. Külső táblák csak tárolja a tábladefiníció struktúra. Az adatok marad az eredeti helyen.

    * `ROW FORMAT` -Az adatok formázását. Ebben az esetben a mezőket az egyes naplókon szóközzel elválasztva.

    * `STORED AS TEXTFILE LOCATION` -Ha az adatok tárolását, és milyen formátumban.

    * `SELECT` -Választja ki az összes sorok számát ahol oszlop **t4** értéke **[hiba]**. A lekérdezés által visszaadott érték **3** ahány három ezt az értéket tartalmazó sorok.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive megkísérli a séma alkalmazása a könyvtárban található összes fájl. Ebben az esetben a directory nem egyeznek meg a séma fájlokat tartalmazza. Szemétgyűjtési adatok a eredmények elkerülése érdekében a jelen nyilatkozat közli struktúra, hogy azt kell csak vissza adatokat fájlok végződése. napló.

  > [!NOTE]
  > Külső táblák kell használni, amikor külső forrásból frissítenie kell az alapul szolgáló adatokat várt. Például egy automatizált adatok feltöltési folyamat vagy a MapReduce művelethez.
  >
  > A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában.

    Ez a parancs eredménye az alábbihoz hasonló:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

5. Beeline kilépéshez használja `!exit`.

### <a id="file"></a>Futtassa a HiveQL fájlt Beeline segítségével

Az alábbi lépések segítségével hozzon létre egy fájlt, majd Beeline használatával.

1. Az alábbi parancs segítségével hozzon létre egy fájlt **query.hql**:

    ```bash
    nano query.hql
    ```

2. A következő szöveg használható a fájl tartalmát. Ez a lekérdezés táblázatot hoz létre új "belső" nevű **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezekre az utasításokra hajtsa végre a következő műveleteket:

    * **Hozzon létre Ha nem létezik táblázat** -Ha a tábla már nem létezik, a rendszer létrehozza. Mivel a **külső** kulcsszó nem használható, a jelen nyilatkozat egy belső táblát hoz létre. Belső táblák a Hive-adatraktárban tárolja, és Hive teljesen kezeli.
    * **TÁROLT AS ORC** -tárolja az adatokat optimalizált sor oszlopos (ORC) formátumban. ORC Hive adattárolás magas optimalizált és hatékony formátuma érvénytelen.
    * **ÍRJA FELÜL AZ INSERT... Válassza ki** -sorát kiválasztja a **log4jLogs** tartalmazó **[hiba]**, majd szúrja be az adatokat a **errorLogs** tábla.

    > [!NOTE]
    > Ellentétben a külső táblákhoz az alapul szolgáló adatokat egy belső tábla eldobása törli.

3. Mentse a fájlt, használja a **Ctrl**+**_X**, majd adja meg **Y**, és végül **Enter**.

4. A fájl Beeline futtatásához használja a következőket:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > A `-i` paraméter Beeline elindul, és található utasításokat futtat a `query.hql` fájlt. A lekérdezés után megérkezik a a `jdbc:hive2://headnodehost:10001/>` kérdés. Egy fájl segítségével is futtathatja a `-f` paraméter, amely Beeline kilép a lekérdezés befejeződése után.

5. Annak ellenőrzésére, hogy a **errorLogs** táblázat létrejött, a következő utasítás használatával adja vissza az összes sorát **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Három sor adat vissza kell adni, az összes tartalmazó **[hiba]** az oszlop t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Beeline távolról használható

Ha helyileg telepített Beeline, és a nyilvános interneten keresztül csatlakozni, használja a következő paramétereket:

* __Kapcsolati karakterlánc__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Fürt bejelentkezési neve__: `-n admin`

* __Fürt bejelentkezési jelszó__ `-p 'password'`

Cserélje le a `clustername` a HDInsight-fürt neve a kapcsolati karakterláncban.

Cserélje le `admin` nevű, a fürt bejelentkezési és a név felülírandó `password` a fürt bejelentkezési jelszavával.

Ha helyileg telepített Beeline, és az Azure virtuális hálózaton keresztül csatlakozni, használja a következő paramétereket:

* __Kapcsolati karakterlánc__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

A teljesen minősített tartománynevét egy headnode megkereséséhez használja az információk a [az Ambari REST API használatával kezelheti a HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentum.

## <a id="sparksql"></a>Beeline használata Spark

Spark hiveserver2-n, amelyek van más néven a Spark Thrift-kiszolgáló a saját végrehajtásának biztosít. Ez a szolgáltatás Spark SQL használatával helyett Hive a lekérdezéseket, és attól függően, hogy a lekérdezés jobb teljesítményt biztosíthat.

Ha csatlakozni szeretne a Spark Thrift-kiszolgáló, a Spark on HDInsight-fürt, a port használatára `10002` helyett `10001`. Például: `beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'`.

> [!IMPORTANT]
> A Spark Thrift-kiszolgáló nem érhető el közvetlenül az interneten keresztül. Csak csatlakozhat az SSH-munkamenetet, vagy a HDInsight-fürttel azonos Azure virtuális hálózatán belül.

## <a id="summary"></a><a id="nextsteps"></a>További lépések

A Hive HDInsight további általános információkért tekintse meg a következő dokumentumban:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

A HDInsight Hadoop dolgozhat egyéb lehetőségeiről további információkért lásd a következő dokumentumokat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Ha Tez Hive használ, tekintse meg a következő dokumentumokat:

* [A Tez felhasználói felület használata a Windows-alapú HDInsight-on](../hdinsight-debug-tez-ui.md)
* [Az Ambari Tez nézetben a Linux-alapú HDInsight-on](../hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx
