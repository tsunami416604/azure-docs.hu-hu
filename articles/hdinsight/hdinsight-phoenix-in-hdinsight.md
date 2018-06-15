---
title: A HDInsight - az Azure HDInsight Apache Phoenix |} Microsoft Docs
description: ''
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 5d96b5656881815a82c89e0d159ba2bf556946b9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407731"
---
# <a name="apache-phoenix-in-hdinsight"></a>Apache Phoenix a HDInsightban

[Apache Phoenix](http://phoenix.apache.org/) egy nyílt forráskódú, épülő nagymértékben párhuzamos relációs Adatbázisréteg [HBase](hbase/apache-hbase-overview.md). Phoenix lehetővé teszi az SQL-szerű lekérdezéseket használatát a HBase-en. Phoenix felhasználók létrehozása, törlés, az alter SQL táblák, indexek, nézetek és sorozatok és upsert sorok egyesével és csoportosan alatt JDBC illesztőprogramokat használja. Phoenix használ fordítása a lekérdezések MapReduce használata helyett natív noSQL-fordítási engedélyezése a HBase felett kis késleltetésű alkalmazások létrehozását. Phoenix hozzáadja a közösen elhelyezett adatokkal kód végrehajtása coprocessors történő ügyfél által megadott kód futtatásához a kiszolgáló, a címterületen belülre. Ez a megközelítés minimálisra ügyfél/kiszolgáló adatátvitel.

Apache Phoenix megnyílik a big Data típusú adatok nem-fejlesztők számára is szintaxissal egy SQL-szerű lekérdezéseket programozási helyett. Phoenix optimalizálhatja a HBase, más eszközök eltérően például [Hive](hadoop/hdinsight-use-hive.md) és Spark SQL. A fejlesztők előnye magas ír performant lekérdezések sokkal kevesebb kóddal.
<!-- [Spark SQL](spark/apache-spark-sql-with-hdinsight.md)  -->

Egy SQL-lekérdezés elküldésekor Phoenix lefordítja a HBase natív hívás lekérdezése pedig a vizsgálat (vagy terv) optimális párhuzamosan. Absztrakciós réteg szabadít fel a MapReduce-feladatok írásában fejlesztő hangsúlyozza inkább az üzleti logika és a munkafolyamat a Phoenix a big Data típusú adatok tároló-alkalmazását.

## <a name="query-performance-optimization-and-other-features"></a>Lekérdezés teljesítmény optimalizálása és más szolgáltatások

Apache Phoenix számos fejlesztést és szolgáltatások hozzáadása HBase lekérdezések.

### <a name="secondary-indexes"></a>Másodlagos indexek

A HBase van, amely a sor elsődleges kulcs lexicographically rendezve egyetlen indexe. Ezeket a rekordokat csak a sor kulcsán keresztül érhető el. Rekordok el az oszlopot a sorkulcs eltérő szükséges vizsgálatát az összes adat szükséges a szűrő alkalmazása közben. A másodlagos indexnél oszlopokat vagy kifejezéseket, amelyek indexelt űrlap egy másik sorkulcsa, így keresések és a tartomány vizsgálja meg, hogy az index.

A másodlagos indexet létrehozni a `CREATE INDEX` parancs:

```sql
CREATE INDEX ix_purchasetype on SALTEDWEBLOGS (purchasetype, transactiondate) INCLUDE (bookname, quantity);
```

Ezt a módszert használja a jelentős teljesítmény növelését partíciónként akár egyetlen indexelt lekérdezések végrehajtása során. Ez a másodlagos index típus egy **index kiterjedő**, az összes a lekérdezésben szereplő oszlopokat. Ezért a táblában való keresés nincs szükség, és az index a teljes lekérdezés eleget tesz.

### <a name="views"></a>Nézetek

Phoenix nézetek biztosít, amelyek egy HBase korlátozás, ahol a teljesítmény romlásához vezethetnek körülbelül 100-nál több fizikai táblák létrehozásakor kezdődik módot. Phoenix nézetek lehetővé teszik több *virtuális táblák* megosztani egy alapul szolgáló fizikai HBase tábla.

A Phoenix nézetek létrehozásával hasonlít szabványos SQL-nézet szintaxis használatával. Egy különbség, hogy meghatározhatja a nézet mellett az öröklődés forrása: az alaptáblában oszlopok oszlopok. Azt is megteheti az új `KeyValue` oszlopok.

Például ez nevű fizikai tábla `product_metrics` a következő definícióját:

```sql
CREATE  TABLE product_metrics (
    metric_type CHAR(1),
    created_by VARCHAR, 
    created_date DATE, 
    metric_id INTEGER
    CONSTRAINT pk PRIMARY KEY (metric_type, created_by, created_date, metric_id));
```

Nézet definiálása keresztül rendelkező további oszlopok ebben a táblázatban:

```sql
CREATE VIEW mobile_product_metrics (carrier VARCHAR, dropped_calls BIGINT) AS
SELECT * FROM product_metrics
WHERE metric_type = 'm';
```

Több oszlop később hozzáadásához használja a `ALTER VIEW` utasítást.

### <a name="skip-scan"></a>Kihagyás vizsgálat

Kihagyás ellenőrzés egy vagy több oszlop egy összetett index különböző értékeket használja. A tartomány vizsgálat eltérően skip vizsgálat megvalósítja az belüli soron kívüli vizsgálatát, ad eredményt a következő [növeli a teljesítményt](http://phoenix.apache.org/performance.html#Skip-Scan). Vizsgálatát, miközben az első egyező értéket a rendszer kihagyja az index együtt amíg meg nem találja a következő érték.

Használja a skip vizsgálat a `SEEK_NEXT_USING_HINT` a HBase szűrő enumerálása. Használatával `SEEK_NEXT_USING_HINT`, a skip vizsgálat nyomon követi, hogy mely kulcsok készlete vagy tartományok kulcsok, minden egyes oszlopának a rendszer keres. A skip vizsgálata, majd szűrő kiértékelése közben átadott, és határozza meg, hogy egyik kombinációja kulcsa vesz igénybe. Ha nem, a skip vizsgálat értékeli ki a következő legnagyobb kulcs ugorhat.

### <a name="transactions"></a>Tranzakciók

HBase biztosít sorszintű tranzakciók, amíg Phoenix integrálható [Tephra](http://tephra.io/) cross-sor- és Kereszttábla tranzakció támogatását teljes [sav](https://en.wikipedia.org/wiki/ACID) szemantikáját.

Mint hagyományos SQL tranzakciókkal, a Phoenix tranzakciókezelő keresztül elérhető tranzakciók teszik lehetővé, így az adatok egy atomi egység sikeresen upserted, a tranzakció visszaállítása, ha a upsert művelete sikertelen, bármely tranzakció engedélyezett táblán.

Ahhoz, hogy a Phoenix tranzakciók, tekintse meg a [Apache Phoenix tranzakció dokumentáció](http://phoenix.apache.org/transactions.html).

Hozzon létre egy új táblát tranzakciók engedélyezett, állítsa be a `TRANSACTIONAL` tulajdonságot `true` a egy `CREATE` utasítást:

```sql
CREATE TABLE my_table (k BIGINT PRIMARY KEY, v VARCHAR) TRANSACTIONAL=true;
```

Módosítja a meglévő tábla lennie, használja az ugyanahhoz a tulajdonsághoz az `ALTER` utasítást:

```sql
ALTER TABLE my_other_table SET TRANSACTIONAL=true;
```

> [!NOTE]
> Vissza a nem tranzakciós alatt nem lehet átállítani a tranzakciós tábla.

### <a name="salted-tables"></a>Sózott táblák

*Régió server hotspotting* akkor fordulhat elő, rögzíti a szekvenciális kulccsal rendelkező a HBase írásakor. Bár előfordulhat, hogy több régióban kiszolgáló a fürtön, az írási műveletek összes előforduló csak egy. Ez a koncentráció hoz létre a hotspotting problémát, a rendelkezésre álló terület kiszolgálók mindegyikének alatt álló elosztott írási terhelést, helyett csak egy van terhelés kezelésére. Mivel minden egyes régió egy előre meghatározott maximális méretét, amikor terület eléri a méretkorlátot, két kis részre oszlik. Ha ez történik, ezek új régiók egyikéhez sem új rekordok, az új interaktív terület váljon vesz igénybe.

A probléma mérséklése és a teljesítmény, előre vágási táblák, hogy az összes régió kiszolgálón egyaránt használhatók. Phoenix biztosít *táblák sózott*, transzparens módon a sózás bájtot ad hozzá a sorkulcs egy adott tábla. A tábla nem előre felosztása a védőérték bájt határokon belül egyenlő terheléseloszlását régió kiszolgálók között a kezdeti fázisában a tábla biztosításához. Ezt a módszert használja a írási munkaterhelés elosztása az összes rendelkezésre álló terület kiszolgálót javítása az írási és olvasási teljesítménye. Egy tábla só, adja meg a `SALT_BUCKETS` tábla tulajdonság, ha a tábla létrehozása:

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

## <a name="enable-and-tune-phoenix-with-ambari"></a>Engedélyezze és az Ambari Phoenix hangolása

HDInsight HBase-fürtöt tartalmaz a [Ambari felhasználói felület](hdinsight-hadoop-manage-ambari.md) konfigurációs módosítások.

1. Engedélyezheti vagy tilthatja le a Phoenix, valamint szabályozhatja a Phoenix a lekérdezés időtúllépési beállításai, jelentkezzen be az Ambari webes felhasználói felület (`https://YOUR_CLUSTER_NAME.azurehdinsight.net`) Hadoop-felhasználó hitelesítő adataival.

2. Válassza ki **HBase** szolgáltatások a bal oldali menüben, majd válassza ki a listáról a **Configs** fülre.

    ![Ambari HBase-konfiguráció](./media/hdinsight-phoenix-in-hdinsight/ambari-hbase-config.png)

3. Keresés a **Phoenix SQL** engedélyezze vagy tiltsa le a phoenix, és állítsa be a a lekérdezés időkorlátja a konfigurációs szakasz.

    ![Ambari Phoenix SQL konfigurációs szakasz](./media/hdinsight-phoenix-in-hdinsight/ambari-phoenix.png)

## <a name="see-also"></a>Lásd még

* [Hdinsight Linux-alapú HBase-fürtökkel Apache Phoenix használata](hbase/apache-hbase-phoenix-squirrel-linux.md)
