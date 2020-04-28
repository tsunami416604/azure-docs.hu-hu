---
title: Az Apache beines használata a Apache Hive-Azure HDInsight
description: Ismerje meg, hogyan futtathat kaptár-lekérdezéseket a Hadoop-mel a HDInsight-on a Beeline-ügyfél használatával. A Beeline egy olyan segédprogram, amely a HiveServer2-t a JDBC protokollon keresztül folytatja.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 2396207c88716420d299382006a270eb747ddc03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192663"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Az Apache Beeline-ügyfél használata Apache Hive-val

Ismerje meg, hogyan használható az [Apache beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) Apache Hive lekérdezések futtatásához a HDInsight.

A Beeline egy kaptár-ügyfél, amely a HDInsight-fürt fő csomópontjain található. Ha helyileg szeretné telepíteni a beelinet, tekintse meg az alábbi, a [Beeline-ügyfél telepítése](#install-beeline-client)című témakört. A Beeline JDBC használatával csatlakozik a HDInsight-fürtön üzemeltetett HiveServer2-hez. A Beeline használatával távolról is elérheti a HDInsight a struktúrát az interneten keresztül. A következő példák a HDInsight való kapcsolódáshoz használt leggyakoribb kapcsolati karakterláncokat biztosítják a Beeline szolgáltatásból.

## <a name="types-of-connections"></a>A kapcsolatok típusai

### <a name="from-an-ssh-session"></a>SSH-munkamenetből

Ha egy SSH-munkamenetből egy fürt átjárócsomóponthoz kapcsolódik, akkor a `headnodehost` porton `10001`a következőhöz kapcsolódhat:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Azure-Virtual Network

Amikor egy ügyfélről csatlakozik egy Azure-Virtual Network HDInsight keresztül, meg kell adnia a fürt fő csomópontjának teljes tartománynevét (FQDN). Mivel ez a kapcsolódás közvetlenül a fürtcsomópontok számára történik, a csatlakozás a portot `10001`használja:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Cserélje `<headnode-FQDN>` le a helyére egy fürt átjárócsomóponthoz teljes tartománynevét. A átjárócsomóponthoz teljes tartománynevének megkereséséhez használja a [HDInsight kezelése az Apache Ambari REST API dokumentum segítségével](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) című témakört.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Enterprise Security Package (ESP) fürt HDInsight Kerberos használatával

Amikor az ügyfélről egy Enterprise Security Package (ESP) fürthöz csatlakozik, amely a (z) Azure Active Directory (HRE)-DS-hez csatlakozik a fürt ugyanazon tartományában lévő számítógépen, meg kell `<AAD-Domain>` adnia a tartomány nevét és a fürt `<username>`eléréséhez szükséges engedélyekkel rendelkező tartományi felhasználói fiók nevét is:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Cserélje `<username>` le a nevet a tartomány egy olyan fiókjának nevére, amely a fürt elérésére vonatkozó engedélyekkel rendelkezik. Cserélje `<AAD-DOMAIN>` le a helyére annak a Azure Active Directorynak (HRE) a nevét, amelyhez a fürt csatlakozik. Használjon nagybetűs karakterláncot `<AAD-DOMAIN>` az értékhez, ellenkező esetben a hitelesítő adat nem található. Szükség `/etc/krb5.conf` esetén ellenőrizze a tartománynevek nevét.

A Ambari-ből származó JDBC URL-cím megkeresése:

1. Egy webböngészőből nyissa meg `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`a következőt:, ahol `CLUSTERNAME` a a fürt neve. Győződjön meg arról, hogy a HiveServer2 fut.

1. A HiveServer2 JDBC URL-cím másolásához használja a vágólapot.

---

### <a name="over-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

Ha nyilvános vagy privát végpontokat használó fürthöz csatlakozik, meg kell adnia a fürt bejelentkezési fiókjának nevét (alapértelmezett `admin`) és jelszavát. Ha például egy ügyfélrendszer beelinét használja a `clustername.azurehdinsight.net` címnek való kapcsolódáshoz. Ez a kapcsolat a porton `443`keresztül történik, és a TLS/SSL protokollal van titkosítva.

Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje `admin` le a-t a fürthöz tartozó bejelentkezési fiókra. ESP-fürtök esetén használja a teljes UPN-t (például user@domain.com:). Cserélje `password` le a nevet a fürt bejelentkezési fiókjának jelszavára.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

vagy privát végpont esetén:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

A privát végpontok alapszintű Load balancerre mutatnak, amely csak ugyanabban a régióban található virtuális hálózatok érhető el. További információért tekintse [meg a globális VNet-társítási és terheléselosztó-korlátozásokat](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . A következő paranccsal `curl` `-v` elháríthatja a nyilvános vagy privát végpontokkal kapcsolatos csatlakozási problémákat a beelin használata előtt.

---

### <a name="use-beeline-with-apache-spark"></a>A Beeline használata a Apache Spark

Apache Spark a HiveServer2 saját implementációját biztosítja, amelyet más néven a Spark-takarékossági kiszolgálónak nevezünk. A szolgáltatás a Spark SQL használatával oldja fel a lekérdezéseket a struktúra helyett. A és a lekérdezéstől függően jobb teljesítményt nyújthat.

#### <a name="through-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

A használt kapcsolatok karakterlánca némileg eltér. Ahelyett, `httpPath=/hive2` hogy a- `httpPath/sparkhive2`t használja. Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje `admin` le a-t a fürthöz tartozó bejelentkezési fiókra. ESP-fürtök esetén használja a teljes UPN-t (például user@domain.com:). Cserélje `password` le a nevet a fürt bejelentkezési fiókjának jelszavára.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

vagy privát végpont esetén:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

A privát végpontok alapszintű Load balancerre mutatnak, amely csak ugyanabban a régióban található virtuális hálózatok érhető el. További információért tekintse [meg a globális VNet-társítási és terheléselosztó-korlátozásokat](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . A következő paranccsal `curl` `-v` elháríthatja a nyilvános vagy privát végpontokkal kapcsolatos csatlakozási problémákat a beelin használata előtt.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>A fürt feje vagy az Azure Virtual Networkon belül Apache Spark

Ha közvetlenül a fürt fő csomópontja vagy egy, a HDInsight-fürttel azonos Azure-Virtual Network található erőforráshoz csatlakozik, `10002` akkor a portot kell használni a Spark takarékosság- `10001`kiszolgálóhoz a helyett. Az alábbi példa bemutatja, hogyan csatlakozhat közvetlenül a fő csomóponthoz:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Példák az előfeltételekre

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* Figyelje meg a fürt elsődleges tárolójának URI-sémáját. Például `wasb://` az Azure Storage-hoz, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1hoz. Ha a biztonságos átvitel engedélyezve van az Azure Storage-hoz, `wasbs://`az URI a következő:. További információ: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

* 1. lehetőség: egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md). A jelen dokumentumban ismertetett lépések többsége azt feltételezi, hogy egy SSH-munkamenetből a fürtre használja a Beeline-t.

* 2. lehetőség: helyi Beeline-ügyfél.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Ez a példa a Beeline-ügyfél SSH-kapcsolatban való használatára épül.

1. Nyisson meg egy SSH-kapcsolódást a fürthöz az alábbi kóddal. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A következő parancs beírásával csatlakozhat a HiveServer2-hez a Beeline-ügyféllel az Open SSH-munkamenetből:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. A Beeline parancsok `!` karakterrel kezdődnek, például `!help` megjeleníti a súgót. Bizonyos parancsok `!` esetében azonban a kihagyható. Például a `help` is működik.

    Létezik `!sql`, amely a HiveQL utasítások végrehajtásához használatos. A HiveQL azonban általában úgy használják, hogy kihagyhatja a fentieket `!sql`. A következő két utasítás egyenértékű:

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

5. Adja meg a következő utasításokat a **log4jLogs** nevű tábla létrehozásához a HDInsight-fürthöz megadott mintaadatok használatával: (szükség szerint módosítsa az URI-séma alapján.)

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

    Ezek az utasítások a következő műveleteket végzik el:

    |Nyilatkozat |Leírás |
    |---|---|
    |TÁBLÁZAT ELDOBÁSA|Ha a tábla létezik, törölve lett.|
    |KÜLSŐ TÁBLA LÉTREHOZÁSA|Létrehoz egy **külső** táblát a struktúrában. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.|
    |SOR FORMÁTUMA|Az adat formázása. Ebben az esetben az egyes naplók mezői szóközzel vannak elválasztva.|
    |TEXTFILE HELYEN TÁROLVA|Az adattárolás helye és a fájl formátuma.|
    |SELECT|Kiválasztja az összes olyan sor számát, ahol a **T4** oszlop tartalmazza a **[hiba]** értéket. Ez a lekérdezés **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.|
    |INPUT__FILE__NAME például: "%. log"|A struktúra megpróbálja alkalmazni a sémát a címtárban található összes fájlra. Ebben az esetben a könyvtár olyan fájlokat tartalmaz, amelyek nem egyeznek a sémával. Ha meg szeretné akadályozni, hogy az eredmények ne kerüljenek az adatokba, ez az utasítás azt ismerteti, hogy a kaptár csak a. log fájlban végződő fájlokból tér vissza.|

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

6. Kilépés Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL-fájl futtatása

Ez a példa az előző példa folytatását szemlélteti. A következő lépésekkel hozzon létre egy fájlt, majd futtassa a Beeline paranccsal.

1. A következő parancs használatával hozzon létre egy **query. HQL**nevű fájlt:

    ```bash
    nano query.hql
    ```

1. Használja a következő szöveget a fájl tartalmának megfelelően. Ez a lekérdezés egy új, **alkalmazásnaplókat**nevű belső táblát hoz létre:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek az utasítások a következő műveleteket végzik el:

    |Nyilatkozat |Leírás |
    |---|---|
    |CREATE TABLE, HA NEM LÉTEZIK|Ha a tábla még nem létezik, akkor létrejön. Mivel a **külső** kulcsszó nincs használatban, az utasítás belső táblát hoz létre. A belső táblák a struktúra adattárházában tárolódnak, és a struktúra teljes mértékben felügyeli őket.|
    |ORK-KÉNT TÁROLVA|Az adatok az optimalizált sorok oszlopos (ORK) formátumban vannak tárolva. Az ork formátum a kaptárak adatok tárolására szolgáló, kiválóan optimalizált és hatékony formátum.|
    |FELÜLÍRÁS BESZÚRÁSA... Válassza|A **[Error]** karaktert tartalmazó sorok kiválasztása a **log4jLogs** táblából, majd az adatok beillesztése a **alkalmazásnaplókat** táblába.|

    > [!NOTE]  
    > A külső tábláktól eltérően a belső tábla eldobása a mögöttes adatokat is törli.

1. A fájl mentéséhez használja a **CTRL X billentyűkombinációt**+**X**, majd írja be az **Y**értéket, és végül **írja be**a következőt:.

1. A következő paranccsal futtathatja a fájlt a Beeline használatával:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > A `-i` paraméter elindítja és futtatja a `query.hql` fájlban szereplő utasításokat. A lekérdezés befejeződése után a rendszer megkéri a `jdbc:hive2://headnodehost:10001/>` kérdést. A (z) `-f` paraméterrel is futtathat egy fájlt, amely a lekérdezés befejeződése után kilép a szolgáltatásból.

1. A **alkalmazásnaplókat** tábla létrehozásának ellenőrzéséhez használja a következő utasítást a **alkalmazásnaplókat**összes sorának visszaküldéséhez:

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
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>A Beeline-ügyfél telepítése

Bár a Beeline szerepel a fő csomópontokon, érdemes helyileg telepítenie.  A helyi gépek telepítési lépései a [Linux Windows alrendszerén](https://docs.microsoft.com/windows/wsl/install-win10)alapulnak.

1. Frissítési csomagok listája. Adja meg a következő parancsot a bash-rendszerhéjban:

    ```bash
    sudo apt-get update
    ```

1. Telepítse a Java-t, ha nincs telepítve. A paranccsal megtekintheti `which java` a parancsot.

    1. Ha nincs telepítve Java-csomag, írja be a következő parancsot:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Nyissa meg a bashrc fájlt (gyakran megtalálható a ~/.bashrc `nano ~/.bashrc`):.

    1. Módosítsa a bashrc fájlt. Adja hozzá a következő sort a fájl végéhez:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Ezután nyomja le a **CTRL + X**billentyűkombinációt, majd az **Y**, majd a ENTER billentyűt.

1. Töltse le a Hadoop és a Beeline archívumokat, és írja be a következő parancsokat:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Csomagolja ki az archívumokat, írja be a következő parancsokat:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Módosítsa a bashrc-fájlt. Meg kell határoznia azt az elérési utat, ahová az archívumok kicsomagolása megtörtént. Ha a [Linux Windows alrendszerét](https://docs.microsoft.com/windows/wsl/install-win10)használja, és pontosan követte a lépéseket, az elérési `/mnt/c/Users/user/`út az `user` , ahol a a Felhasználónév.

    1. Nyissa meg a fájlt:`nano ~/.bashrc`

    1. Módosítsa az alábbi parancsokat a megfelelő elérési úttal, majd írja be őket a bashrc fájl végére:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Ezután nyomja le a **CTRL + X**billentyűkombinációt, majd az **Y**, majd a ENTER billentyűt.

1. Zárjuk be, majd nyissa meg újra a bash-munkamenetet.

1. A kapcsolatok tesztelése. Használja a kapcsolat formátumát a fentiekben [nyilvános vagy privát végpontokon](#over-public-or-private-endpoints).

## <a name="next-steps"></a>További lépések

* További általános információk a HDInsight-beli Kaptárról: [Apache Hive használata a Apache Hadoop használatával a HDInsight](hdinsight-use-hive.md)

* További információk a HDInsight-ben végzett Hadoop-használattal kapcsolatos egyéb módszerekről: [a MapReduce használata az Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)
