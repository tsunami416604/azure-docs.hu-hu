---
title: HDInsight 4.0 áttekintés (előzetes verzió) – Azure
description: A HDInsight 3.6 és a HDInsight 4.0 funkcióinak, korlátainak és frissítési ajánlásainak összehasonlítása.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 152a145601dcf4282ec0a3a3b6ebcf37bd11848b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992966"
---
# <a name="hdinsight-40-overview-preview"></a>HDInsight 4.0 áttekintés (előzetes verzió)

Az Azure HDInsight a legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében a nyílt forráskódú Hadoop- és Spark-elemzésekhez az Azure-ban. A HDInsight (HDI) 4.0 a [Hortonworks Data Platform (HDP) 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) Hadoop-összetevőinek felhőalapú disztribúciója. Ez a cikk az Azure HDInsight legújabb kiadásával és a frissítés menetével kapcsolatban nyújt információkat.

## <a name="whats-new-in-hdi-40"></a>A HDI 4.0 újdonságai

### <a name="hive-30-and-llap"></a>Hive 3.0 és LLAP

A Hive közel valós idejű elemzésfeldolgozási (LLAP) szolgáltatása állandó lekérdezéskiszolgálók és memórián belüli gyorsítótárazás használatával gyors SQL-lekérdezési eredményeket szolgáltat a távoli felhőtárolókban lévő adatok alapján. A Hive LLAP állandó démonok használatával hajtja végre a Hive-lekérdezések töredékeit. A lekérdezések végrehajtása az LLAP-vel nagyon hasonló az LLAP nélküli Hive-hoz, csak a feldolgozó feladatok a tárolók helyett LLAP-démonokban futnak.

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

Nem kell amiatt aggódnia, hogy véletlenül közvetlenül éri el a tranzakciós Hive-táblákat a Sparkból, ami inkonzisztens találatokat, duplikált vagy sérült adatokat eredményezhetne. A HDI 4.0 a Spark- és a Hive-táblákat külön metaadattárakban tárolja. A Hive Data Warehouse-összekötő használatával a tranzakciós Hive-táblákat kifejezetten Spark külső táblákként regisztrálhatja.

További tudnivalók az [Apache Sparkról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).


### <a name="oozie"></a>Oozie

Az Apache Oozie 4.3.1 a HDI 4.0 részét képezi az alábbi módosításokkal:

* Az Oozie már nem futtat Hive-műveleteket. A Hive parancssori felülete megszűnt, és a BeeLine vette át a helyét.

* A nemkívánatos függőségeket kizárhatja a megosztási kódtárból egy kizárási minta felvételével a **job.properties** fájlba.

További tudnivalók az [Apache Oozie-ról](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdi-40"></a>Frissítés a HDI 4.0-ra

Ahogy bármely nagyobb kiadás esetében, itt is fontos alaposan tesztelni az összetevőket, mielőtt az új verziót az éles környezetben implementálná. A HDI 4.0-s verzióval is megkezdheti a frissítést, de a véletlen hibák elkerülése érdekében a 3.6-os verzió az alapértelmezett választás.

Nincs támogatott frissítési útvonal a HDI korábbi verzióiról a HDI 4.0-ra. Mivel a metaadattár- és a blobadatformátumok megváltoztak, a HDI 4.0 nem kompatibilis az előző verziókkal. Fontos, hogy az új HDI 4.0-környezetet külön kezelje az aktuális éles környezettől. Ha a HDI 4.0-t az aktuális környezetben helyezi üzembe, az frissíti a metaadattárt, ami nem vonható vissza.  

## <a name="limitations"></a>Korlátozások

* A HDI 4.0 nem támogatja a MapReduce-t. Használja inkább a Tezt. További tudnivalók az [Apache Tezről](https://tez.apache.org/).

* A Hive View a HDI 4.0-ban a továbbiakban nem érhető el. 

## <a name="next-steps"></a>További lépések

* [Azure HDInsight – dokumentáció](index.yml)
* [Kibocsátási megjegyzések](hdinsight-release-notes.md)
