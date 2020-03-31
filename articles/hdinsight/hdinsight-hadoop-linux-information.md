---
title: Tippek a Hadoop Linux-alapú HDInsight-alapú használatához – Azure
description: Végrehajtási tippeket kaphat a Linux-alapú HDInsight (Hadoop) fürtök azure-felhőben futó ismerős Linux-környezetben való használatához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272460"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Információk a HDInsight Linuxon való használatáról

Az Azure HDInsight-fürtök az Apache Hadoop szolgáltatást egy ismerős Linux-környezetben biztosítják, amely az Azure-felhőben fut. A legtöbb dolog, meg kell dolgozni pontosan úgy, mint bármely más Hadoop-on-Linux telepítés. Ez a dokumentum olyan konkrét különbségeket szólít fel, amelyekről tudnia kell.

## <a name="prerequisites"></a>Előfeltételek

A jelen dokumentum számos lépése a következő segédprogramokat használja, amelyeket esetleg telepíteni kell a rendszerre.

* [cURL](https://curl.haxx.se/) - webalapú szolgáltatásokkal való kommunikációra szolgál.
* **jq**, parancssori JSON processzor.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) – az Azure-szolgáltatások távoli kezeléséhez használható.
* **Egy SSH-ügyfél**. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="users"></a>Felhasználók

Ha csak [a tartományhoz csatlakozott](./domain-joined/hdinsight-security-overview.md), a HDInsight **egyfelhasználós** rendszernek tekintendő. Egyetlen SSH felhasználói fiók jön létre a fürttel, rendszergazdai szintű engedélyekkel. További SSH-fiókok is létrehozhatók, de rendszergazdai hozzáféréssel is rendelkeznek a fürthöz.

A tartományhoz csatlakozó HDInsight több felhasználót és részletesebb engedélyeket és szerepkör-beállításokat támogat. További információt a [Tartományhoz csatlakozott HDInsight-fürtök kezelése című témakörben talál.](./domain-joined/apache-domain-joined-manage.md)

## <a name="domain-names"></a>Tartománynevek

A teljesen minősített tartománynév (FQDN), amelyet akkor kell `CLUSTERNAME.azurehdinsight.net` használni, amikor az internetről csatlakozik a fürthöz, vagy `CLUSTERNAME-ssh.azurehdinsight.net` (csak SSH esetén).

