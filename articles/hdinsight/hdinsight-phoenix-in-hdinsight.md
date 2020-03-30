---
title: Apache Phoenix a HDInsightban – Azure HDInsight
description: Az Apache Phoenix áttekintése
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: b1d81296c996ab09cb6482cb970496779ccf8bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435496"
---
# <a name="apache-phoenix-in-azure-hdinsight"></a>Apache Phoenix az Azure HDInsightban

[Apache Phoenix](https://phoenix.apache.org/) egy nyílt forráskódú, masszívan párhuzamos relációs adatbázis réteg épül [Apache HBase](hbase/apache-hbase-overview.md). A Phoenix lehetővé teszi az SQL-szerű lekérdezések használatát a HBase-en keresztül. A Phoenix az alatta lévő JDBC-illesztőprogramok segítségével teszi lehetővé a felhasználók számára, hogy egyedileg és ömlesztve hozzanak létre, töröljenek, módosítsanak SQL-táblákat, indexeket, nézeteket és szekvenciákat, valamint upsert sorokat. A Phoenix a MapReduce helyett noSQL natív összeállítást használ a lekérdezések fordításához, lehetővé téve az alacsony késleltetésű alkalmazások létrehozását a HBase tetején. A Phoenix társprocesszorokat ad hozzá az ügyfél által biztosított kód futtatásához a kiszolgáló címterében, és az adatokkal közösen elhelyezett kódot futtatja. Ez a megközelítés minimálisra csökkenti az ügyfél/kiszolgáló adatátvitelét.

Az Apache Phoenix big data-lekérdezéseket nyit meg olyan nem fejlesztők számára, akik programozás helyett SQL-szerű szintaxist használhatnak. A Phoenix nagy mértékben optimalizált a HBase számára, ellentétben más eszközökkel, például [az Apache Hive-val](hadoop/hdinsight-use-hive.md) és az Apache Spark SQL-rel. Az előnye, hogy a fejlesztők írásban erősen teljesítményt lekérdezések sokkal kevesebb kódot.

SQL-lekérdezés küldésekor a Főnix összeállítja a lekérdezést a HBase natív hívásokés a vizsgálat (vagy terv) párhuzamosan optimalizálási fut. Ez a réteg absztrakció megszabadítja a fejlesztő írása MapReduce feladatok, hogy összpontosítson, hanem az üzleti logika és a munkafolyamat az alkalmazás körül Phoenix big data tároló.

## <a name="query-performance-optimization-and-other-features"></a>Lekérdezési teljesítmény optimalizálása és egyéb funkciók

Az Apache Phoenix számos teljesítményfejlesztést és funkciót ad a HBase-lekérdezésekhez.

### <a name="secondary-indexes"></a>Másodlagos indexek

A HBase egyetlen indexe lexikografikusan van rendezve az elsődleges sorkulcson. Ezek a rekordok csak a sorkulcson keresztül érhetők el. A rekordok nak a sorkulcson kívül bármely oszlopon keresztül történő eléréséhez az összes adat beolvasásához a szükséges szűrő alkalmazása közben kell elvégezni. A másodlagos indexben az indexelt oszlopok vagy kifejezések egy alternatív sorkulcsot alkotnak, lehetővé téve a kereséseket és a tartományvizsgálatokat az indexen.

Másodlagos index létrehozása `CREATE INDEX` a következő paranccsal:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Ez a megközelítés jelentős teljesítménynövekedést hozhat az egyindexelt lekérdezések végrehajtásához képest. Ez a másodlagos indextípus egy **fedőindex,** amely a lekérdezésben szereplő összes oszlopot tartalmazza. Ezért a táblakeresés nem kötelező, és az index megfelel a teljes lekérdezésnek.

### <a name="views"></a>Nézetek

A Phoenix-nézetek lehetővé teszik a HBase-korlátozás leküzdését, ahol a teljesítmény kezd romoozni, ha több mint 100 fizikai táblát hoz létre. A Phoenix-nézetek lehetővé teszik, hogy több *virtuális tábla* osszon meg egy mögöttes Fizikai HBase táblát.

A Főnix nézet létrehozása hasonló a szabványos SQL nézet szintaxisához. Az egyik különbség az, hogy az alaptáblából örökölt oszlopokon kívül oszlopokat is definiálhat a nézethez. Új `KeyValue` oszlopokat is hozzáadhat.

Itt van például egy `product_metrics` fizikai tábla, amelynek neve a következő definícióval történik:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR,
    created_date DATE,
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Nézet definiálása a táblázat felett további oszlopokkal:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Ha később további oszlopokat `ALTER VIEW` szeretne hozzáadni, használja az utasítást.

### <a name="skip-scan"></a>Beszkaval

A beálló vizsgálat az összetett index egy vagy több oszlopát használja a különböző értékek megkereséséhez. A tartományvizsgálattól eltérően a can scan sinasztáson belüli beolvasást valósít meg, [ami jobb teljesítményt](https://phoenix.apache.org/performance.html#Skip-Scan)eredményez. A beolvasás során az első egyező értéket a program kihagyja az indexszel együtt, amíg a következő érték meg nem történik.

A kihagyási `SEEK_NEXT_USING_HINT` vizsgálat a HBase szűrő felsorolását használja. A `SEEK_NEXT_USING_HINT`használatával a kihagyási vizsgálat nyomon követi, hogy az egyes oszlopokban mely billentyűkészletre vagy kulcstartományokra keres. A kihagyási vizsgálat ezután vesz egy kulcsot, amely átadott a szűrő kiértékelése során, és meghatározza, hogy ez az egyik kombináció. Ha nem, a skip scan kiértékeli a következő legmagasabb gombot ugrani.

### <a name="transactions"></a>Tranzakciók

Míg a HBase sorszintű tranzakciókat biztosít, a Phoenix a [Tephra-val](https://tephra.io/) integrálva adja hozzá a sorközi és a keresztasztalos tranzakciók támogatását teljes [ACID](https://en.wikipedia.org/wiki/ACID) szemantikával.

A hagyományos SQL-tranzakciókhoz ugyanúgy, a Phoenix tranzakciókezelőn keresztül biztosított tranzakciók lehetővé teszik annak biztosítását, hogy egy atomi adategység sikeresen upserted legyen, és visszaállítja a tranzakciót, ha a upsert művelet sikertelen bármely tranzakcióképes táblában.

A Phoenix-tranzakciók engedélyezéséhez olvassa el az [Apache Phoenix tranzakciódokumentációját.](https://phoenix.apache.org/transactions.html)

Ha új táblát szeretne létrehozni, `TRANSACTIONAL` amelyben `true` a `CREATE` tranzakciók engedélyezve vannak, állítsa a tulajdonságot egy kimutatásban:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Ha egy meglévő táblát tranzakcióssá szeretne módosítani, `ALTER` használja ugyanazt a tulajdonságot egy kimutatásban:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]  
> A tranzakciós tábla nem állítható vissza nem tranzakciós táblára.

### <a name="salted-tables"></a>Sózott asztalok

*A régiókiszolgáló hotspotting jattot* okozhat, ha a HBase szekvenciális kulcsokkal rendelkező rekordjait írja. Bár előfordulhat, hogy több régió kiszolgálók a fürtben, az írási műveletek mind csak egy. Ez a koncentráció a hotspotting problémát hozza létre, amelyben az írási számítási feladatok helyett az összes rendelkezésre álló régiókiszolgálók között elosztva, csak egy kezeli a terhelést. Mivel minden régió rendelkezik egy előre meghatározott maximális mérettel, amikor egy régió eléri ezt a méretkorlátot, két kis régióra van osztva. Amikor ez megtörténik, az egyik új régió minden új rekordot felvesz, és ez lesz az új hotspot.

A probléma enyhítése és a jobb teljesítmény elérése érdekében a felosztás előtti táblákat úgy, hogy az összes régiókiszolgáló egyformán legyen használva. Phoenix biztosít *sózott táblázatok,* átláthatóan hozzátéve, a sózás byte a sor gombot egy adott táblázat. A tábla előre fel van osztva a sóbájt határain, hogy a tábla kezdeti fázisában egyenlő terheléselosztást biztosítson a régiókiszolgálók között. Ez a megközelítés elosztja az írási számítási feladatokat az összes rendelkezésre álló régiókiszolgálók között, az írási és olvasási teljesítmény javítása. A tábla sózásához adja meg a `SALT_BUCKETS` tábla tulajdonságát a tábla létrehozásakor:

```sql
CREATE TABLE Saltedweblogs (
    transactionid varchar(500) Primary Key,
    transactiondate Date NULL,
    customerid varchar(50) NULL,
    bookid varchar(50) NULL,
    purchasetype varchar(50) NULL,
    orderid varchar(50) NULL,
    bookname varchar(50) NULL,
    categoryname varchar(50) NULL,
    invoicenumber varchar(50) NULL,
    invoicestatus varchar(50) NULL,
    city varchar(50) NULL,
    state varchar(50) NULL,
    paymentamount DOUBLE NULL,
    quantity INTEGER NULL,
    shippingamount DOUBLE NULL) SALT_BUCKETS=4;
```

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Phoenix engedélyezése és finomhangolása az Apache Ambari segítségével

A HDInsight HBase fürt tartalmazza az [Ambari felhasználói felületet](hdinsight-hadoop-manage-ambari.md) a konfigurációs módosításokhoz.

1. A Phoenix engedélyezéséhez vagy letiltásához, valamint a Phoenix lekérdezési időtúllépési beállításainak szabályozásához jelentkezzen be az Ambari Web felhasználói felületére (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) a Hadoop felhasználói hitelesítő adataival.

2. Válassza a **HBase** elemet a bal oldali menüszolgáltatások listájából, majd válassza a **Configs** fület.

    ![Apache Ambari HBase konfigurációk](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config1.png)

3. Keresse meg a **Phoenix SQL** konfigurációs szakaszt a főnix engedélyezéséhez vagy letiltásához, és állítsa be a lekérdezés időidejét.

    ![Ambari Phoenix SQL konfigurációs szakasz](./media/hdinsight-phoenix-in-hdinsight/apache-ambari-phoenix.png)

## <a name="see-also"></a>Lásd még

* [Az Apache Phoenix használata Linux-alapú HBase-fürtökkel a HDInsightban](hbase/apache-hbase-query-with-phoenix.md)

* [Apache Zeppelin használatával apache phoenix-lekérdezések futtatása apache HBase-en keresztül az Azure HDInsightban](./hbase/apache-hbase-phoenix-zeppelin.md)
