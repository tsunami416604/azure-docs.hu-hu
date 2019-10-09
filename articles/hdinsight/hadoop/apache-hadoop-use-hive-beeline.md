---
title: Az Apache beines használata a Apache Hive-Azure HDInsight
description: Ismerje meg, hogyan futtathat kaptár-lekérdezéseket a Hadoop-mel a HDInsight-on a Beeline-ügyfél használatával. A Beeline egy olyan segédprogram, amely a HiveServer2-t a JDBC protokollon keresztül folytatja.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: d6063daa649b507057fd2a4468c32dad1cd35eec
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030427"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Használja az Apache Beeline-ügyfelet Apache Hive

Ismerje meg, hogyan használható az [Apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) Apache Hive lekérdezések futtatásához a HDInsight.

A Beeline egy kaptár-ügyfél, amely a HDInsight-fürt fő csomópontjain található. A Beeline JDBC használatával csatlakozik a HDInsight-fürtön üzemeltetett HiveServer2-hez. A Beeline használatával távolról is elérheti a HDInsight a struktúrát az interneten keresztül. Az alábbi példák a HDInsight való kapcsolódáshoz használt leggyakoribb kapcsolati karakterláncokat biztosítják a következő esetekben:

## <a name="types-of-connections"></a>A kapcsolatok típusai

### <a name="from-an-ssh-session"></a>SSH-munkamenetből

Ha egy SSH-munkamenetből egy fürt átjárócsomóponthoz kapcsolódik, akkor a `headnodehost` címen csatlakozhat a következő porton: `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Azure-Virtual Network

Amikor egy ügyfélről csatlakozik egy Azure-Virtual Network HDInsight keresztül, meg kell adnia a fürt fő csomópontjának teljes tartománynevét (FQDN). Mivel ez a kapcsolódás közvetlenül a fürtcsomópontok számára történik, a csatlakozás a következő portot használja: `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Cserélje le a `<headnode-FQDN>` értéket a fürt átjárócsomóponthoz teljes tartománynevére. A átjárócsomóponthoz teljes tartománynevének megkereséséhez használja a [HDInsight kezelése az Apache Ambari REST API dokumentum segítségével](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) című témakört.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Enterprise Security Package (ESP) fürt HDInsight Kerberos használatával

Amikor az ügyfélről egy Enterprise Security Package (ESP) fürthöz csatlakozik, amely a (z) Azure Active Directory (HRE)-DS-hez csatlakozik a fürt ugyanazon tartományában található számítógépen, meg kell adnia a tartománynevet is `<AAD-Domain>`, valamint egy tartományi felhasználói fiók nevét, amely jogosult a következőre: hozzáférés a (z) `<username>` fürthöz:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Cserélje le a `<username>` értéket a tartományhoz tartozó, a fürt elérésére jogosult fiók nevére. A `<AAD-DOMAIN>` helyére írja be annak a Azure Active Directorynak (HRE) a nevét, amelyhez a fürt csatlakozik. Használjon nagybetűs karakterláncot a `<AAD-DOMAIN>` értékhez, ellenkező esetben a hitelesítő adat nem található. Szükség esetén ellenőrizze a tartománynevek @no__t – 0 értéket.

---

### <a name="over-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

Ha nyilvános vagy privát végpontokat használó fürthöz csatlakozik, meg kell adnia a fürt bejelentkezési fiókjának nevét (alapértelmezett @no__t – 0) és a jelszót. Ha például egy ügyfélrendszer beelinét használja a `<clustername>.azurehdinsight.net` címnek való kapcsolódáshoz. Ez a kapcsolat a `443` porton keresztül történik, és SSL használatával titkosítva:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

vagy privát végpont esetén:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje le a `admin` értéket a fürthöz tartozó bejelentkezési fiókkal. Cserélje le a `password` értéket a fürt bejelentkezési fiókjának jelszavára.

---

### <a id="sparksql"></a>A Beeline használata a Apache Spark

Apache Spark a HiveServer2 saját implementációját biztosítja, amelyet más néven a Spark-takarékossági kiszolgálónak nevezünk. Ez a szolgáltatás a Spark SQL-t használja a lekérdezések feloldásához a struktúra helyett, és a lekérdezéstől függően jobb teljesítményt nyújthat.

#### <a name="through-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

A használt kapcsolatok karakterlánca némileg eltér. A `httpPath=/hive2` érték nem tartalmaz `httpPath/sparkhive2` értéket:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

vagy privát végpont esetén:

```bash 
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje le a `admin` értéket a fürthöz tartozó bejelentkezési fiókkal. Cserélje le a `password` értéket a fürt bejelentkezési fiókjának jelszavára.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>A fürt feje vagy az Azure Virtual Networkon belül Apache Spark

Ha közvetlenül a fürt fő csomópontja vagy egy, a HDInsight-fürttel azonos Azure-Virtual Network található erőforráshoz csatlakozik, a `10002` portot kell használni a Spark takarékosság-kiszolgálóhoz a `10001` helyett. Az alábbi példa bemutatja, hogyan csatlakozhat közvetlenül a fő csomóponthoz:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Előfeltételek

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* Figyelje meg a fürt elsődleges tárolójának [URI-sémáját](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Például `wasb://` az Azure Storage-hoz, `abfs://` for Azure Data Lake Storage Gen2, vagy `adl://` for Azure Data Lake Storage Gen1. Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, az URI `wasbs://`. További információ: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

