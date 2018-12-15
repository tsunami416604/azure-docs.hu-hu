---
title: Az Azure HDInsight archivált kibocsátási megjegyzései
description: Archivált kibocsátási megjegyzései és verziói az Azure HDInsight.
services: hdinsight
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: hrasheed
ms.openlocfilehash: 43acf5f59667f45e67156b33e31d28160ba47c13
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408808"
---
# <a name="archived-release-notes-for-azure-hdinsight"></a>Az Azure HDInsight archivált kibocsátási megjegyzései

Az a **legutóbbi** Azure HDInsight kibocsátási frissítések, lásd: [HDInsight kibocsátási megjegyzéseit](hdinsight-release-notes.md).

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight versioning cikk](hdinsight-component-versioning.md).

## <a name="notes-for-06272018---release-of-new-open-source-versions-adls-gen2-etc-on-hdinsight-36"></a>Megjegyzések a 06/27 vagy 2018 - új nyílt forráskódú verziók megjelenésével, a HDInsight 3.6-os ADLS Gen2 stb.
Az új frissítések és funkciók ügyfeleink számára jelentős HDInsight 2018 június kiadása nem. Olvassa el ezt [közzététele](https://azure.microsoft.com/blog/enterprises-get-deeper-insights-with-hadoop-and-spark-updates-on-azure-hdinsight/) további részletekért.

Az alábbiakban a fontosabb eseményeket. A részletes kibocsátási megjegyzések javított, az ismert problémákról, stb., olvassa el a [Azure HDInsight kibocsátási megjegyzései](hdinsight-release-notes.md).

- **Frissítse az Apache Hadoop- és más nyílt forráskódú projektek** – mellett 1000 + több mint 20 nyílt forráskódú projekten hibajavításokat tartalmaz, a frissítés egy új verziója (2.3-as) az Apache Spark és Apache Kafka (1.0) tartalmazza.
- **Frissítse az R Server 9.1 Machine Learning szolgáltatás 9.3** – ezzel a kiadással adjuk az adatszakértők és a mérnökök a nyílt forráskódú, kiegészítve az algoritmikus innovációkat, és a könnyű operacionalizálás, az összes elérhető legjobb azok elsődleges nyelv, az Apache Spark sebessége. Ebben a kiadásban érhető el az R Server-támogatás hozzáadva a Python, R Server ML szolgáltatásokkal, a fürt névváltoztatás vezető képességeitől bontja ki. 
- **Az Azure Data Lake Storage Gen2 támogatása** – HDInsight támogatja az Azure Data Lake Storage Gen2 előzetes kiadását. Az elérhető régiók, az ügyfelek fogja tudni ADLS Gen2 fiók kiválasztása a HDInsight-fürtök tárolóként.
- **HDInsight vállalati biztonsági csomag frissítések (előzetes verzió)** – virtuális hálózati Szolgáltatásvégpontok (előzetes verzió) támogatása az Azure blob Storage, az ADLS Gen1, Cosmos DB és az Azure DB. 

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Megjegyzések 03/20/2018 - a Spark 2.2-es on HDInsight 3.6-os kiadás

- Spark 2.2.0 stabilitását javítja a különböző Spark mag, SQL, a gépi tanulás, és számos lehetőséget kínál a strukturált Streamelés a végleges verzió állapota. Spark 2.2.0 már elérhető a HDInsight 3.6-ot.


## <a name="notes-for-08012017-release-of-hdinsight"></a>HDInsight kiadása 08/01/2017 megjegyzései

| Beosztás | Leírás | Érintett terület  | Fürt típusa  | 
| --- | --- | --- | --- | --- |
| A HDInsight, az a Microsoft R Server 9.1 kiadása |HDInsight R Server 9.1 kiépítési fürtök mostantól támogatja a HDInsight. A Microsoft R Server 9.1-kiadás további információkért lásd: [ebben a blogbejegyzésben](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Szolgáltatás |R Server |
| HDInsight 3.6-os mostantól tartalmazza a Hadoop-veremmel újabb verzióiban|<ul><li>Frissített verzió részletes listáját lásd: [HDInsight elérhető az Apache Hadoop összetevő verziók](hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).</li><li>A Hadoop-veremmel legújabb verziójában javított hibák listáját lásd: [Apache javítás információk](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Parancsban történt használhatatlanná tévő HDP 2.6.1 (amely már elérhető a HDInsight 3.6) közötti listáját lásd: [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>Ismert problémák a HDP 2.6.1 listáját lásd: [ismert problémák](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Szolgáltatás |Összes |– |
| Frissítések-fürtök interaktív Hive (előzetes verzió) |<ul><li><b>A szolgáltatás javítására.</b> Gyorsítótárazott metaadattár SQL háttérbeli a metaadatok gyorsítótárazásával csökkenti, és növeli a teljesítményt az összes metaadat-műveletek végrehajtása.  Ennek a fejlesztésnek már az összes Apache interaktív Hive-fürt alapértelmezett. Tovább információ: [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>A szolgáltatás javítására.</b> A dinamikus partíció betöltése optimalizáltuk. Tovább információ: [https://issues.apache.org/jira/browse/HIVE-14204](https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>A szolgáltatás javítására.</b> HDInsight Linux rendszeren konfigurációs optimalizálását.</li><li><b>Hibajavítás.</b> `CredentialProviderFactory$getProviders` nem szálbiztos. Most ezt a problémát megoldottuk. Tovább információ: [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Hibajavítás.</b> Magas CPU-használat WASB-illesztőprogrammal `liststatus` API hibás ATS teljesítményt eredményez. Most ezt a problémát megoldottuk. Tovább információ: [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Szolgáltatás |Interaktív Hive (előzetes verzió) |
| Hadoop-fürtök frissítéseinek |Javult templeton eszközön keresztül végzett feladat művelet megbízhatóság. További információkért lásd: [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Szolgáltatás |Hadoop |
| YARN-frissítések | HDInsight most már adatbázist hoz létre 250 GB-os Ambari (nélkül a növekvő költségek), ami az ügyfelek jobb felhasználói élményt eredményez. Ez a változás kell megakadályozza, hogy az ATS első adatok felfelé kitöltve, és valószínűleg jobb teljesítmény érdekében. |Szolgáltatás |Összes |
| Spark-frissítések | Spark 2.1.1 kiadása. További információkért lásd: [Apache Spark kiadási 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Szolgáltatás | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 – a HDInsight 3.6-os általános rendelkezésre állás

* Ebben a kiadásban az Azure HDInsight hozzáadja a HDP 2.6 alapul, 3.6-os verzióját. HDP 2.6 kibocsátási megjegyzései elérhetők [Itt](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) találhat további információt a HDInsight-verziókról és [Itt](hdinsight-component-versioning.md). HDInsight 3.6-os verziója érhető el a következő számítási feladatokkal:

    * Hadoop v2.7.3
    * A HBase v1.1.2
    * A Storm v1.1.0
    * A Spark v2.1.0
    * Interaktív Hive v2.1.0

* **Hive-nézet 2.0 támogatása**. Ez a felhasználói élmény interaktív Hive kell javításához. További információkért lásd: [Hortonworks dokumentáció](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Hive LLAP-teljesítményt érintő továbbfejlesztés**. További információkért lásd: [Hortonworks dokumentáció](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Új szolgáltatások a Hive**. Lásd: [Hortonworks dokumentáció](https://hortonworks.com/apache/hive/#section_4).

* **Hive-CLI elavulásának**: Hive CLI hamarosan elavulttá válik, és hogy a Beeline használata. További információkért lásd: [Apache-dokumentáció](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). A Beeline használata a HDInsight útmutatásért lásd: [Beeline használata a HDInsight Hadoop-fürtök](hadoop/apache-hadoop-use-hive-beeline.md).

* **Az Apache Phoenix és a HBase új funkciói**.
    * Tárolási kvóta támogatása: Több-bérlős környezetekben, így korlátozott tárterület a leggyakrabban használt egy táblánként és névtér szintek.
    * A Phoenix indexelési fejlesztései: Növekményes index-létrehozási és újraépítése és folytathat az indexelés a korábbi hibák után.
    * A Phoenix adatok integritásának eszközzel: Támogatja a séma, index és más metaadatokat.


* **A hbase-ben probléma**: A fürt megosztott kötetei szolgáltatás tömeges feltöltés MapReduce-feladatot futtat, miközben a következő szintaxist hibát eredményezhet.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Ehelyett használja a következő szintaxist:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017 – a Spark 2.1-es on HDInsight 3.6-os kiadásában (előzetes verzió)
* [A Spark 2.1-es](http://spark.apache.org/releases/spark-release-2-1-0.html) növeli a sok stabilitással és használattal kapcsolatos problémák korábbi verzióival. Minden Spark számítási feladatok, például a Spark mag, az SQL, a gépi tanulás és a Streamelési új funkciókat is biztosít.
* Strukturált Streamelés esemény idő vízjelek és a Kafka 0.10-ás connector támogatása továbbfejlesztett méretezhetőség beolvasása.
* Spark SQL-particionálást már kezeli új méretezhető partíció kezelési mechanizmus segítségével. További részletek [Itt](http://spark.apache.org/releases/spark-release-2-1-0.html) amelyen a verziófrissítésre.
* Az Azure HDInsight 3.6 előzetes verziója jelenleg Spark 2.1-es nem támogatja az Üzletiintelligencia-eszköze kapcsolat ODBC-illesztő használatával.
* A Spark 2.1-es fürtök az Azure Data Lake Store-hozzáférés nem támogatott ebben az előzetes verzióban.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016-2.0.1 sparkot a HDInsight 3.5-ös kiadás
Spark 2.0.1 már elérhető a Spark-fürtökön (HDInsight 3.5-ös verzió).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 – R Server 9.0 esetén a HDInsight 3.5-ös kiadás (Spark 2.0-s)
*   R Server-fürtök mostantól a két verziója lehetőséget is tartalmazzák: Az R Server 9.0 HDI 3.5-ös (Spark 2.0-s) és az R Server 8.0-s a HDI 3.4-es (Spark 1.6-os).
*   Az R Server 9.0 a HDI 3.5 (Spark 2.0) R 3.3.2 épül, és új ScaleR adatforrás-függvényeken RxHiveData és RxParquetData nevű az adatok betöltéséhez Hive és a parquet eszközökben a Spark DataFrames elemzéshez közvetlenül a ScaleR által végzett tartalmazza. További információkért lásd a súgó ezeket a funkciókat az R használata a beágyazott a **? RxHiveData** és **? RxParquetData** parancsokat.
*   Az RStudio Server community Edition kiadást mostantól alapértelmezés szerint telepítve van (és a letiltási lehetőség) a fürt konfigurációjának panelen a kiépítési folyamat részeként.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016 - kiadás Spark 2.0-s on HDInsight
* A HDInsight 3.5-ös Spark 2.0-fürtök mostantól támogatják a Livy és Jupyter-szolgáltatások.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 – R Server on HDInsight kiadása
* Az URI-t, a peremhálózati csomópont hozzáféréshez változott **clustername**-ed-ssh.azurehdinsight.net
* Az R Server on HDInsight-fürtök kiépítése egyszerűbb lett.
* Az R Server on HDInsight már elérhető, mint a rendszeres HDInsight "R Server" fürt típusa, és a egy külön HDInsight-alkalmazás már nincs telepítve. A peremhálózati csomópont és az R Server bináris fájlok most már kiépített az R Server-fürt központi telepítésének részeként. Ez növeli a gyorsasága és megbízhatósága kiépítése. Az R Server díjszabási modellje a ennek megfelelően frissül.
* Az R Server-fürt típusa árát most Standard szintnek megfelelő díjat, és az R Server pótdíja ár alapján. Ez a változás nem érinti hatékony díjszabása az R Server csak hogyan a költségek jelenik meg a számlán változik. Minden meglévő R Server-fürt továbbra is működni, és a Resource Manager-sablonok továbbra is működnek, amíg elavulással kapcsolatos figyelmeztetés. **Bár az új Resource Manager-sablonnal történő parancsfájlalapú telepítés frissítése az ajánlott.**






