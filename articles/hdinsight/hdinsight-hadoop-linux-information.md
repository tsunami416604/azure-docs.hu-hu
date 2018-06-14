---
title: Hadoop Linux-alapú hdinsight - Azure tippek |} Microsoft Docs
description: Megvalósítási tippek a Linux-alapú HDInsight (Hadoop) fürtök használata az Azure felhőben futó ismerős Linux-környezetben.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 3ad7aa01200bf2bf4a63a380b2b883983c8622d6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405391"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Információk a HDInsight Linuxon való használatáról

Az Azure HDInsight-fürtök ismerős Linux környezetben, az Azure felhőben futó Hadoop szolgálnak. A legtöbb feladat akkor működnek, pontosan a másik Hadoop a Linux-telepítés. Ez a dokumentum meghívja a kimenő, meg kell ismernie a speciális eltéréseket.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Előfeltételek

A jelen dokumentumban leírt lépések számos használja az alábbi segédprogramokat, és előfordulhat, hogy telepítve kell lennie a rendszeren.

* [cURL](https://curl.haxx.se/) - web-alapú szolgáltatásokkal történő kommunikációhoz használt
* [jq](https://stedolan.github.io/jq/) - használt elemzése JSON-dokumentumok
* [Az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) - használt távoli kezelése az Azure-szolgáltatások

## <a name="users"></a>Felhasználók

Ha [tartományhoz](./domain-joined/apache-domain-joined-introduction.md), érdemes figyelembe venni a HDInsight egy **egyfelhasználós** rendszer. Egy SSH-felhasználói fiókot a fürt rendszergazdai szintű engedélyekkel rendelkező hozza létre. További SSH-fiókok hozhatók létre, de a fürt rendszergazdai hozzáféréssel is rendelkeznek.

Tartományhoz csatlakozó HDInsight támogatja egyszerre több felhasználó és részletesebb engedéllyel és szerepkör-beállítások. További információkért lásd: [kezelése tartományhoz a HDInsight-fürtök](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Tartománynevek

A teljes tartománynevét (FQDN) az internetről a fürthöz történő csatlakozás során használandó van  **&lt;clustername >. azurehdinsight.net** vagy (az SSH csak)  **&lt;fürtnév-ssh >. azurehdinsight.NET**.

Belső a fürt minden csomópontja rendelkezik a nevet, amely hozzá van rendelve, fürt konfigurálása során. A fürt nevének megkereséséhez lásd: a **állomások** oldalon, az Ambari webes felhasználói felületén. Az Ambari REST API-t az állomások listájához való visszatéréshez is használhatja a következő:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Cserélje le a **CLUSTERNAME** elemet a fürt nevére. Amikor a rendszer kéri, adja meg a jelszót a rendszergazdai fiókot. Ez a parancs visszaadja a JSON-dokumentumában, amely a fürt állomásai között listáját tartalmazza. Jq használatos kiolvasni a `host_name` elemérték minden állomás számára.

Ha egy adott szolgáltatás található a csomópont neve van szüksége, az adott összetevő lekérheti Ambari. Például a gazdagépek a HDFS neve csomópont megkereséséhez, használja a következő parancsot:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Ez a parancs visszaadja a szolgáltatás leíró JSON-dokumentum, és ezután jq kéri le csak a `host_name` érték a gazdagépek számára.

## <a name="remote-access-to-services"></a>Szolgáltatásokhoz való távoli hozzáférés

* **Ambari (webalkalmazás)** -https://&lt;clustername >. azurehdinsight.net

    A fürt rendszergazdai felhasználói és jelszó használatával hitelesíteni, és az Ambari, majd jelentkezzen be.

    Hitelesítés egyszerű szövegként – mindig HTTPS PROTOKOLLT használnak a kapcsolat biztonságának garantálása érdekében.

    > [!IMPORTANT]
    > A web UI Ambari keresztül elérhető némelyike érhető el a csomópontok egy belső tartománynév használatával. Tartománynevek belső nincsenek nyilvánosan elérhető az interneten keresztül. "A kiszolgáló nem található" hibák jelenhet meg, amikor megpróbál hozzáférni az egyes szolgáltatások az interneten keresztül.
    >
    > Az Ambari webes felhasználói felület összes funkciójának használatához az SSH-alagút az átjárócsomóponthoz proxy webes forgalom számára. Lásd: [használata SSH Tunneling Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie és egyéb web UI eléréséhez](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** -https://&lt;clustername >.azurehdinsight.net/ambari

    > [!NOTE]
    > A fürt rendszergazdai felhasználói és jelszó használatával hitelesíteni.
    >
    > Hitelesítés egyszerű szövegként – mindig HTTPS PROTOKOLLT használnak a kapcsolat biztonságának garantálása érdekében.

* **WebHCat (Templeton)** -https://&lt;clustername >.azurehdinsight.net/templeton

    > [!NOTE]
    > A fürt rendszergazdai felhasználói és jelszó használatával hitelesíteni.
    >
    > Hitelesítés egyszerű szövegként – mindig HTTPS PROTOKOLLT használnak a kapcsolat biztonságának garantálása érdekében.

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net 22-es vagy 23-porton. Az elsődleges headnode csatlakozni, miközben a másodlagos való csatlakozáshoz használt 23 22-es portot szolgál. Az átjárócsomópontokkal kapcsolatos további információkat lásd: [Availability and reliability of Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (A Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsightban).

    > [!NOTE]
    > A fürt átjárócsomópontokkal SSH-n keresztül ügyfélgépről csak elérni. Miután csatlakozott, majd hozzáférhet a feldolgozó csomópontok egy headnode SSH használatával.

További információkért lásd: a [a HDInsight Hadoop-szolgáltatás által használt portok](hdinsight-hadoop-port-settings-for-services.md) dokumentum.

## <a name="file-locations"></a>Fájlhelyek

Hadoop kapcsolatos fájlok találhatók a fürtcsomópontokon, `/usr/hdp`. Ez a könyvtár a következő alkönyvtárakat tartalmaz:

* **2.2.4.9-1**: A könyvtár neve a Hortonworks Data Platform HDInsight által használt verziója telepítve. Lehet, hogy a fürt hány eltérnek a listán.
* **aktuális**: Ez a könyvtár alatti alkönyvtárak mutató hivatkozásokat tartalmaz a **2.2.4.9-1** könyvtár. Ez a könyvtár létezik, így nem kell jegyezze meg a verziószámot.

Példa adatok és a JAR-fájlok megtalálhatók a Hadoop elosztott fájlrendszer, `/example` és `/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-store"></a>HDFS, az Azure Storage és a Data Lake Store

A legtöbb Hadoop azokat a terjesztéseket HDFS biztonsági helyi tárhelyet a fürt a gépen. A helyi tároló költséges lehet egy felhőalapú megoldás hol van szó, óránként vagy számítási erőforrások percenként.

HDInsight az Azure Storage blobs, vagy az Azure Data Lake Store használja az alapértelmezett tárolójába. Ezek a szolgáltatások a következő előnyöket biztosítják:

* Olcsó hosszú távú tárolás
* Kisegítő lehetőségek külső szolgáltatásokból, például a webhelyek, a fájl feltöltése/letöltés segédprogramok, a különböző nyelvi SDK-k és a böngészők

Egy Azure Storage-fiók tárolható 4.75 TB, bár egyes blobok (vagy a HDInsight szempontból fájl) csak lépjen 195 GB. Azure Data Lake Store dinamikusan növelhető trillions fájlok, az egyes nagyobb, mint egy petabájtnyi fájlok tárolásához. További információkért lásd: [ismertetése blobok](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) és [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Azure Storage vagy a Data Lake Store használata esetén nincs tennivalója különleges a HDInsight adatok eléréséhez. Például a következő parancs megjeleníti a fájlok a `/example/data` függetlenül attól, hogy tárolása Azure Storage vagy a Data Lake Store mappába:

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>URI és a rendszer

Néhány parancsok igényelheti paraméter megadásakor a rendszer az URI részeként a fájl elérése közben. Például a Storm-HDFS összetevő szükséges, hogy adja meg a rendszer. Ha a nem alapértelmezett tároló ("További" tárolóként a fürthöz hozzáadott tároló), mindig kell használni a rendszer az URI részeként.

Használata esetén __Azure Storage__, használja a következő URI-rendszerek egyikét:

* `wasb:///`: A hozzáférés alapértelmezett tárolási titkosítatlan kommunikáció használata.

* `wasbs:///`: A hozzáférés alapértelmezett tárolási titkosított kommunikáció használata.  A wasbs séma csak a HDInsight 3.6 verzió és újabb verziók esetében támogatott.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: A nem alapértelmezett tárfiók való kommunikáció során használt. Például, ha rendelkezik egy további storage-fiókot, vagy amikor tárolt adatok elérése a nyilvánosan elérhető tárfiók.

Használata esetén __Data Lake Store__, használja a következő URI-rendszerek egyikét:

* `adl:///`: Az alapértelmezett Data Lake Store a fürt eléréséhez.

* `adl://<storage-name>.azuredatalakestore.net/`: Egy nem alapértelmezett Data Lake Store való kommunikáció során használt. A HDInsight-fürt gyökérkönyvtárán kívül adatok eléréséhez is használt.

> [!IMPORTANT]
> Ha a Data Lake Store-t a HDInsight az alapértelmezett tároló, meg kell adnia egy elérési utat a HDInsight-tárolóba gyökereként használja a tárolóban. Az alapértelmezett elérési út `/clusters/<cluster-name>/`.
>
> Használata esetén `/` vagy `adl:///` adatok eléréséhez csak hozzáférhet a legfelső szintű tárolt adatokat (például `/clusters/<cluster-name>/`) a fürt. Bárhol a tárolóban lévő adatok eléréséhez használja a `adl://<storage-name>.azuredatalakestore.net/` formátumban.

### <a name="what-storage-is-the-cluster-using"></a>Milyen tárolási a fürt használ

Ambari segítségével a fürt alapértelmezett tároló konfigurációjának beolvasása. A következő paranccsal lekérni használata curl használatával HDFS konfigurációs adatokat, és szűréséhez használatával [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> Ez a parancs visszaadja az első konfiguráció, a kiszolgálón alkalmazott (`service_config_version=1`), amely tartalmazza ezt az információt. Szükség lehet a buildszám található összes konfigurációs verziójának felsorolása.

Ez a parancs értéket ad vissza a következő URI-k hasonlít:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Ha egy Azure Storage-fiók használatával.

    A fiók neve az Azure Storage-fiók nevét. A tároló neve a blob-tároló, amely a fürttároló gyökere.

* `adl://home` Ha az Azure Data Lake Store használatára. A Data Lake Store nevét, amelyet a következő REST-hívást:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    Ez a parancs visszaadja a következő állomásnevét: `<data-lake-store-account-name>.azuredatalakestore.net`.

    A könyvtár a tárolóban, amely a HDInsight a legfelső szintű megtekintéséhez használja a következő REST-hívást:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    Ez a parancs visszaadja egy elérési utat a következő elérési utat hasonló: `/clusters/<hdinsight-cluster-name>/`.

Az Azure portál használatával a következő lépések segítségével tárolással kapcsolatos is található:

1. Az a [Azure-portálon](https://portal.azure.com/), válassza ki a HDInsight-fürthöz.

2. Az a **tulajdonságok** szakaszban jelölje be **Tárfiókok**. A fürt a tárolási információk jelennek meg.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hogyan érhetem el fájlokat a külső HDInsight-ból

A HDInsight-fürtön kívüli adatok eléréséhez különböző módja van. Az alábbiakban néhány hivatkozások segédprogramok és SDK-k, amelyek segítségével az adatok kezeléséhez:

Ha használ __Azure Storage__, tekintse meg a következő hivatkozások módon, hogy az adatok végezheti el:

* [Az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): használata az Azure parancssori felület parancsai. Telepítés után használja a `az storage` parancsot a Súgó a tárolót, vagy `az storage blob` a blob-specifikus parancsok.
* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): A python parancsfájl az Azure Storage blobs használata a.
* Különböző SDK-k:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Ha használ __Azure Data Lake Store__, tekintse meg a következő hivatkozások módon, hogy az adatok végezheti el:

* [Webböngésző](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI 2.0](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API-n](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [A Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>A fürt méretezése

A fürt skálázás funkció lehetővé teszi, hogy dinamikusan módosíthatja a fürt által használt adatok csomópontok száma. Méretezési műveleteket közben egyéb feladatokat hajthat végre, vagy egy fürtön folyamatok futnak.

A különböző fürttípussal érhető érinti skálázás az alábbiak szerint:

* **Hadoop**: skálázás le a fürtben található csomópontok számát, ha egyes szolgáltatások a fürt újraindításakor. Műveletek skálázás feladatok eredményezheti fut, vagy függőben lévő meghiúsulhatnak, a méretezési művelet befejezését. Akkor is küldje el újra a feladatok a művelet végrehajtása után.
* **A HBase**: területi kiszolgálók automatikusan elosztott terhelésű, a méretezési művelet befejeződése után néhány percen belül. Manuálisan egyensúlyba területi kiszolgálók, tegye a következőket:

    1. Csatlakozzon a HDInsight-fürthöz SSH használatával. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. A HBase rendszerhéj elindításához használja a következőket:

            hbase shell

    3. Amennyiben az a HBase rendszerhéjból be van töltve, használja a regionális kiszolgálók manuálisan elosztása érdekében a következőket:

            balancer

* **A Storm**: minden futó Storm-topológiák kell egyensúlyba, a méretezési művelet végrehajtását követően. Újraelosztás lehetővé teszi a topológia állítsa be újra a párhuzamos végrehajtás beállításokat a fürtben található csomópontok új száma alapján. Visszaegyensúlyozás futó topológiákat, használja az alábbi lehetőségek közül:

    * **SSH**: Csatlakozás a kiszolgálóhoz, és a következő paranccsal egy topológia egyensúlyba:

            storm rebalance TOPOLOGYNAME

        Bírálja felül a eredetileg biztosítja a topológia párhuzamosságát mutatók paramétereket is megadható. Például `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` újrakonfigurálja a topológia 5 munkavégző folyamatok, a kék-spout összetevő 3 végrehajtója és a sárga-bolt összetevő 10 végrehajtója.

    * **Storm UI**: a következő lépésekkel egyensúlyba a topológia a Storm felhasználói felületén.

        1. Nyissa meg **https://CLUSTERNAME.azurehdinsight.net/stormui** a böngészőben, ahol CLUSTERNAME-e a Storm-fürt nevét. Ha a rendszer kéri, írja be a HDInsight fürt Fürtrendszergazda (rendszergazda) nevét és jelszavát, a fürt létrehozásakor megadott.
        2. Válassza ki a topológiát kíván egyensúlyba, majd válassza ki a **egyensúlyba** gombra. Adja meg a késleltetés, a egyensúlyozza ki újra a művelet végrehajtása előtt.

* **Kafka**: skálázás műveletek után kell egyensúlyba a partíció replikákat. További információkért lásd: a [magas rendelkezésre álláshoz az adatok a HDInsight Kafka](./kafka/apache-kafka-high-availability.md) dokumentum.

A HDInsight-fürt méretezése a részletes információkat lásd:

* [Hdinsight Hadoop-fürtök kezelése az Azure-portál használatával](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hdinsight Hadoop-fürtök kezelése az Azure PowerShell használatával](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hogyan kell telepíteni a Hue (vagy más Hadoop-összetevők)?

HDInsight egy olyan felügyelt szolgáltatás. Ha Azure a fürt problémát észlel, akkor előfordulhat, hogy törli a hibás csomópontot, és lecseréli csomópont létrehozása. Ha manuálisan telepíti a fürtön dolog, azokat nem maradnak meg a művelet esetén. Ehelyett használjon [HDInsight Parancsfájlműveletek](hdinsight-hadoop-customize-cluster.md). A parancsfájlművelet segítségével a következő módosításokat:

* Telepítsen és konfiguráljon egy szolgáltatás vagy webhelyre.
* Telepítsen és konfiguráljon a fürt több csomópontja konfigurációs módosítások igénylő összetevők.

A Parancsfájlműveletek olyan Bash parancsfájlok. A parancsfájlok futtathatók a fürt létrehozása során, és telepítéséről és konfigurálásáról további összetevőket használják. A következő összetevők telepítésének parancsfájlpéldákat biztosítja:

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### <a name="jar-files"></a>JAR-fájlok

Néhány Hadoop technológiák részeként MapReduce feladatot, vagy a használt funkciók önálló jar-fájlok szerepelnek a Pig- vagy Hive belül. Azok gyakran nem szükséges a telepítés, és a fürt létrehozása után feltöltött és használt közvetlenül. Ha azt szeretné, győződjön meg arról, hogy az összetevő survives, különösen a fürt számára, a fürt (WASB vagy ADL) tárolhatja a jar-fájlt az alapértelmezett tároló.

Például, ha szeretné használni a legújabb [DataFu](http://datafu.incubator.apache.org/), töltse le a a projektet tartalmazó jar, és töltse fel a HDInsight-fürthöz. Kövesse a DataFu dokumentáció a Pig- vagy Hive használatával.

> [!IMPORTANT]
> Néhány összetevőt, amelyek az önálló jar-fájlok a HDInsight által biztosított, de nem szerepelnek az elérési út. Ha egy adott összetevőre keres, a következő segítségével keresse meg azt a fürt:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> A parancs minden egyező jar-fájlok elérési útját adja vissza.

Egy összetevő egy másik verzióját használja, töltse fel a verziót kell, és a feladatok használatát.

> [!WARNING]
> A HDInsight-fürt összetevői teljes mértékben támogatottak, és Microsoft Support segít elkülöníteni, és ezeket az összetevőket kapcsolatos problémák megoldásához.
>
> Egyéni összetevők kapnak minden üzleti szempontból ésszerű támogatási segítséget nyújtanak a probléma további hibaelhárításához. A probléma megoldását, vagy kéri fel, a nyílt forráskódú technológiák, ahol a részletes segítséget, hogy a technológiát található elérhető csatorna végezhetnek eredményezhet. Például nincsenek sok közösségi webhelyek használható, például: [MSDN fórum hdinsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Is Apache projektek rendelkezik projekt helyek [ http://apache.org ](http://apache.org), például: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>További lépések

* [Linux-alapú Windows-alapú HDInsight át](hdinsight-migrate-from-windows-to-linux.md)
* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hadoop/hdinsight-use-mapreduce.md)
