---
title: Az Apache Hive – Azure HDInsight az Apache a Beeline használata
description: Ismerje meg, hogyan használhatja a Beeline-ügyfél és a Hadoop Hive-lekérdezések futtatása a HDInsight. A beeline használata a hiveserver2-n keresztül JDBC egy segédprogramot.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056852"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Az Apache Hive az Apache a Beeline-ügyfél használata

Ismerje meg, hogyan használható [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) HDInsight az Apache Hive-lekérdezések futtatásához.

A beeline egy Hive-ügyfél, amely része az átjárócsomópontokkal a HDInsight-fürt. A beeline JDBC hiveserver2-n keresztül, a HDInsight-fürtön lévő üzemeltetett szolgáltatásként való kapcsolódáshoz használ. A Beeline használatával távoli elérése a Hive a HDInsight az interneten keresztül. Az alábbi példák megadják a leggyakrabban használt kapcsolati karakterláncok Beeline a HDInsight segítségével kapcsolódhat:

## <a name="types-of-connections"></a>Kapcsolattípus

### <a name="from-an-ssh-session"></a>Egy SSH-munkamenetből

Ha egy fürt átjárócsomójával létesített SSH-munkamenetből történő kapcsolódik, majd csatlakozhat az `headnodehost` cím, port `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Egy Azure virtuális hálózaton keresztül

Ha kapcsolódik egy ügyfél a HDInsight egy Azure virtuális hálózaton keresztül, meg kell adnia a fürt fő csomópontjának teljes tartománynevét (FQDN). Mivel ez a kapcsolat közvetlenül a fürtcsomópontokon történik, a kapcsolat-portot használja `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Cserélje le `<headnode-FQDN>` egy fürt átjárócsomójával létesített teljesen minősített nevére. Egy átjárócsomópontjával teljesen minősített tartományneve megkereséséhez használja a található információk a [kezelése HDInsight az Apache Ambari REST API használatával](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) dokumentumot.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Vállalati biztonsági csomag (ESP) HDInsight-fürthöz

Ha az Azure Active Directory (AAD) kliensből csatlakozik egy vállalati biztonsági csomag (ESP) fürthöz csatlakozik, is meg kell adnia a tartománynevet `<AAD-Domain>` és a fürt elérésére jogosult tartományi felhasználói fiók nevével `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Cserélje le `<username>` a tartományon a fürt elérésére jogosult fiók nevével. Cserélje le `<AAD-DOMAIN>` , az Azure Active Directory (AAD), amely csatlakozik a fürt nevét. Nagybetűk karakterláncát használja a `<AAD-DOMAIN>` érték, ellenkező esetben a hitelesítő adatok nem találhatók. Ellenőrizze `/etc/krb5.conf` a kezdőtartomány-nevek, szükség esetén.

---

### <a name="over-public-internet"></a>Nyilvános interneten keresztül

Ha a nyilvános interneten keresztül kapcsolódik, meg kell adnia a fürt bejelentkezési fiók nevét (alapértelmezett `admin`) és a jelszót. Például a Beeline ügyfél rendszerről való kapcsolódáshoz használ a `<clustername>.azurehdinsight.net` címét. A kapcsolat-as porton `443`, és SSL-titkosítás használatával:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje le `admin` az a fürt a fürt bejelentkezési fiókjának. Cserélje le `password` az a fürt bejelentkezési fiókjának jelszavát.

---

### <a id="sparksql"></a>A Beeline használata Apache Spark használata

Az Apache Spark biztosít a hiveserver2-n keresztül, amely is hívják mint a Spark Thrift-kiszolgáló a saját megvalósítását. Ez a szolgáltatás használ a Spark SQL helyett a Hive a lekérdezéseket, és jobb teljesítmény érdekében a lekérdezéstől függően előfordulhat, hogy adja meg.

#### <a name="over-public-internet-with-apache-spark"></a>Az Apache Spark a nyilvános interneten keresztül

Az interneten keresztül való kapcsolódáskor használt kapcsolati karakterlánc kis mértékben eltér. Hanem `httpPath=/hive2` van `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>A központi vagy a belső Azure Virtual Network használata Apache Spark-fürt

Ha kapcsolódik közvetlenül a fürt fő csomópontjának, vagy egy erőforráscsoport, a HDInsight-fürt azonos Azure virtuális hálózaton belül, a port `10002` Spark Thrift-kiszolgáló helyett használjon `10001`. Az alábbi példa bemutatja, hogyan közvetlenül is csatlakozhat az átjárócsomóponthoz:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Előfeltételek

* A HDInsight Hadoop-fürt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).

* Figyelje meg a [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme) a fürt elsődleges tárhelyeként. Ha például `wasb://` az Azure Storage esetében `abfs://` az Azure Data Lake Storage Gen2 vagy `adl://` az Azure Data Lake Storage Gen1. Ha az Azure Storage vagy a Data Lake Storage Gen2 engedélyezve van a biztonságos átvitel, az URI-ja `wasbs://` vagy `abfss://`, illetve. További információkért lásd: [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md).


* Option 1: Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md). A legtöbb a jelen dokumentumban leírt lépések azt feltételezik, hogy használ a Beeline egy SSH-munkamenetből a fürthöz.

* Option 2:  A helyi a Beeline-ügyfél.


## <a id="beeline"></a>Hive-lekérdezés futtatása

Ez a példa alapján az SSH-kapcsolatot a Beeline-ügyfél használatával.

1. Nyissa meg a fürthöz az SSH-kapcsolatot az alábbi kódra. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a jelszót az SSH-felhasználói fiókot.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Kapcsolódás hiveserver2-n keresztül a Beeline-ügyféllel a nyissa meg az SSH-munkamenetből a következő parancs beírásával:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. A beeline-parancsok kezdődhet egy `!` karakter, például `!help` megjeleníti a súgót. Azonban a `!` egyes parancsok elhagyható. Ha például `help` is működik.

    Van egy `!sql`, amely segítségével hajtsa végre a HiveQL utasításokat. Azonban HiveQL ezért gyakran használják, hogy az előző kihagyhatja `!sql`. A következő két utasításokat egyenértékűek:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Egy új fürtön, csak egy táblázat szerepel: **hivesampletable**.

4. A következő paranccsal a hivesampletable sémáját megjeleníteni:

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

5. Adja meg az alábbi utasításokat, hozzon létre egy táblát nevű **log4jLogs** mintaadatokkal, a HDInsight-fürt használatával: (Igény szerint felügyel alapján a [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
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

6. A kilépéshez a Beeline használata `!exit`.

## <a id="file"></a>HiveQL fájl futtatása

Ez az az előző példából folytatása. Az alábbi lépések segítségével hozzon létre egy fájlt, majd futtassa azt a Beeline használata.

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




## <a id="summary"></a><a id="nextsteps"></a>Következő lépések

A Hive a HDInsight további általános információkért tekintse meg a következő dokumentumot:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

További információ a más módokon használhatja a Hadoop on HDInsight az alábbi dokumentumokban talál:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
