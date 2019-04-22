---
title: HDInsight 4.0 áttekintése – Azure
description: A HDInsight 3.6 és a HDInsight 4.0 funkcióinak, korlátainak és frissítési ajánlásainak összehasonlítása.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: overview
ms.date: 04/15/2019
ms.openlocfilehash: 553f50897afaaf9c677e84f9cfffbff7d2c1e607
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679673"
---
# <a name="hdinsight-40-overview"></a>HDInsight 4.0 – áttekintés

Az Azure HDInsight a legnépszerűbb szolgáltatások nagyvállalati ügyfelek körében a nyílt forráskódú Apache Spark és Apache Hadoop analytics az Azure-ban egyike. HDInsight 4.0, az Apache Hadoop-összetevők felhőalapú terjesztett a [Hortonworks Data Platform (HDP) 3.0-s](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html). Ez a cikk az Azure HDInsight legújabb kiadásával és a frissítés menetével kapcsolatban nyújt információkat.

## <a name="whats-new-in-hdinsight-40"></a>HDInsight 4.0 újdonságai?

### <a name="apache-hive-30-and-llap"></a>Az Apache Hive, 3.0-s és az LLAP

Az Apache Hive közel valós idejű analitikus feldolgozás (LLAP) kiszolgálók és a memórián belüli gyorsítótárazást gyors SQL lekérdezési eredményeket a távoli felhőalapú tárolás az adatok állandó lekérdezést használ. A Hive LLAP állandó démonok használatával hajtja végre a Hive-lekérdezések töredékeit. A lekérdezések végrehajtása az LLAP-vel nagyon hasonló az LLAP nélküli Hive-hoz, csak a feldolgozó feladatok a tárolók helyett LLAP-démonokban futnak.

A Hive LLAP előnyei:

* Mélyreható SQL-elemzések, például összetett illesztések, allekérdezések, ablakkezelési függvények, rendezések, felhasználó által definiált függvények és komplex összesítések végrehajtása a teljesítmény és a méretezhetőség romlása nélkül.

* Interaktív lekérdezések futtatása az adatok előkészítéséhez használt tárolón belül, így az adatokat nem szükséges egy másik alrendszerbe átmozgatni az elemzések feldolgozásához.

* A lekérdezési eredmények gyorsítótárazásának köszönhetően a korábban kiszámított lekérdezési eredmények újra felhasználhatóak, ami időt takarít meg, és kíméli a lekérdezéshez szükséges fürtfeladatokat futtató erőforrásokat.

### <a name="hive-dynamic-materialized-views"></a>A Hive dinamikus, tényleges táblán alapuló nézetei

A Hive mostantól támogatja a dinamikus, tényleges táblán alapuló nézeteket, azaz a releváns összesítések előzetes kiszámítását, amelyek a lekérdezések feldolgozásának gyorsabbá tételét szolgálják az adattárházakban. A tényleges táblán alapuló nézetek natív módon tárolhatók a Hive-ban, és zökkenőmentesen használhatják az LLAP-gyorsítást.

### <a name="hive-transactional-tables"></a>A Hive tranzakciós táblái

A HDI 4.0 részét képezi az Apache Hive 3, amelynek használatához a Hive-adattárházban lévő tranzakciós tábláknak meg kell felelniük az atomitási, konzisztenciabeli, elkülönítési és tartóssági (ACID) követelményeknek. Az ACID-kompatibilis táblákat és táblaadatokat a Hive használja és felügyeli. A létrehozási, lekérési, frissítési és törlési (CRUD) táblákban lévő adatoknak az optimalizált sor oszlop (ORC) fájlformátumot kell követnie, a csak beszúrásos táblák azonban minden fájlformátumot támogatnak.

* Az ACID 2-es verziójában a tárolási formátummal és a végrehajtómotorral kapcsolatos teljesítmény is javult. 

* Az ACID alapértelmezés szerint engedélyezve van az adatfrissítések teljes támogatása érdekében.

* A fejlesztett ACID-képességek sorszintű frissítési és törlési funkcionalitást biztosítanak.

* Nincs teljesítménybeli többletterhelés.

* Nincs szükség gyűjtésre.

* A Spark a Hive Warehouse-összekötő segítségével képes írni és olvasni a Hive ACID-táblákat.

