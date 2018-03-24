---
title: A kibocsátási megjegyzésekben talál az Azure HDInsight Hadoop-összetevők |} Microsoft Docs
description: Legújabb kibocsátási megjegyzések és az Azure hdinsight Hadoop-összetevők verzióit. Fejlesztési tippek és részletek beszerzése Spark, R Server, Hive és további.
services: hdinsight
documentationcenter: ''
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: nitinme
ms.openlocfilehash: bf0bfa052c8cd7589af40972fda1e1802b4d1acb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Az Azure HDInsight Hadoop-összetevők kibocsátási megjegyzései

Ez a cikk nyújt tájékoztatást a **legutóbbi** Azure HDInsight kiadás frissítéseket. A korábbi kiadásokban információkért lásd: [HDInsight kibocsátási megjegyzések archív](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információkért lásd: [HDInsight versioning cikk](hdinsight-component-versioning.md).

## <a name="notes-for-03202018---release-of-spark-22-on-hdinsight-36"></a>Megjegyzések a 03/20/2018 - 2.2 Spark on HDInsight 3.6 kiadása

- Spark 2.2.0 stabilitását növeli a Spark mag, SQL, ML között, és számos lehetőséget kínál a strukturált Streaming GA állapotát. Spark 2.2.0 a HDInsight 3.6 most érhető el.


## <a name="notes-for-08012017-release-of-hdinsight"></a>A HDInsight 08/01/2017 kibocsátási megjegyzései

| Beosztás | Leírás | Érintett terület  | Fürt típusa  | 
| --- | --- | --- | --- | --- |
| Microsoft R Server 9.1 hdinsight kiadása |HDInsight létesítési R Server 9.1 fürtök mostantól támogatja a hdinsight platformon. További információk a Microsoft R Server 9.1 kiadására: [ebben a blogban](https://blogs.technet.microsoft.com/dataplatforminsider/2017/04/19/introducing-microsoft-r-server-9-1-release/). |Szolgáltatás |R Server |
| HDInsight 3.6 mostantól tartalmazza a Hadoop-veremmel újabb verziói|<ul><li>A frissített verziókat részletes listájáért lásd: [elérhető, a HDInsight Hadoop összetevő verzióinak](hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions).</li><li>A Hadoop-veremmel legújabb verziói javított listájáért lásd: [Apache javítás információk](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/patch_parent.html).</li><li>Módosítások között HDP (amely most már elérhető a HDInsight 3.6) 2.6.1 megtörje listáját lásd: [ https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html ](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/behavior_changes.html).</li><li>HDP 2.6.1 kapcsolatos ismert problémák listáját, [ismert problémák](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.1/bk_release-notes/content/known_issues.html).</li></ul> |Szolgáltatás |Összes |– |
| Frissítések fürtökre interaktív struktúra (előzetes verzió) |<ul><li><b>A szolgáltatás javítására.</b> Gyorsítótárazott metaadattárhoz, amely csökkenti a metaadatok gyorsítótárazása révén a háttérkiszolgálón SQL, és javítja a teljesítményt az összes metaadat-művelet végrehajtása.  Ennek a fejlesztésnek köszönhetően már egy alapértelmezett összes interaktív Hive fürtökön. Tovább információ: [https://issues.apache.org/jira/browse/HIVE-16520](https://issues.apache.org/jira/browse/HIVE-16520).</li><li><b>A szolgáltatás javítására.</b> Dinamikus partíció betöltése megfelelően lett optimalizálva. További információ: [https://issues.apache.org/jira/browse/HIVE-14204] (https://issues.apache.org/jira/browse/HIVE-14204).</li><li><b>A szolgáltatás javítására.</b> A HDInsight Linux rendszeren konfigurációs optimalizálás.</li><li><b>Hiba a javítás.</b> `CredentialProviderFactory$getProviders` Nincs szálbiztos. Ez a probléma fennáll most. Tovább információ: [https://issues.apache.org/jira/browse/HADOOP-14195](https://issues.apache.org/jira/browse/HADOOP-14195).</li><li><b>Hiba a javítás.</b> Magas CPU-használatot a WASB illesztőprogram `liststatus` API rossz ATS teljesítményt eredményez. Ez a probléma fennáll most. Tovább információ: [https://github.com/Azure/azure-storage-java/pull/154](https://github.com/Azure/azure-storage-java/pull/154).</li></ul> |Szolgáltatás |Interaktív struktúra (előzetes verzió) |
| Hadoop-fürtök módosításai |Lépni a Templeton feladat művelet megbízhatóságának növelése További információkért lásd: [https://issues.apache.org/jira/browse/HIVE-15947](https://issues.apache.org/jira/browse/HIVE-15947) |Szolgáltatás |Hadoop |
| YARN frissítések | HDInsight létrehoz egy 250 GB Ambari adatbázis (nélkül költség növekvő), amely az ügyfelek egy jobb felhasználói élményt eredményez. Ez a változás kell ATS megakadályozása első tölti, és valószínűleg jobb teljesítményt. |Szolgáltatás |Összes |
| Spark frissítések | Spark 2.1.1 kiadásában. További információkért lásd: [Spark kiadás 2.1.1](https://spark.apache.org/releases/spark-release-2-1-1.html). | Szolgáltatás | Spark |

  



## <a name="04062017---general-availability-of-hdinsight-36"></a>04/06/2017 - általános rendelkezésre állását HDInsight 3.6.

* Ezzel a kiadással Azure HDInsight hozzáadja verzió 3.6, amely HDP 2.6 alapul. Kibocsátási megjegyzések HDP 2.6 érhetők el [Itt](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) és a HDInsight-verziókról további információt talál [Itt](hdinsight-component-versioning.md). Az alábbi munkaterhelések HDInsight 3.6 érhető el:

    * Hadoop v2.7.3
    * A HBase v1.1.2
    * A Storm v1.1.0
    * Spark v2.1.0
    * Interactive Hive v2.1.0

* **Hive nézete 2.0 támogatása**. Ez kell Hive interaktív felhasználói élményének növelése. További információkért lásd: [Hortonworks dokumentáció](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **A Hive LLAP teljesítményfejlesztést**. További információkért lásd: [Hortonworks dokumentáció](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Új szolgáltatások a Hive**. Lásd: [Hortonworks dokumentáció](https://hortonworks.com/apache/hive/#section_4).

* **Parancssori felület érvénytelenítése Hive**: CLI struktúra elavult, és hogy Beeline használja helyette. További információkért lásd: [Apache-dokumentáció](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Beeline használata a hdinsight eszközzel, lásd: [Beeline használata a HDInsight Hadoop-fürtök](hadoop/apache-hadoop-use-hive-beeline.md).

* **Az Apache Phoenix és HBase új funkciói**.
    * Tárolási kvóta támogatási: több-bérlős környezetekben, lehetővé téve a korlátozott tárolóhely a gyakran használt egy táblánként és névtér szintek.
    * Phoenix indexelő fejlesztései: növekményes indexlétrehozás és a korábbi hibák indexelő rebuild/Folytatás.
    * Phoenix adatok integritásának eszköz: támogatja a séma, Tárgymutató és egyéb metaadatokat érvényesítése.


* **A hbase eszközzel probléma**: a fürt megosztott kötetei szolgáltatás tömeges feltöltés MapReduce futtatása során feladat, a következő szintaktikai hiba okozhatja.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Ehelyett használja a következő szintaxist:

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>02/28/2017-2.1 Spark on HDInsight 3.6 kiadása (előzetes verzió)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) növeli a sok stabilitás és a használhatóság problémák korábbi verzióival. Új szolgáltatások közötti összes Spark-munkaterhelések, például a Spark mag, az SQL, a gépi tanulás összetevő és a Streaming is jelent.
* Strukturált Streaming lekérdezi az esemény időpontja vízjelek és Kafka 0.10 connector támogatása továbbfejlesztett méretezhetőséget.
* Spark SQL particionálás most kezeli új méretezhető partíció kezelése mechanizmus segítségével. További részletek [Itt](http://spark.apache.org/releases/spark-release-2-1-0.html) frissítésével.
* Spark 2.1 az Azure HDInsight 3.6 előzetes verzió jelenleg nem támogatja a BI-eszköz kapcsolat ODBC-illesztőprogram használatával.
* Spark 2.1-fürtök az Azure Data Lake Store elérése nem támogatott ebben az előzetes verzióban.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>11/18/2016-2.0.1 Spark on HDInsight 3.5 kiadása
Spark 2.0.1 már a Spark-fürtök (HDInsight 3.5-ös verzió) érhető el.

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>11/16/2016 - kiadás az R Server 9.0 hdinsight 3.5 (Spark 2.0-s)
*   R Server-fürtök között már elérhető a kívánt beállítást két verziója: R Server 9.0 HDI 3.5 (Spark 2.0) és az R Server 8.0 a HDI 3.4 (Spark 1.6-os).
*   R Server 9.0 HDI 3.5 (Spark 2.0) R 3.3.2 épül, és új ScaleR adatok forrás RxHiveData és RxParquetData hívja az adatok betöltése a Hive és Parquet közvetlenül a Spark DataFrames elemzés ScaleR függvényt tartalmaz. További információkért tekintse meg a súgó ezeket a funkciókat R használata a beágyazott a **? RxHiveData** és **? RxParquetData** parancsok.
*   Rstudióból Server közösségi kiadás most már telepítve van (egy opcióval letiltási) alapértelmezés szerint a fürtkonfiguráció panelen a kiépítési folyamat részeként.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>11/09/2016-2.0 Spark on HDInsight kiadása
* Spark 2.0 fürtöket a HDInsight 3.5 mostantól támogatja a Livy és Jupyter szolgáltatásokat.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>10/26/2016 - R Server on HDInsight kiadása
* A peremhálózati csomópont hozzáféréshez URI-re módosította **clustername**-kell adnia végrehajtási adatokat-ssh.azurehdinsight.net
* Az R Server on HDInsight-fürtök kiépítése úgy alakították ki.
* Az R Server on HDInsight rendszeres HDInsight lehetőség "R Server" fürt típusa, és már nem egy külön HDInsight-alkalmazásokat telepítették. Az élcsomóponthoz és R Server bináris fájlok most már kiépített az R fájlkiszolgálófürt központi telepítésének részeként. Ez növeli a sebesség és a kiépítés megbízhatóságát. Az R Serverhez árképzési modellt a ennek megfelelően frissül.
* R Server fürt típusa árát a Standard csomag ár plusz R Server emelt díjas ár alapján. Ez a változás nincs hatással az R Server; hatékony díjszabása csak hogyan a díjak jelenjenek meg a számlázási változik. R Server összes meglévő fürt továbbra is működnek, és a Resource Manager-sablonok is érvénytelenítése értesítésig működni. **Ajánlott, ha a parancsfájlalapú telepítésének folyamatát, új Resource Manager-sablonnal használ.**






