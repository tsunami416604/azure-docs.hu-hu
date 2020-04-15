---
title: A HDInsight 4.0 áttekintése – Azure
description: A HDInsight 3.6 és a HDInsight 4.0 funkcióinak, korlátainak és frissítési ajánlásainak összehasonlítása.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: d0fd9999abc4a67ded0f66977e1a3ba5310c87be
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383028"
---
# <a name="azure-hdinsight-40-overview"></a>Az Azure HDInsight 4.0 áttekintése

Az Azure HDInsight az Apache Hadoop és az Apache Spark nagyvállalati ügyfelei nek egyik legnépszerűbb szolgáltatása. A HDInsight 4.0 az Apache Hadoop-összetevők felhőalapú disztribúciója. Ez a cikk az Azure HDInsight legújabb kiadásával és a frissítés menetével kapcsolatban nyújt információkat.

## <a name="whats-new-in-hdinsight-40"></a>A HDInsight 4.0 újdonságai

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3.0 és alacsony késleltetésű analitikai feldolgozás

Az Apache Hive alacsony késleltetésű analitikus feldolgozás (LLAP) állandó lekérdezési kiszolgálókat és memórián belüli gyorsítótárazást használ. Ez a folyamat gyors SQL-lekérdezési eredményeket biztosít a távoli felhőbeli tárolóban lévő adatokon. Hive LLAP egy sor állandó démonok, amelyek a Hive-lekérdezések töredékei. A lekérdezések végrehajtása az LLAP-vel nagyon hasonló az LLAP nélküli Hive-hoz, csak a feldolgozó feladatok a tárolók helyett LLAP-démonokban futnak.

A Hive LLAP előnyei:

* Képes mély SQL-elemzésre a teljesítmény és az alkalmazkodóképesség feláldozása nélkül. Például összetett illesztések, segédlekérdezések, ablakos függvények, rendezés, felhasználó által definiált függvények és összetett összesítések.

* Interaktív lekérdezések futtatása az adatok előkészítéséhez használt tárolón belül, így az adatokat nem szükséges egy másik alrendszerbe átmozgatni az elemzések feldolgozásához.

* A lekérdezés eredményének gyorsítótárazása lehetővé teszi a korábban kiszámított lekérdezési eredmények újbóli felhasználtvá t. Ez a gyorsítótár időt és erőforrásokat takarít meg a lekérdezéshez szükséges fürtfeladatok futtatásával.

### <a name="hive-dynamic-materialized-views"></a>A Hive dinamikus, tényleges táblán alapuló nézetei

Hive most antól támogatja a dinamikus materializált nézetek, vagy a megfelelő összefoglalók előre számítása. A nézetek felgyorsítják a lekérdezésfeldolgozást az adatraktárakban. A tényleges táblán alapuló nézetek natív módon tárolhatók a Hive-ban, és zökkenőmentesen használhatják az LLAP-gyorsítást.

### <a name="hive-transactional-tables"></a>A Hive tranzakciós táblái

A HDI 4.0 tartalmazza az Apache Hive 3-at. A Hive 3 atomi, konzisztencia, elkülönítés és tartóssági megfelelőségszükséges a Hive-raktárban élő tranzakciós táblákhoz. Az ACID-kompatibilis táblákat és táblaadatokat a Hive használja és felügyeli. A létrehozási, beolvasási, frissítési és törlési (CRUD) táblákban lévő adatoknak optimalizált soroszlop (ORC) fájlformátumban kell lenniük. A csak beszúrási táblázatok minden fájlformátumot támogatnak.

* Az ACID 2-es verziójában a tárolási formátummal és a végrehajtómotorral kapcsolatos teljesítmény is javult.

* Az ACID alapértelmezés szerint engedélyezve van az adatfrissítések teljes támogatása érdekében.

* A fejlesztett ACID-képességek sorszintű frissítési és törlési funkcionalitást biztosítanak.

* Nincs teljesítménybeli többletterhelés.

* Nincs szükség gyűjtésre.

* A Spark a Hive Warehouse-összekötő segítségével képes írni és olvasni a Hive ACID-táblákat.

