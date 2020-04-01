---
title: Az Apache Beline használata az Apache Hive-val - Azure HDInsight
description: Ismerje meg, hogyan használhatja a Beeline-ügyfél tanusítására hive-lekérdezéseket a Hadoop segítségével a HDInsighton. A Beeline egy olyan segédprogram, amely a HiveServer2-vel dolgozik a JDBC felett.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 4f2b192765aab4c7cf18c62988ae2f6080b4b17c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436918"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Az Apache Beeline-ügyfél használata Apache Hive-val

Ismerje meg, hogyan futtathatja az [Apache Beeline használatával](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) az Apache Hive-lekérdezéseket a HDInsighton.

A Beeline egy Hive-ügyfél, amely a HDInsight-fürt fő csomópontjain található. A Beeline helyi telepítéséhez lásd alább [A beeline ügyfél telepítése](#install-beeline-client)című témakört. A Beeline a JDBC segítségével csatlakozik a HIVeServer2 szolgáltatáshoz, amely a HDInsight-fürtön található. A Beeline használatával a HIVe-t távolról is elérheti a HDInsight-on az interneten keresztül. Az alábbi példák a Beeline-tól a HDInsighthoz való csatlakozáshoz használt leggyakoribb kapcsolati karakterláncokat szolgálják.

## <a name="types-of-connections"></a>A kapcsolatok típusai

### <a name="from-an-ssh-session"></a>SSH-munkamenetből

Amikor Egy SSH-munkamenetből fürtcsomóponthoz csatlakozik, a porton `headnodehost` `10001`lévő címhez csatlakozhat:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Egy Azure virtuális hálózaton keresztül

Amikor egy ügyfélről a HDInsight-hoz csatlakozik egy Azure virtuális hálózaton keresztül, meg kell adnia a fürtfőcsomópont teljes mértékben minősített tartománynevét (FQDN). Mivel ez a kapcsolat közvetlenül a fürtcsomópontokhoz `10001`kapcsolódik, a kapcsolat portot használ:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Cserélje `<headnode-FQDN>` le a fürtcsomópont teljesen minősített tartománynevére. A headnode teljesen minősített tartománynevének megkereséséhez használja a [HDInsight kezelése az Apache Ambari REST API-dokumentum használatával](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) található információkat.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>HdInsight Enterprise Security Package (ESP) fürt a Kerberos használatával

Ha egy ügyfélről egy vállalati biztonsági csomag (ESP) fürthöz csatlakozik, amely a fürt ugyanazon tartományában lévő gépen csatlakozik az Azure `<AAD-Domain>` Active Directory (AAD)-DS `<username>`szolgáltatáshoz, meg kell adnia a fürt eléréséhez szükséges engedélyekkel rendelkező tartománynevet és tartományfelhasználói fiók nevét is:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Cserélje `<username>` le a tartományban lévő fiók nevére a fürt elérésére vonatkozó engedélyekkel. Cserélje `<AAD-DOMAIN>` le az Azure Active Directory (AAD) nevét, amelyhez a fürt csatlakozik. Használjon nagybetűs `<AAD-DOMAIN>` karakterláncot az értékhez, ellenkező esetben a hitelesítő adat nem található. Szükség `/etc/krb5.conf` esetén ellenőrizze a bazsámineveket.

A JDBC URL-cím megkeresése az Ambari-tól:

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`meg `CLUSTERNAME` a , ahol a fürt neve. Győződjön meg arról, hogy a HiveServer2 fut.

1. A HiveServer2 JDBC URL-címének másolásához használjon vágólapra.

---

### <a name="over-public-or-private-endpoints"></a>Nyilvános vagy magánvégpontokon keresztül

Ha nyilvános vagy privát végpontok használatával csatlakozik egy fürthöz, meg kell `admin`adnia a fürt bejelentkezési fióknevét (alapértelmezett) és a jelszót. Például a Beeline egy ügyfélrendszerből `clustername.azurehdinsight.net` csatlakozni a címhez. Ez a kapcsolat `443`porton keresztül történik, és TLS/SSL használatával titkosítva van.

Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje `admin` le a fürt bejelentkezési fiókjával a fürthöz. ESP-fürtök esetén használja a teljes upn-t (például). user@domain.com Cserélje `password` le a fürt bejelentkezési fiókjához.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

vagy a magánvégpont esetében:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

A privát végpontok egy alapszintű terheléselosztóra mutatnak, amely csak az ugyanabban a régióban társviszonyba tartozó VNET-kből érhető el. További információkért tekintse meg [a globális virtuális hálózatok társviszony-létesítési és terheléselosztók korlátait.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) A parancs `curl` segítségével `-v` elháríthatja a nyilvános vagy privát végpontokkal kapcsolatos kapcsolódási problémákat a beeline használata előtt.

---

### <a name="use-beeline-with-apache-spark"></a>A Beeline használata az Apache Sparkkal

Az Apache Spark saját megvalósítást biztosít a HiveServer2 számára, amelyet néha Spark Thrift-kiszolgálónak is neveznek. Ez a szolgáltatás a Spark SQL használatával oldja fel a lekérdezéseket a Hive helyett, és a lekérdezéstől függően jobb teljesítményt nyújthat.

#### <a name="through-public-or-private-endpoints"></a>Nyilvános vagy privát végpontokon keresztül

A használt kapcsolati karakterlánc némileg eltérő. Ahelyett, hogy `httpPath=/hive2` tartalmazza `httpPath/sparkhive2`azt . Cserélje le a `clustername` kifejezést a HDInsight-fürt nevére. Cserélje `admin` le a fürt bejelentkezési fiókjával a fürthöz. ESP-fürtök esetén használja a teljes upn-t (például). user@domain.com Cserélje `password` le a fürt bejelentkezési fiókjához.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

vagy a magánvégpont esetében:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

A privát végpontok egy alapszintű terheléselosztóra mutatnak, amely csak az ugyanabban a régióban társviszonyba tartozó VNET-kből érhető el. További információkért tekintse meg [a globális virtuális hálózatok társviszony-létesítési és terheléselosztók korlátait.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) A parancs `curl` segítségével `-v` elháríthatja a nyilvános vagy privát végpontokkal kapcsolatos kapcsolódási problémákat a beeline használata előtt.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>A fürtfejből vagy az Azure virtuális hálózaton belül az Apache Spark segítségével

Ha közvetlenül a fürtfőcsomópontról vagy ugyanabból az Azure virtuális hálózaton belül lévő `10002` erőforrásból csatlakozik, mint `10001`a HDInsight-fürt, a portot a Spark Thrift kiszolgálóhoz kell használni a helyett. A következő példa bemutatja, hogyan csatlakozhat közvetlenül a főcsomóponthoz:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>A példák előfeltételei

* Egy Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)

* Figyelje meg a fürt elsődleges tárolójának [URI-sémát.](../hdinsight-hadoop-linux-information.md#URI-and-scheme) Például `wasb://` az Azure `abfs://` Storage, az Azure Data `adl://` Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 esetében. Ha az Azure Storage biztonságos átvitele `wasbs://`engedélyezve van, az URI a . További információt a [biztonságos átvitel című](../../storage/common/storage-require-secure-transfer.md)témakörben talál.

* 1. lehetőség: Egy SSH ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md) A dokumentum legtöbb lépése feltételezi, hogy beeline-t használ egy SSH-munkamenetből a fürtbe.