Belsőleg a fürt minden csomópontjának van egy neve, amely a fürtkonfiguráció során van hozzárendelve. A fürtnevek megkereséséhez tekintse meg az Ambari webfelhasználói felület **Ének állomások** lapját. Az alábbi módon is visszaadhat egy állomáslistát az Ambari REST API-ból:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Cserélje le a `CLUSTERNAME` elemet a fürt nevére. Amikor a rendszer kéri, adja meg az admin fiók jelszavát. Ez a parancs egy JSON-dokumentumot ad vissza, amely a fürt állomásainak listáját tartalmazza. [A jq](https://stedolan.github.io/jq/) az `host_name` egyes gazdagép elemek értékének kinyerésére szolgál.

Ha meg kell találnia egy adott szolgáltatás csomópontjának nevét, lekérdezheti az Ambari-t az adott összetevőhöz. Ha például a HDFS-névcsomópont állomásait szeretné megkeresni, használja a következő parancsot:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Ez a parancs egy JSON-dokumentumot ad vissza, amely `host_name` leírja a szolgáltatást, majd [jq](https://stedolan.github.io/jq/) csak az állomások értékét húzza ki.

## <a name="remote-access-to-services"></a>Szolgáltatások távoli elérése

* **Ambari (web)** - `https://CLUSTERNAME.azurehdinsight.net`

    Hitelesítse magát a fürtrendszergazdának használt felhasználóval és jelszóval, majd jelentkezzen be az Ambari ba.

    A hitelesítés egyszerű szöveges - mindig https használatával győződjön meg arról, hogy a kapcsolat biztonságos.

    > [!IMPORTANT]  
    > Az Ambari-hozzáférési csomópontokon keresztül elérhető webes felhasználói felület egy belső tartománynév használatával érhető el. A belső tartománynevek nem érhetők el nyilvánosan az interneten keresztül. Előfordulhat, hogy "a kiszolgáló nem található" hibaüzeneteket kap, amikor bizonyos szolgáltatásokat próbál elérni az interneten keresztül.
    >
    > Az Ambari webes felhasználói felület teljes funkcionalitásának használatához használjon SSH-alagutat a fürtfőcsomópont webes forgalmának proxyproxyhoz. Lásd: [SSH-bújtatás használata az Apache Ambari webes felhasználói felületének, a ResourceManagernek, a JobHistory, a NameNode, az Oozie és más webes felhasználói lehetőségeknek a eléréséhez.](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Hitelesítse magát a fürtrendszergazda felhasználójával és jelszavával.
    >
    > A hitelesítés egyszerű szöveges - mindig https használatával győződjön meg arról, hogy a kapcsolat biztonságos.

* **WebHCat (Templom)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Hitelesítse magát a fürtrendszergazda felhasználójával és jelszavával.
    >
    > A hitelesítés egyszerű szöveges - mindig https használatával győződjön meg arról, hogy a kapcsolat biztonságos.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net a 22-es vagy 23-as porton. A 22-es port az elsődleges csomóponthoz való csatlakozásra szolgál, míg a 23-as a másodlagoshoz való csatlakozásra szolgál. A fő csomópontokról további információt az [Apache Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsightban című témakörben talál.](hdinsight-high-availability-linux.md)

    > [!NOTE]  
    > A fürtfej-csomópontok csak az SSH-n keresztül érhetők el egy ügyfélgépről. Miután csatlakozott, ezután elérheti a munkavégző csomópontok segítségével SSH egy headnode használatával.

További információ: Az [Apache Hadoop-szolgáltatások által a HDInsight-dokumentumon használt portok.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Fájlhelyek

A Hadoop-fájlokkal kapcsolatos fájlok a fürtcsomópontokon találhatók a(z) oldalon. `/usr/hdp` Ez a könyvtár a következő alkönyvtárakat tartalmazza:

* **2.6.5.3009-43**: A könyvtár neve a HDInsight által használt Hadoop platform verziója. A fürtön lévő szám eltérhet az itt felsoroltaktól.
* **aktuális**: Ez a könyvtár a **2.6.5.3009-43** könyvtár alkönyvtáraihoz mutató hivatkozásokat tartalmaz. Ez a könyvtár azért létezik, hogy ne kelljen megjegyeznie a verziószámot.

Példa adatok és JAR fájlok találhatók Hadoop elosztott fájlrendszer `/example` és `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage és Data Lake Storage

A legtöbb Hadoop-disztribúcióban az adatok a HDFS-ben tárolódnak, amelyet a fürtben lévő gépeken lévő helyi tároló támogat. A helyi tárhely használata költséges lehet egy felhőalapú megoldás, ahol a számítási erőforrások óránként vagy percben kell fizetnie.

A HDInsight használatakor az adatfájlok méretezhető és rugalmas módon tárolódnak a felhőben az Azure Blob Storage és opcionálisan az Azure Data Lake Storage használatával. Ezek a szolgáltatások a következő előnyöket biztosítják:

* Olcsó hosszú távú tárolás.
* Külső szolgáltatásokból, például webhelyekről, fájlfeltöltési/-letöltési segédprogramokból, különböző nyelvi SDK-kból és webböngészőkből való hozzáférhetőség.
* Nagy fájlkapacitás és nagy méretezhető tároló.

További információ: A blobok és a [Data Lake Storage ismertetése.](https://azure.microsoft.com/services/storage/data-lake-storage/) [Understanding blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

Az Azure Storage vagy a Data Lake Storage használatakor nem kell semmi különlegeset tennie a HDInsightból az adatok eléréséhez. A következő parancs például felsorolja `/example/data` a mappában lévő fájlokat, függetlenül attól, hogy az Azure Storage vagy a Data Lake Storage tárolja-e őket:

    hdfs dfs -ls /example/data

A HDInsightban az adattárolási erőforrások (Az Azure Blob Storage és az Azure Data Lake Storage) levannak választva a számítási erőforrásoktól. Ezért hdinsight-fürtöket hozhat létre, hogy szükség szerint végezhesse a számításokat, és később törölheti a fürtöt, amikor a munka befejeződött, miközben az adatfájlok at biztonságosan megőrzik a felhőbeli tárolóban, ameddig csak szüksége van.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI és séma

Egyes parancsok esetében előfordulhat, hogy a sémát az URI részeként kell megadni a fájl elérésekor. A Storm-HDFS összetevő például meg kell adnia a sémát. Ha nem alapértelmezett tárolót (a fürthöz "kiegészítő" tárolóként hozzáadott tárolót) használ, mindig az URI részeként kell használnia a sémát.

Az [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md)használatakor használja az alábbi URI-sémák egyikét:

* `wasb:///`: Az alapértelmezett tároló elérése titkosítatlan kommunikációval.

* `wasbs:///`: Hozzáférés az alapértelmezett tárolóhoz titkosított kommunikációval.  A wasbs séma csak a HDInsight 3.6-os verziójától támogatott.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Nem alapértelmezett tárfiókkal való kommunikációhoz használatos. Például ha egy további tárfiókkal rendelkezik, vagy nyilvánosan elérhető tárfiókban tárolt adatok elérésekor.

Az [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)használataesetén használja a következő URI-sémát:

* `abfs://`: Hozzáférés az alapértelmezett tárolóhoz titkosított kommunikációval.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Nem alapértelmezett tárfiókkal való kommunikációhoz használatos. Például ha egy további tárfiókkal rendelkezik, vagy nyilvánosan elérhető tárfiókban tárolt adatok elérésekor.

Az [**Azure Data Lake Storage Gen1**](./hdinsight-hadoop-use-data-lake-store.md)használataesetén használja az alábbi URI-sémák egyikét:

* `adl:///`: A fürt alapértelmezett Data Lake Storage tárolójának elérése.

* `adl://<storage-name>.azuredatalakestore.net/`: Nem alapértelmezett Data Lake Storage szolgáltatással való kommunikációsorán használatos. A HDInsight-fürt gyökérkönyvtárán kívüli adatok elérésére is használható.

> [!IMPORTANT]  
> Ha a Data Lake Storage-t használja a HDInsight alapértelmezett tárolójaként, meg kell adnia egy elérési utat az áruházon belül, amelyet a HDInsight-tároló gyökereként használhat. Az alapértelmezett `/clusters/<cluster-name>/`elérési út a .
>
> Adatok `/` használatakor vagy `adl:///` eléréséhez csak a fürt gyökérben (például `/clusters/<cluster-name>/`) tárolt adatokhoz férhet hozzá. Ha az áruház bármely pontján `adl://<storage-name>.azuredatalakestore.net/` szeretne hozzáférni az adatokhoz, használja a formátumot.

### <a name="what-storage-is-the-cluster-using"></a>Milyen tárolót használ a fürt?

Az Ambari segítségével lekérheti a fürt alapértelmezett tárolási konfigurációját. A következő paranccsal a HDFS konfigurációs adatait a curl használatával lekérheti, majd [jq](https://stedolan.github.io/jq/)használatával szűrheti:

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Ez a parancs az adatokat tartalmazó`service_config_version=1`kiszolgálóra ( ) alkalmazott első konfigurációt adja vissza. Előfordulhat, hogy a legújabb verzió megkereséséhez az összes konfigurációs verziót fel kell sorolnia.

Ez a parancs a következő URI-khoz hasonló értéket ad vissza:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`ha egy Azure Storage-fiókot használ.

    A fiók neve az Azure Storage-fiók neve. A tároló neve a blob tároló, amely a fürttároló gyökere.

* `adl://home`ha az Azure Data Lake Storage-t használja. A Data Lake Storage nevének lekérnie, használja a következő REST-hívást:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Ez a parancs a `<data-lake-store-account-name>.azuredatalakestore.net`következő állomásnevet adja vissza: .

    A HDInsight gyökérkönyvtárát tartalmazó könyvtár lehívásához használja a következő REST-hívást:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Ez a parancs a következő elérési `/clusters/<hdinsight-cluster-name>/`úthoz hasonló elérési utat ad vissza: .

A tárolási adatokat az Azure Portalon is megtalálhatja az alábbi lépések segítségével:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a HDInsight-fürt.

2. A **Tulajdonságok csoportban** válassza **a Tárfiókok lehetőséget.** Megjelennek a fürt tárolási adatai.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Hogyan érhetem el a HDInsighton kívüli fájlokat?

A HDInsight-fürtön kívülről származó adatok elérésének számos módja van. Az alábbiakban néhány olyan segédprogramra és SDK-ra mutató hivatkozás tanéven látható, amely az adatokkal való együttműködéshez használható:

Az __Azure Storage__használata esetén tekintse meg az alábbi hivatkozásokat az adatok elérésének módjairól:

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2): Parancssori felület parancsok az Azure-ral való munkához. A telepítés után `az storage` használja a parancsot a `az storage blob` storage használatával kapcsolatos segítségért, vagy a blob-specifikus parancsokhoz.
* [blobxfer.py:](https://github.com/Azure/blobxfer)Az Azure Storage blobok használatával végzett munka python parancsfájl.
* Különböző SDK-k:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [Php](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Tárolási REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Az __Azure Data Lake Storage__használata esetén tekintse meg az alábbi hivatkozásokat az adatok elérésének módjairól:

* [Webböngésző](../data-lake-store/data-lake-store-get-started-portal.md)
* [Powershell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake eszközök a Visual Studio számára](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>A fürt méretezése

A fürtméretezési szolgáltatás lehetővé teszi a fürt által használt adatcsomópontok számának dinamikus módosítását. Skálázási műveleteket hajthat végre, miközben más feladatok vagy folyamatok egy fürtön futnak.  Lásd még: [HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md)

A különböző fürttípusokat a méretezés az alábbiak szerint érinti:

* **Hadoop**: A fürt csomópontjainak leskálázásakor a fürt egyes szolgáltatásai újraindulnak. A skálázási műveletek a méretezési művelet befejezésekor a futó vagy függőben lévő feladatok sikertelensül okozhatnak. A művelet befejezése után újra elküldheti a feladatokat.
* **HBase**: A regionális kiszolgálók automatikusan kivannak egyensúlyban néhány percen belül, amint a skálázási művelet befejeződik. A regionális kiszolgálók manuális kiegyensúlyozásához kövesse az alábbi lépéseket:

    1. Csatlakozzon a HDInsight-fürthöz az SSH használatával. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md)

    2. A HBase rendszerhéj elindításához használja az alábbiakat:

            hbase shell

    3. A HBase rendszerhéj betöltése után a következőkkel manuálisan egyensúlyozhassa ki a regionális kiszolgálókat:

            balancer

* **Vihar:** A méretezési művelet végrehajtása után újra kell egyensúlyoznia a futó Storm-topológiákat. Az újraegyensúlyozás lehetővé teszi, hogy a topológia a fürt új csomópontjainak új száma alapján újraértelmezi a párhuzamossági beállításokat. A futó topológiák újraegyensúlyozásához használja az alábbi lehetőségek egyikét:

    * **SSH**: Csatlakozzon a kiszolgálóhoz, és a következő paranccsal egyensúlyba hozza a topológiát:

            storm rebalance TOPOLOGYNAME

        Paramétereket is megadhat a topológia által eredetileg biztosított párhuzamossági tippek felülbírálásához. Például `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` újrakonfigurálja a topológiát 5 munkavégző folyamatra, 3 végrehajtóra a kék-kifolyó összetevőhöz és 10 végrehajtóra a sárgacsavar-összetevőhöz.

    * **Storm felhasználói felület:** A következő lépésekkel egyensúlyba hozva a topológia a Storm felhasználói felület használatával.

        1. Nyissa `https://CLUSTERNAME.azurehdinsight.net/stormui` meg a böngészőben, hol `CLUSTERNAME` található a Storm-fürt neve. Ha a rendszer kéri, adja meg a HDInsight-fürt rendszergazdájának (rendszergazdai) nevét és jelszavát, amelyet a fürt létrehozásakor megadott.
        2. Jelölje ki az egyensúlyhelyreállítására kívánt topológiát, majd válassza az **Egyensúly helyreállítása** gombot. Adja meg a késleltetést az újraegyensúlyozási művelet végrehajtása előtt.

* **Kafka**: A méretezési műveletek után újra kell egyensúlyoznia a partícióreplikákat. További információ: [Az Apache Kafka által a HDInsight-dokumentumon az adatok magas rendelkezésre állású](./kafka/apache-kafka-high-availability.md) száma.

A HDInsight-fürt méretezésével kapcsolatos konkrét tudnivalókat a következő témakörben talál:

* [Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portal használatával](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Apache Hadoop-fürtök kezelése a HDInsightban az Azure CLI használatával](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Hogyan telepíthetem a Hue-t (vagy más Hadoop-összetevőt)?

A HDInsight egy felügyelt szolgáltatás. Ha az Azure problémát észlel a fürttel kapcsolatban, törölheti a meghibásodott csomópontot, és létrehozhat egy csomópontot a helyére. Ha manuálisan telepíti a dolgokat a fürtön, azok nem maradnak meg a művelet során. Ehelyett használja a [HDInsight parancsfájlműveleteket.](hdinsight-hadoop-customize-cluster-linux.md) A parancsfájl-művelet a következő módosításokat hajthatja végre:

* Szolgáltatás vagy webhely telepítése és konfigurálása.
* Olyan összetevő telepítése és konfigurálása, amely konfigurációs módosításokat igényel a fürt több csomópontján.

A parancsfájlműveletek Bash-parancsfájlok. A parancsfájlok a fürt létrehozása során futnak, és további összetevők telepítésére és konfigurálására szolgálnak. Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### <a name="jar-files"></a>Jar fájlok

Egyes Hadoop-technológiák önálló jar fájlokban találhatók, amelyek a MapReduce feladat részeként használt funkciókat tartalmaznak, vagy a Pig vagy a Hive belsejéből. Gyakran nem igényelnek semmilyen beállítást, és a létrehozás után feltölthetők a fürtbe, és közvetlenül használhatók. Ha meg szeretne győződni arról, hogy az összetevő túléli a fürt újraimagingját, tárolhatja a jar fájlt a fürt alapértelmezett tárolójában (WASB vagy ADL).

Ha például az [Apache DataFu](https://datafu.incubator.apache.org/)legújabb verzióját szeretné használni, letöltheti a projektet tartalmazó üveget, és feltöltheti a HDInsight-fürtbe. Ezután kövesse a DataFu dokumentációt, hogyan kell használni a Pig vagy a Hive.

> [!IMPORTANT]  
> Egyes önálló jar fájlokat tartalmazó összetevők hdinsight-el vannak ellátva, de nem szerepelnek az elérési úton. Ha egy adott összetevőt keres, az alábbiak segítségével keresheti meg a fürtön:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Ez a parancs a megfelelő jar fájlok elérési útját adja vissza.

Egy összetevő egy másik verziójának használatához töltse fel a szükséges verziót, és használja azt a feladatokban.

> [!IMPORTANT]
> A HDInsight-fürthöz tartozó összetevők teljes mértékben támogatottak, és a Microsoft támogatási szolgálata segít az összetevőkkel kapcsolatos problémák elkülönítésében és megoldásában.
>
> Az egyéni összetevők üzletileg ésszerű támogatást kapnak a probléma további elhárításához. Ez a probléma megoldásához vezethet, vagy megkéri, hogy vegyen részt a nyílt forráskódú technológiák elérhető csatornáiban, ahol az adott technológiával kapcsolatos mély szakértelem található. Például számos közösségi webhely használható, például: [MSDN fórum a HDInsighthoz](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Szintén Apache projektek projekt [https://apache.org](https://apache.org)oldalak , például: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari REST API használatával](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [MapReduce-feladatok használata a HDInsightban](hadoop/hdinsight-use-mapreduce.md)