További információk az [Apache Hive 3-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Az Apache Spark a Hive Warehouse-összekötővel frissíthető táblákhoz és ACID-tranzakciókhoz fér hozzá. A Hive Warehouse-összekötővel a Hive tranzakciós táblák külső táblákként regisztrálhatók a Sparkban a teljes tranzakciós funkcionalitás használatához. A korábbi verziók csak a táblapartíciók kezelését támogatták. Hive Warehouse Connector is támogatja a streamelési dataframes.  Ez a folyamat streameli az olvasást, és a Spark tól származó hive-táblák tranzakciós és streamelési.

A Spark végrehajtói közvetlenül kapcsolódhatnak a Hive LLAP-démonokhoz az adatok tranzakciós lekéréséhez és frissítéséhez, így továbbra is a Hive vezérli az adatokat.

Az Apache Spark on HDInsight 4.0 az alábbi forgatókönyveket támogatja:

* Gépi tanulási modellbetanítás futtatása a jelentéskészítéshez használt tranzakciós táblán.
* Oszlopok biztonságos hozzáadása ACID-tranzakciók használatával a Spark ML-ből a Hive-táblákba.
* Spark streamelési feladatok futtatása a streamelési Hive-táblák változáscsatornáin.
* ORC-fájlok létrehozása közvetlenül a Spark strukturált stream feladataiból.

Többé már nem kell aggódnia, hogy véletlenül megpróbál hozzáférni a Hive tranzakciós táblák közvetlenül a Sparkból. Inkonzisztens eredményeket, duplikált adatokat vagy adatsérülést eredményez. A HDInsight 4.0-s, Spark-táblák és Hive-táblák külön metaáruházakban vannak tárolva. A Hive Data Warehouse-összekötő használatával a tranzakciós Hive-táblákat kifejezetten Spark külső táblákként regisztrálhatja.

További tudnivalók az [Apache Sparkról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apacs Oozie

Az Apache Oozie 4.3.1 a HDI 4.0 részét képezi az alábbi módosításokkal:

* Az Oozie már nem futtat Hive-műveleteket. A Hive parancssori felülete megszűnt, és a BeeLine vette át a helyét.

* A nemkívánatos függőségeket kizárhatja a megosztási kódtárból egy kizárási minta felvételével a **job.properties** fájlba.

További tudnivalók az [Apache Oozie-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Frissítés a HDInsight 4.0-s csomagra

Alaposan tesztelje az összetevőket, mielőtt a legújabb verziót éles környezetben megvalósítana. A HDInsight 4.0 elérhető a frissítési folyamat megkezdéséhez. A HDInsight 3.6 az alapértelmezett lehetőség a véletlen balesetek megelőzésére.

A HDInsight korábbi verzióitól a HDInsight 4.0-s verziójára nem támogatott frissítési útvonal. Mivel a Metastore és a blob adatformátuma megváltozott, a 4.0 nem kompatibilis a korábbi verziókkal. Fontos, hogy az új HDInsight 4.0-s környezetet elkülönítse a jelenlegi éles környezettől. Ha a HDInsight 4.0-t a jelenlegi környezetében telepíti, a Metastore véglegesen frissül.  

## <a name="limitations"></a>Korlátozások

* A HDInsight 4.0 nem támogatja a MapReduce-t az Apache Hive számára. Használja inkább az Apache Tez-t. További tudnivalók az [Apache Tezről](https://tez.apache.org/).
* A HDInsight 4.0 nem támogatja az Apache Stormot.
* A Hive-nézet már nem érhető el a HDInsight 4.0-s verzióban.
* Az Apache Zeppelin shell-értelmezője nem támogatott a Spark és az Interaktív lekérdezési fürtökben.
* Az LLAP nem *tiltható le* a Spark-LLAP-fürtön. Az LLAP csak kikapcsolható.
* Az Azure Data Lake Storage Gen2 nem tudja menteni a Jupyter-jegyzetfüzeteket egy Spark-fürtben.

## <a name="next-steps"></a>További lépések

* [Azure HDInsight dokumentáció](index.yml)
* [Kibocsátási megjegyzések](hdinsight-release-notes.md)