* 2. lehetőség: Egy helyi Beeline kliens.

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Ez a példa a Beeline ügyfél SSH-kapcsolatból történő használatán alapul.

1. Nyisson meg egy SSH-kapcsolatot a fürthöz az alábbi kóddal. Cserélje le az `sshuser` elemet a fürt SSH-felhasználójára, illetve a `CLUSTERNAME` elemet a fürt nevére. Amikor a rendszer kéri, adja meg az SSH felhasználói fiók jelszavát.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Csatlakozzon a HiveServer2-hez a Beeline ügyféllel a megnyitott SSH-munkamenetből a következő parancs megadásával:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. A beeline parancsok `!` egy karakterrel kezdődnek, például `!help` súgót jelenít meg. Azonban `!` a kihagyható néhány parancs. Például, `help` is működik.

    Van, `!sql`amely a HiveQL utasítások végrehajtására szolgál. Azonban a HiveQL-t olyan gyakran használják, `!sql`hogy kihagyhatja az előzőt. A következő két állítás egyenértékű:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Új fürtön csak egy tábla szerepel a listában: **hivesampletable**.

4. A struktúramintatábla sémáját a következő paranccsal jelenítheti meg:

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

    Ez az információ a táblázat oszlopait írja le.

5. Adja meg a következő utasítások hozegy tábla nevű **log4jLogs** segítségével minta adatokat a HDInsight-fürt: (Szükség szerint vizsgálja felül az [URI-séma.)](../hdinsight-hadoop-linux-information.md#URI-and-scheme)

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

    Ezek a kijelentések a következő műveleteket követik:

    |Nyilatkozat |Leírás |
    |---|---|
    |TÁBLÁZAT LEDOBÁSA|Ha a tábla létezik, törlődik.|
    |KÜLSŐ TÁBLA LÉTREHOZÁSA|Külső **táblát** hoz létre a Hive-ban. A külső táblák csak a tábladefiníciót tárolják hive.External tables only store the table definition in Hive. Az adatok az eredeti helyen maradnak.|
    |SOR FORMÁTUMA|Az adatok formázásának megtörténik. Ebben az esetben az egyes naplók mezőit szóköz választja el egymástól.|
    |TÁROLT SZÖVEGFÁJL HELYE|Az adatok tárolási helye és milyen fájlformátuma.|
    |SELECT|A **t4** oszlopban a **[HIBA]** értéket tartalmazó összes sor számát jelöli ki. Ez a lekérdezés **3** értéket ad vissza, mivel három sor van, amelyek tartalmazzák ezt az értéket.|
    |INPUT__FILE__NAME'%.log' kedvelése|Hive megpróbálja alkalmazni a sémát a könyvtár összes fájljára. Ebben az esetben a könyvtár olyan fájlokat tartalmaz, amelyek nem egyeznek meg a sémával. Az eredményekben lévő szemétadatok elkerülése érdekében ez a nyilatkozat azt jelzi a Hive-nak, hogy csak a .log végződésbe végződő fájlokból kell adatokat visszaadnia.|

   > [!NOTE]  
   > Külső táblákat kell használni, amikor azt várja, hogy az alapul szolgáló adatokat egy külső forrás frissíti. Például egy automatikus adatfeltöltési folyamat vagy egy MapReduce művelet.
   >
   > A külső tábla eldobása **nem** törli az adatokat, csak a tábladefiníciót.

    A parancs kimenete hasonló a következő szöveghez:

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

6. Kilépés a Beeline-ból:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL-fájl futtatása

Ez a folytatása a korábbi példa. A következő lépésekkel hozzon létre egy fájlt, majd futtassa azt a Beeline használatával.

1. A **query.hql**nevű fájl létrehozásához használja a következő parancsot:

    ```bash
    nano query.hql
    ```

1. A fájl tartalmaként a következő szöveget használja. Ez a lekérdezés egy "belső" táblát hoz létre **errorLogs névre:**

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ezek a kijelentések a következő műveleteket követik:

    |Nyilatkozat |Leírás |
    |---|---|
    |TÁBLA LÉTREHOZÁSA, HA NEM LÉTEZIK|Ha a tábla még nem létezik, létrejön. Mivel a **KÜLSŐ** kulcsszó nincs használatban, ez az utasítás belső táblát hoz létre. A belső táblák a Hive adattárházban tárolódnak, és a Hive teljes mértékben kezeli.|
    |TÁROLT ORC|Az adatokat optimalizált soroszlopos (ORC) formátumban tárolja. Az ORC formátum egy rendkívül optimalizált és hatékony formátum a Hive-adatok tárolására.|
    |FELÜLÍRÁS BESZÚRÁSA ... Válassza ki|Kijelöli a **log4jLogs** táblát tartalmazó **sorokat,** majd beszúrja az adatokat az **errorLogs** táblába.|

    > [!NOTE]  
    > A külső táblákkal ellentétben a belső táblák eldobása az alapul szolgáló adatokat is törli.

1. A fájl mentéséhez használja a Ctrl**X** **billentyűt,**+majd írja be az **Y**, és végül írja be az **Enter billentyűt.**

1. A fájl futtatásához futtassa a fájlt a Beeline használatával:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > A `-i` paraméter elindítja a Beeline-t, és futtatja a fájlban lévő `query.hql` állításokat. Miután a lekérdezés befejeződött, `jdbc:hive2://headnodehost:10001/>` meg kell érkeznie a kérdés. A fájlt a `-f` paraméter rel is futtathatja, amely a lekérdezés befejezése után kilép a Beeline programból.

1. Az **errorLogs** tábla létrehozásának ellenőrzéséhez használja a következő utasítást az **errorLogs**összes sorának visszaadására:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Három adatsort kell visszaadni, amelyek mindegyike a t4 oszlopban **[HIBA]** értéket tartalmaz:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a name="install-beeline-client"></a>Beeline kliens telepítése

Bár a Beeline a HDInsight-fürt főcsomópontjain található, érdemes lehet azt egy helyi számítógépre telepíteni.  Az alábbi lépések a Beeline helyi számítógépre való telepítéséhez egy [Linux-alrendszeren alapulnak.](https://docs.microsoft.com/windows/wsl/install-win10)

1. Csomaglisták frissítése. Írja be a következő parancsot a bash shell:

    ```bash
    sudo apt-get update
    ```

1. Telepítse a Java-t, ha nincs telepítve. Ellenőrizheti a `which java` parancsot.

    1. Ha nincs telepítve java csomag, írja be a következő parancsot:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Nyissa meg a bashrc fájlt (általában megtalálható `nano ~/.bashrc`~/.bashrc): .

    1. Módosítsd a bashrc aktát. Adja hozzá a következő sort a fájl végén:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Ezután nyomja **le a Ctrl+X**billentyűkombinációt, majd az **Y**billentyűt, majd írja be a gombot.

1. Letöltés Hadoop és Beeline archívumok, adja meg a következő parancsokat:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Csomagolja ki az archívumot, írja be a következő parancsokat:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. További módosítja a bashrc fájlt. Meg kell határoznia az utat, ahol az archívumot kicsomagolták. Ha a [Windows Alrendszer Linux ,](https://docs.microsoft.com/windows/wsl/install-win10)és követte a lépéseket `/mnt/c/Users/user/`pontosan, az elérési út lenne, hol `user` van a felhasználónevét.

    1. Nyissa meg a fájlt:`nano ~/.bashrc`

    1. Módosítsa az alábbi parancsokat a megfelelő elérési úttal, majd írja be őket a bashrc fájl végén:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Ezután nyomja **le a Ctrl+X**billentyűkombinációt, majd az **Y**billentyűt, majd írja be a gombot.

1. Zárja be, majd nyissa meg újra a bash munkamenetet.

1. Tesztelje a kapcsolatot. Használja a fenti [Nyilvános vagy privát végpontok](#over-public-or-private-endpoints)kapcsolatformátumát.

## <a name="next-steps"></a>További lépések

* A HIVE HDInsight-ban való használatáról az [Apache Hive használata az Apache Hadoop használatával a HDInsight-on című](hdinsight-use-hive.md) témakörben talál további általános tudnivalókat.

* A Hadoop hdinsight-alapú használatának egyéb módjairól a MapReduce használata az [Apache Hadoop használatával a HDInsight-on című](hdinsight-use-mapreduce.md) témakörben talál további információt.
