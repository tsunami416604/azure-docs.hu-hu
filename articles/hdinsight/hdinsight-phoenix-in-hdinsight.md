---
title: HDInsight – Azure HDInsight az Apache Phoenixhez
description: ''
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 86b10d65ecaa52055244f3530f91c1cabbe219e0
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435548"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix a HDInsightban

[Az Apache Phoenix](http://phoenix.apache.org/) egy nyílt forráskódú, épülő masszív párhuzamos alapuló relációs Adatbázisréteg [Apache HBase](hbase/apache-hbase-overview.md). A Phoenix lehetővé teszi, hogy az SQL-szerű lekérdezéseket HBase-en. A Phoenix felhasználók létrehozása, törlés, egyenként és tömegesen az SQL táblák, indexek, nézetek és a feladatütemezések és upsert sorok alter JDBC-illesztőprogramok alatt használja. Phoenix használ lefordítani a lekérdezést, a MapReduce használata helyett natív noSQL-összeállítás engedélyezése HBase felett közel valós idejű alkalmazások létrehozását. A Phoenix hozzáadja a coprocessors támogatásához az ügyfél által megadott kódot futtató kiszolgáló, a címtér a védelmicsoport-készletek az adatok a kód végrehajtása. Ez a megközelítés minimálisra csökkenti az adatátviteli ügyfél/kiszolgáló.

Az Apache Phoenix megnyílik a big data-lekérdezések nem-fejlesztőknek, akik egy SQL-szerű szintaxist használhat programozási helyett. A Phoenix optimalizálhatja a hbase-hez, ellentétben más eszközök például [Hive](hadoop/hdinsight-use-hive.md) és az Apache Spark SQL. A fejlesztők számára a legfontosabb előny rendkívül nagy teljesítményű lekérdezéseket, hiszen lényegesen kevesebb kódot ír.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

SQL-lekérdezés elküldésekor a Phoenix lefordítja a lekérdezést a HBase natív hívások, és a vizsgálat (vagy tervezett) optimalizálás párhuzamosan futtatja. Absztrakciós réteg szabadít fel a fejlesztő MapReduce-feladatok írjanak hangsúlyozza inkább az üzleti logikát és a munkafolyamat az alkalmazás körül Phoenix a big Data típusú adatok tárolására.

## <a name="query-performance-optimization-and-other-features"></a>Lekérdezési teljesítmény optimalizálása és egyéb szolgáltatások

Az Apache Phoenix számos fejlesztést és szolgáltatások hozzáadása HBase lekérdezéseket.

### <a name="secondary-indexes"></a>A másodlagos indexek

A HBase, amely lexicographically rendezett sor elsődleges kulcs egyetlen indexszel rendelkezik. Ezek a rekordok csak a sorkulcs keresztül érhető el. Beolvasás az adatok a szükséges szűrő alkalmazása közben való elérése rögzíti a sorkulcs kívül bármely másik oszlopot igényel. A másodlagos indexek az oszlopok vagy kifejezéseket, amelyek indexelt űrlap egy másik sorkulcs, lehetővé téve a keresések és a tartomány figyeli az adott indexen.

A másodlagos indexet létrehozni a `CREATE INDEX` parancsot:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Ez a megközelítés egy jelentős teljesítménybeli növekedést partíciónként akár egyetlen indexelt lekérdezések végrehajtása során. Ez a másodlagos index típus egy **index kiterjedő**, tartalmazó összes oszlop szerepel a lekérdezésben. Ezért a tábla keresésekor azonban nem kötelező, és az index eleget tesz a teljes lekérdezést.

### <a name="views"></a>Nézetek

Phoenix nézetek oly módon, hogy egy HBase korlátozás, ahol a teljesítmény körülbelül 100-nál több fizikai táblák létrehozásakor csökkeni kezdődik meg. A Phoenix nézetek lehetővé teszik több *virtuális táblák* megosztani egy alapul szolgáló fizikai HBase tábla.

A Phoenix nézetek létrehozásával hasonlít a standard SQL-nézet szintaxissal. Egy különbség az, hogy meghatározhatja a nézet mellett az öröklődés forrása: az alaptáblában oszlopok oszlopokat. Új is hozzáadhat `KeyValue` oszlopokat.

Például a következő nevű fizikai tábla `product_metrics` az alábbi definíciójában:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Adja meg a nézet a táblán további oszlopokat:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Később további oszlopok hozzáadásához használja a `ALTER VIEW` utasítást.

### <a name="skip-scan"></a>Ellenőrzés kihagyása

Kihagyás vizsgálat egy vagy több oszlop egy összetett index különböző értékeket használja. Ellentétben a tartomány ellenőrzését kihagyása vizsgálat valósít meg intra-sor vizsgálatát, az elsőbbséget adó [továbbfejlesztett teljesítmény](http://phoenix.apache.org/performance.html#Skip-Scan). Keresés, miközben az első megfeleltetett értéket a rendszer kihagyta együtt az index mindaddig, amíg a következő érték található.

Használja a skip vizsgálat a `SEEK_NEXT_USING_HINT` enumerálás a HBase-szűrő. Használatával `SEEK_NEXT_USING_HINT`, a skip vizsgálat nyomon követi a kulcsok körét, vagy a kulcsok tartományokat, éppen keresi a rendszer az egyes oszlopokban. A skip vizsgálata, majd egy kulcsot, filter kiértékelés során átadott, és határozza meg, hogy egyik kombinációja vesz igénybe. Ha nem, a skip vizsgálat értékeli ki ugorhat a következő legmagasabb kulcsot.

### <a name="transactions"></a>Tranzakciók

HBase sorszintű tranzakciók biztosít, míg a Phoenix integrálható [Tephra](http://tephra.io/) cross-sor- és Kereszttábla tranzakciók támogatása teljes hozzáadandó [ACID](https://en.wikipedia.org/wiki/ACID) szemantikáját.

Az hagyományos SQL-tranzakció, tranzakciók keresztül a Phoenix-kezelő lehetővé teszik annak érdekében, hogy az adatok egy atomi egység sikeresen upserted, a tranzakció visszaállítása, ha az upsert művelet meghiúsul, bármely tranzakció-kompatibilis táblán.

Engedélyezheti a Phoenix tranzakciók a [Apache Phoenix tranzakció dokumentáció](http://phoenix.apache.org/transactions.html).

Hozzon létre egy új táblát engedélyezett tranzakciók, állítsa be a `TRANSACTIONAL` tulajdonságot `true` a egy `CREATE` utasítást:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Egy meglévő tábla kell tranzakciós módosítható, használja a tulajdonságok egy `ALTER` utasítást:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Tranzakciós tábla nem válthat vissza a nem tranzakcióalapú folyamatban.

### <a name="salted-tables"></a>Sózott táblák

*Régió kiszolgáló hotspotting* előfordulhatnak, ha a rekordok szekvenciális kulcsok az adatraktárba történő írás során HBase. Bár előfordulhat, hogy a több régióbeli kiszolgálók a fürtben, az írási vannak az összes folyamatban lévő csupán egyet. Az összefoglalás hoz létre a hotspotting probléma, ahol az írási számítási feladatok folyamatban van elosztva minden elérhető régió kiszolgálót helyett csak az egyik van terhelés kezelésére. Mivel minden egyes régió rendelkezik egy előre meghatározott maximális méretét, amikor egy régió eléri a maximális mérete, kis két régiójában van felosztva. Ha ez történik, ezek új régiókban tartózkodó új forgalmas váljon az összes új rekord vesz igénybe.

Ez a probléma megoldásához, és jobb teljesítményt, előre felosztása táblákat, hogy az összes régióban kiszolgálón egyaránt használhatók. A Phoenix biztosít *táblák sózott*, átlátható módon a sózás bájtot ad hozzá a sorkulcs egy adott tábla. A tábla nem előre felosztása a védőérték bájt határokon belül egyenlő terheléselosztást régióbeli kiszolgálók közül az első fázisban a tábla biztosítása érdekében. Ezt a módszert az írási számítási feladatok elosztja az összes rendelkezésre álló terület kiszolgáló javítása az írási és olvasási teljesítménye. Egy tábla só, adja meg a `SALT_BUCKETS` tulajdonság táblából, ha a tábla jön létre:

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

## <a name="enable-and-tune-phoenix-with-apache-ambari"></a>Engedélyezze, és az Ambari az Apache Phoenix hangolása

HDInsight HBase-fürt tartalmazza a [Ambari felhasználói felületén](hdinsight-hadoop-manage-ambari.md) konfigurációs módosítások.

1. Engedélyezheti vagy tilthatja le a Phoenix, és szabályozhatja a Phoenix a lekérdezés időkorlátja beállításait, jelentkezzen be az Ambari webes Kezelőfelületen (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) Hadoop-felhasználó hitelesítő adataival.

2. Válassza ki **HBase** szolgáltatások a bal oldali menüben, majd válassza ki a listáról a **Configs** fülre.

    ![Az Ambari HBase-config](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Keresse meg a **Phoenix SQL** engedélyezheti vagy letilthatja a phoenix és állítsa be a lekérdezés időkorlátja a konfigurációs szakaszban.

    ![Az Ambari Phoenix SQL-konfigurációs szakasz](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Lásd még

* [Az Apache Phoenix használata a HDInsight Linux-alapú HBase-fürtök](hbase/apache-hbase-phoenix-squirrel-linux.md)
