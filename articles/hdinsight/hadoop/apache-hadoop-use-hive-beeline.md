---
title: Az Apache Hive – Azure HDInsight az Apache a Beeline használata
description: Ismerje meg, hogyan használhatja a Beeline-ügyfél és a Hadoop Hive-lekérdezések futtatása a HDInsight. A beeline használata a hiveserver2-n keresztül JDBC egy segédprogramot.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: a beeline hive, a hive a beeline
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: hrasheed
ms.openlocfilehash: 23fa146b7bdaef0451984d0fbc638c57691cf259
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201720"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Az Apache Hive az Apache a Beeline-ügyfél használata

Ismerje meg, hogyan használható [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) HDInsight az Apache Hive-lekérdezések futtatásához.

A beeline egy Hive-ügyfél, amely része az átjárócsomópontokkal a HDInsight-fürt. A beeline JDBC hiveserver2-n keresztül, a HDInsight-fürtön lévő üzemeltetett szolgáltatásként való kapcsolódáshoz használ. A Beeline használatával távoli elérése a Hive a HDInsight az interneten keresztül. Az alábbi példák megadják a leggyakrabban használt kapcsolati karakterláncok Beeline a HDInsight segítségével kapcsolódhat:

* __Az SSH-kapcsolatot a Beeline segítségével átjárócsomópontjával vagy peremhálózati csomópont__: `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'`
* __A Beeline használata a HDInsight egy Azure virtuális hálózaton keresztül csatlakozó ügyfél,__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`
* __A Beeline használata a HDInsight vállalati biztonsági csomag (ESP) fürthöz csatlakozik egy Azure virtuális hálózaton keresztüli, ügyfél__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>`
* __A Beeline használata a HDInsight a nyilvános interneten keresztül csatlakozó ügyfél,__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password`

> [!NOTE]  
> Cserélje le `admin` az a fürt a fürt bejelentkezési fiókjának.
>
> Cserélje le `password` az a fürt bejelentkezési fiókjának jelszavát.
>
> Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére.
>
> Ha a fürt virtuális hálózaton keresztül kapcsolódik, cserélje le a `<headnode-FQDN>` egy fürt átjárócsomójával létesített teljesen minősített nevére.
>
> Ha egy vállalati biztonsági csomag (ESP) fürthöz csatlakozik, cserélje le a `<AAD-Domain>` , az Azure Active Directory (AAD), amely csatlakozik a fürt nevét. Cserélje le `<username>` a tartományon a fürt elérésére jogosult fiók nevével.

## <a id="prereq"></a>Előfeltételek

* Egy Linux-alapú Hadooppal a HDInsight-fürt verziója 3.4-es vagy nagyobb.

  > [!IMPORTANT]  
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy SSH-ügyfél vagy a helyi a Beeline-ügyfél. A legtöbb a jelen dokumentumban leírt lépések azt feltételezik, hogy használ a Beeline egy SSH-munkamenetből a fürthöz. A Beeline futtatott a fürtön kívülről információért lásd a [a Beeline használata távoli](#remote) szakaszban.

    SSH használatával kapcsolatos további információkért lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Hive-lekérdezés futtatása

1. Ha a Beeline-től kezdődően meg kell adnia egy kapcsolati karakterláncot a hiveserver2-n keresztül a HDInsight-fürtön:

    * Ha a nyilvános interneten keresztül kapcsolódik, meg kell adnia a fürt bejelentkezési fiók nevét (alapértelmezett `admin`) és a jelszót. Például a Beeline ügyfél rendszerről való kapcsolódáshoz használ a `<clustername>.azurehdinsight.net` címét. A kapcsolat-as porton `443`, és SSL-titkosítás használatával:

        ```bash
        beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
        ```

    * Ha egy fürt átjárócsomójával létesített SSH-munkamenetből történő kapcsolódik, csatlakozhat a `headnodehost` cím, port `10001`:

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    * Ha egy Azure virtuális hálózaton keresztül kapcsolódik, meg kell adnia a fürt fő csomópontjának teljes tartománynevét (FQDN). Mivel ez a kapcsolat közvetlenül a fürtcsomópontokon történik, a kapcsolat-portot használja `10001`:

        ```bash
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
        ```
    * Ha egy vállalati biztonsági csomag (ESP) fürthöz való csatlakozás tartományhoz az Azure Active Directory (AAD), is meg kell adnia a tartománynevet `<AAD-Domain>` és a fürt elérésére jogosult tartományi felhasználói fiók nevével `<username>`:
        
        ```bash
        kinit <username>
        beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
        ```

2. A beeline-parancsok kezdődhet egy `!` karakter, például `!help` megjeleníti a súgót. Azonban a `!` egyes parancsok elhagyható. Ha például `help` is működik.

    Van egy `!sql`, amely segítségével hajtsa végre a HiveQL utasításokat. Azonban HiveQL ezért gyakran használják, hogy az előző kihagyhatja `!sql`. A következő két utasításokat egyenértékűek:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Egy új fürtön, csak egy táblázat szerepel: **hivesampletable**.

3. A következő paranccsal a hivesampletable sémáját megjeleníteni:

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

    Ez a témakör az oszlopokat a táblában.

4. Adja meg az alábbi utasításokat, hozzon létre egy táblát nevű **log4jLogs** mintaadatokkal, a HDInsight-fürt használatával:

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

    Ezek az utasítások hajtsa végre a következő műveleteket:

    * `DROP TABLE` – Ha a tábla létezik, akkor az törlődik.

    * `CREATE EXTERNAL TABLE` – Létrehoz egy **külső** Hive táblát. Külső táblák csak tárolja a tábla definíciójának Hive. Az adatok az eredeti helyén marad.

    * `ROW FORMAT` – Az adatok formázását. Ebben az esetben minden napló mezőinek vesszővel elválasztva.

    * `STORED AS TEXTFILE LOCATION` – Ha az adatok tárolását, és milyen formátumú fájlokban.

    * `SELECT` -Az összes sor számát kiválasztja ahol oszlop **t4** értéket tartalmazza **[hiba]**. A lekérdezés visszaad egy értéket a **3** annyi három sort, amely tartalmazza ezt az értéket.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive megkísérli a alkalmazni a sémát a címtárban található összes fájl. Ebben az esetben a könyvtárban található fájlok, amelyek nem egyeznek a sémát. Szemétgyűjtési adatokat a eredmények elkerülése érdekében a jelen nyilatkozat arra utasítja a Hive, hogy csak adja vissza adatokat a végződésű fájlokból. napló.

  > [!NOTE]  
  > Külső táblák kell használni, amikor várhatóan az alapul szolgáló adatokat egy külső forrás frissíteni kell. Például az automatikus feltöltési folyamat vagy a MapReduce művelet.
  >
  > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.

    Ez a parancs kimenete az alábbi szöveghez hasonló:

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

5. A kilépéshez a Beeline használata `!exit`.

### <a id="file"></a>A Beeline használata futtatni egy HiveQL

Az alábbi lépések segítségével hozzon létre egy fájlt, majd futtassa azt a Beeline használata.

1. A következő paranccsal hozzon létre egy fájlt **query.hql**:

    ```bash
    nano query.hql
    ```

2. Használja a következő szöveget a fájl tartalmát. Ez a lekérdezés új táblát hoz létre "belső" nevű **hibanaplókat**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások hajtsa végre a következő műveleteket:

    * **Hozzon létre tábla IF NOT EXISTS** – Ha a tábla már nem létezik, a rendszer létrehozza. Mivel a **külső** kulcsszó nem használható, ez az utasítás létrehoz egy belső táblázatban. Belső táblák Hive adattárházban tárolódnak, és Hive teljes egészében kezeli.
    * **TÁROLT AS ORC** – optimalizált sor Oszlopalapú (ORC) formátumban tárolja az adatokat. ORC formátum Hive-adatok tárolására szolgáló nagymértékben optimalizált és hatékony formátumban.
    * **ÍRJA FELÜL AZ INSERT... Válassza ki** -sorait kiválasztja a **log4jLogs** tartalmazó tábla **[hiba]**, majd beszúrja az adatokat a **hibanaplókat** tábla.

    > [!NOTE]  
    > Ellentétben a külső táblák elvetését egy belső tábla törli az alapul szolgáló adatokat.

3. Mentse a fájlt, használja a **Ctrl**+**_X**, majd adja meg **Y**, és végül **Enter**.

4. A Beeline használata fájl futtatásához használja a következő:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > A `-i` paraméter Beeline elindul, és található utasításokat futtat a `query.hql` fájlt. Ha a lekérdezés befejeződött, akkor érkeznek a `jdbc:hive2://headnodehost:10001/>` parancssort. Egy fájl segítségével is futtathatja a `-f` paramétert, amely a Beeline indulása után kilép, a lekérdezés befejeződött.