További információk az [Apache Hive 3-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Az Apache Spark a Hive Warehouse-összekötővel frissíthető táblákhoz és ACID-tranzakciókhoz fér hozzá. A Hive Warehouse-összekötővel a Hive tranzakciós táblák külső táblákként regisztrálhatók a Sparkban a teljes tranzakciós funkcionalitás használatához. A korábbi verziók csak a táblapartíciók kezelését támogatták. A Hive Warehouse-összekötő támogatja a Streaming DataFramest az írási és olvasási műveletek streameléséhez a Spark tranzakciós és streamelési Hive-tábláiban.

A Spark végrehajtói közvetlenül kapcsolódhatnak a Hive LLAP-démonokhoz az adatok tranzakciós lekéréséhez és frissítéséhez, így továbbra is a Hive vezérli az adatokat.

Az Apache Spark on HDInsight 4.0 az alábbi forgatókönyveket támogatja:

* Gépi tanulási modellbetanítás futtatása a jelentéskészítéshez használt tranzakciós táblán.
* Oszlopok biztonságos hozzáadása ACID-tranzakciók használatával a Spark ML-ből a Hive-táblákba.
* Spark streamelési feladatok futtatása a streamelési Hive-táblák változáscsatornáin.
* ORC-fájlok létrehozása közvetlenül a Spark strukturált stream feladataiból.

Nem kell amiatt aggódnia, hogy véletlenül közvetlenül éri el a tranzakciós Hive-táblákat a Sparkból, ami inkonzisztens találatokat, duplikált vagy sérült adatokat eredményezhetne. HDInsight 4.0-s Spark-táblák és a Hive-táblákat a külön Metaadattárakat tartani. A Hive Data Warehouse-összekötő használatával a tranzakciós Hive-táblákat kifejezetten Spark külső táblákként regisztrálhatja.

További tudnivalók az [Apache Sparkról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="apache-oozie"></a>Az Apache Oozie

Az Apache Oozie 4.3.1 a HDI 4.0 részét képezi az alábbi módosításokkal:

* Az Oozie már nem futtat Hive-műveleteket. A Hive parancssori felülete megszűnt, és a BeeLine vette át a helyét.

* A nemkívánatos függőségeket kizárhatja a megosztási kódtárból egy kizárási minta felvételével a **job.properties** fájlba.

További tudnivalók az [Apache Oozie-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>A HDInsight 4.0 frissítése

Ahogy bármely nagyobb kiadás esetében, itt is fontos alaposan tesztelni az összetevőket, mielőtt az új verziót az éles környezetben implementálná. HDInsight 4.0 érhető el, hogy a frissítési folyamat megkezdése, de az alapértelmezett beállítás megakadályozza, hogy a véletlen mishaps HDInsight 3.6-ot.

Nincs támogatott frissítési útvonal a HDInsight korábbi verzióiról a HDInsight 4.0 van. Metaadattár és a blob adatformátumok a célnyelven módosult, mert a HDInsight 4.0 ezért nem kompatibilis az előző verziókat. Fontos, hogy őrizze meg az új HDInsight 4.0 környezethez külön, a jelenlegi éles környezetből. Ha az aktuális környezet HDInsight 4.0-s verzióját telepíti, a Metaadattár lesz frissítve, és nem vonható vissza.  

## <a name="limitations"></a>Korlátozások

* HDInsight 4.0-s verzióját nem támogatja a MapReduce. Használja helyette az Apache Tez. További tudnivalók az [Apache Tezről](https://tez.apache.org/).
* HDInsight 4.0-s verzióját nem támogatja az Apache Storm. 
* Hive-nézet már nem érhető el, a HDInsight 4.0-s verzióját. 
* Spark- és interaktív lekérdezési fürtökben az Apache Zeppelin nem támogatja a parancshéj-értelmezők használatát.
* Az LLAP nem *tiltható le* a Spark-LLAP-fürtön. Csak az LLAP kikapcsolására van lehetősége.
* Az Azure Data Lake Storage Gen2 nem tud Juypter jegyzetfüzeteket menteni Spark-fürtökbe.

## <a name="next-steps"></a>További lépések

* [Azure HDInsight – dokumentáció](index.yml)
* [Kibocsátási megjegyzések](hdinsight-release-notes.md)