* 1\. módszer: Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md). A jelen dokumentumban ismertetett lépések többsége azt feltételezi, hogy egy SSH-munkamenetből a fürtre használja a Beeline-t.

* 2\. lehetőség:  Helyi Beeline-ügyfél.

## <a id="beeline"></a>Struktúra-lekérdezés futtatása

Ez a példa a Beeline-ügyfél SSH-kapcsolatban való használatára épül.

1. Nyisson meg egy SSH-kapcsolódást a fürthöz az alábbi kóddal. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A következő parancs beírásával csatlakozhat a HiveServer2-hez a Beeline-ügyféllel az Open SSH-munkamenetből:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. A Beeline parancsok `!` karakterrel kezdődnek, például a `!help` megjeleníti a súgót. Egyes parancsok esetében azonban a `!` is kihagyható. Például `help` is működik.

    Van `!sql`, amely a HiveQL utasítások végrehajtásához használatos. A HiveQL azonban általában úgy használják, hogy kihagyhatja az előző `!sql` értéket. A következő két utasítás egyenértékű:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Egy új fürtön csak egy tábla jelenik meg: **hivesampletable**.

4. A hivesampletable sémájának megjelenítéséhez használja a következő parancsot:

    ```hiveql
    describe hivesampletable;
    ```

    Ez a parancs a következő információkat adja vissza:

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

    Ez az információ a tábla oszlopait ismerteti.

5. Adja meg a következő utasításokat a **log4jLogs** nevű tábla létrehozásához a HDInsight-fürthöz megadott mintaadatok használatával: (Szükség szerint módosítsa az [URI-séma](../hdinsight-hadoop-linux-information.md#URI-and-scheme)alapján.)

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

    Ezek az utasítások a következő műveleteket hajtják végre:

    * @no__t – 0 – Ha a tábla létezik, törölve lett.

    * @no__t – 0 – **külső** táblát hoz létre a struktúrában. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.

    * @no__t – 0 – az adat formázása. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.

    * @no__t – 0 – az adattárolási és a fájlformátumban tárolt fájlok.

    * `SELECT` – kiválasztja az összes olyan sor számát, ahol a **T4** oszlop tartalmazza a **[hiba]** értéket. Ez a lekérdezés **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.

    * @no__t – 0 – a struktúra megpróbálja alkalmazni a sémát a címtárban található összes fájlra. Ebben az esetben a könyvtár olyan fájlokat tartalmaz, amelyek nem egyeznek a sémával. Ha meg szeretné akadályozni, hogy az eredmények ne kerüljenek az adatokba, ez az utasítás azt ismerteti, hogy a kaptár csak a. log fájlban végződő fájlokból tér vissza.

   > [!NOTE]  
   > Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy automatizált adatfeltöltési folyamat vagy egy MapReduce művelet.
   >
   > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

    A parancs kimenete az alábbi szöveghez hasonló:

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

6. A Beeline kilépéséhez használja a következőt: `!exit`.

## <a id="file"></a>HiveQL-fájl futtatása

Ez az előző példa folytatása. A következő lépésekkel hozzon létre egy fájlt, majd futtassa a Beeline paranccsal.

1. A következő parancs használatával hozzon létre egy **query. HQL**nevű fájlt:

    ```bash
    nano query.hql
    ```

2. Használja a következő szöveget a fájl tartalmának megfelelően. Ez a lekérdezés egy új, **alkalmazásnaplókat**nevű belső táblát hoz létre:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások a következő műveleteket hajtják végre:

   * **CREATE TABLE, ha nem létezik** – ha a tábla még nem létezik, létrejön. Mivel a **külső** kulcsszó nincs használatban, az utasítás belső táblát hoz létre. A belső táblák a struktúra adattárházában tárolódnak, és a struktúra teljes mértékben felügyeli őket.
   * **Ork-ként tárolva** – az adatok tárolása optimalizált sor oszlopos (ORK) formátumban történik. Az ork formátum a kaptárak adatok tárolására szolgáló, kiválóan optimalizált és hatékony formátum.
   * @NO__T – 0INSERT FELÜLÍRÁSA... Válassza a @ no__t-0 – a (z) **[Error]** karaktert tartalmazó sorok kiválasztását a **log4jLogs** táblából, majd beszúrja az adatait a **alkalmazásnaplókat** táblába.

    > [!NOTE]  
    > A külső tábláktól eltérően a belső tábla eldobása a mögöttes adatokat is törli.

3. A fájl mentéséhez használja a **Ctrl**+ **_X**, majd írja be az **Y**értéket, és végül **írja be**a következőt:.

4. A következő paranccsal futtathatja a fájlt a Beeline használatával:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > A `-i` paraméter elindítja a beelinét, és futtatja az utasításokat a `query.hql` fájlban. A lekérdezés befejeződése után megérkezik a `jdbc:hive2://headnodehost:10001/>` parancssorba. A `-f` paraméterrel is futtathat egy fájlt, amely a lekérdezés befejeződése után kizárja a betöltést.

5. A **alkalmazásnaplókat** tábla létrehozásának ellenőrzéséhez használja a következő utasítást a **alkalmazásnaplókat**összes sorának visszaküldéséhez:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Három sornyi értéket kell visszaadnia, mind a (z **) [Error]** értéket tartalmazza a T4 oszlopban:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="summary"></a><a id="nextsteps"></a>További lépések

A HDInsight-beli struktúrával kapcsolatos általános információkért tekintse meg a következő dokumentumot:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

További információk a HDInsight-Hadoop való használatáról a következő dokumentumokban találhatók:

* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)