5. Ellenőrizze, hogy a **hibanaplókat** létrehozott táblázat maradványai, használja a következő utasítást az összes sorát **hibanaplókat**:

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

## <a id="remote"></a>A Beeline használata távoli

Ha a Beeline helyben telepítve van, és a nyilvános interneten keresztül csatlakozni, használja a következő paramétereket:

* __Kapcsolati karakterlánc__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __Fürt bejelentkezési neve__: `-n admin`

* __Fürt bejelentkezési jelszava__ `-p 'password'`

Cserélje le a `clustername` a HDInsight-fürt nevére, a kapcsolati karakterláncban.

Cserélje le `admin` nevére a fürt bejelentkezési adatai, és cserélje le `password` a fürt bejelentkezési jelszavával.

Ha a Beeline helyben telepítve van, és egy Azure virtuális hálózaton keresztül, használja a következő paramétereket:

* __Kapcsolati karakterlánc__: `-u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'`

Egy átjárócsomópontjával teljesen minősített tartományneve megkereséséhez használja a található információk a [kezelése HDInsight az Apache Ambari REST API használatával](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentumot.

## <a id="sparksql"></a>A Beeline használata Apache Spark használata

Az Apache Spark biztosít a hiveserver2-n keresztül, amely is hívják mint a Spark Thrift-kiszolgáló a saját megvalósítását. Ez a szolgáltatás használ a Spark SQL helyett a Hive a lekérdezéseket, és jobb teljesítmény érdekében a lekérdezéstől függően előfordulhat, hogy adja meg.

A __kapcsolati karakterlánc__ használható, ha az interneten keresztül csatlakozó némileg eltérő. Hanem `httpPath=/hive2` van `httpPath/sparkhive2`. Az alábbiakban látható egy példa az interneten keresztül csatlakozó:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Ha kapcsolódik közvetlenül a fürt fő csomópontjának, vagy egy erőforráscsoport, a HDInsight-fürt azonos Azure virtuális hálózaton belül, a port `10002` Spark Thrift-kiszolgáló helyett használjon `10001`. Az alábbiakban látható egy példa a az átjárócsomóponthoz való közvetlen csatlakozás lehetőségét:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a id="summary"></a><a id="nextsteps"></a>Következő lépések

A Hive a HDInsight további általános információkért tekintse meg a következő dokumentumot:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

További információ a más módokon használhatja a Hadoop on HDInsight az alábbi dokumentumokban talál:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Ha Tez Hive-val használ, tekintse meg a következő dokumentumot: [Az Apache Ambari Tez nézetben jelenítheti meg a Linux-alapú HDInsight](../hdinsight-debug-ambari-tez-view.md).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
