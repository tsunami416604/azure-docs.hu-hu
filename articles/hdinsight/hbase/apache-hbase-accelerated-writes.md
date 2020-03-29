---
title: Azure HDInsight-beli gyorsított írási műveletek Apache HBase-hez
description: Áttekintést nyújt az Azure HDInsight gyorsított írási funkciójáról, amely prémium szintű felügyelt lemezeket használ az Apache HBase írási előrenapló teljesítményének javítása érdekében.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764584"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight-beli gyorsított írási műveletek Apache HBase-hez

Ez a cikk az Apache HdInsight **accelerated Writes** funkciójának hátterét, valamint az írási teljesítmény hatékony használatát ismerteti. **Az Accelerated Writes** [az Azure prémium szintű SSD-felügyelt lemezeit](../../virtual-machines/linux/disks-types.md#premium-ssd) használja az Apache HBase Írási előrenapló (WAL) teljesítményének javítása érdekében. Ha többet szeretne megtudni az Apache HBase-ről, olvassa el [a Mi az Apache HBase a HDInsight ban.](apache-hbase-overview.md)

## <a name="overview-of-hbase-architecture"></a>A HBase architektúra áttekintése

A HBase alkalmazásban egy **sor** egy vagy több **oszlopból** áll, és egy **sorkulcs**azonosítja. Több sor alkotja a **táblázatot**. Az oszlopok **olyan cellákat**tartalmaznak , amelyek az oszlopban lévő érték időbélyeggel ellátott változatai. Az oszlopok **oszlopcsaládokba**vannak csoportosítva, és az oszlopcsalád összes oszloposzlopa együtt tárolódik a **HFiles**nevű tárolófájlokban.

A HBase **régiói** az adatfeldolgozási terhelés kiegyenlítésére szolgálnak. A HBase először egy tábla sorait tárolja egyetlen régióban. A sorok több régióban oszlanak meg, ahogy a táblázatban szereplő adatok mennyisége nő. **A régiókiszolgálók** több régióra vonatkozó kéréseket is képesek kezelni.

## <a name="write-ahead-log-for-apache-hbase"></a>Előre napló írása az Apache HBase-hez

A HBase először adatfrissítéseket ír egy olyan típusú véglegesítési naplóba, amelyet előre írási naplónak (WAL) neveznek. Miután a frissítés a WAL-ban tárolódik, a rendszer a memóriában lévő **MemStore -ba**kerül. Amikor a memóriában lévő adatok elérik a maximális kapacitást, a rendszer HFile fájlként írja a **lemezre.**

Ha egy **RegionServer** összeomlik, vagy elérhetetlenné válik a MemStore kiürítése előtt, a Frissítések újralejátszására használható az Előreírási napló. A WAL nélkül, ha egy **RegionServer** összeomlik a **HFile**frissítéseinek kiürítése előtt, az összes frissítés elveszik.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Gyorsított írási funkció az Apache HBase-hez való Azure HDInsight ban

A Gyorsított írási funkció megoldja a felhőalapú tárolóban lévő Előre írási naplók használatával okozott magasabb írási késések problémáját.  A HDInsight Apache HBase fürtök gyorsított írási szolgáltatása prémium szintű SSD-vel kezelt lemezeket csatlakoztat minden RegionServer (feldolgozócsomópont) számára. A "Előre" naplóírásezési naplók ezután a Felhőalapú tárolás helyett ezekre a prémium szintű felügyelt lemezekre vannak csatlakoztatva a Hadoop fájlrendszerbe (HDFS).  A prémium szintű felügyelt lemezek szilárdtest-lemezeket (SSD- k) használnak, és kiváló I/O-teljesítményt és hibatűrést kínálnak.  A nem felügyelt lemezektől eltérően, ha egy tárolóegység leáll, az nem lesz hatással az azonos rendelkezésre állási csoport többi tárolóegységére.  Ennek eredményeképpen a felügyelt lemezek alacsony írási késleltetést és jobb rugalmasságot biztosítanak az alkalmazások számára. Az Azure által felügyelt lemezekről a Bevezetés az Azure által felügyelt lemezekbe című témakörben olvashat [bővebben.](../../virtual-machines/windows/managed-disks-overview.md)

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>A HBase gyorsított írásai engedélyezése a HDInsightban

Ha új HBase-fürtöt szeretne létrehozni a Gyorsított írások szolgáltatással, kövesse a Fürtök beállítása a [HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md) című részben leírt lépéseket, amíg el nem éri **a 3.** A **Metastore-beállítások csoportban**jelölje be a **HBase gyorsított írásengedélyezése**jelölőnégyzet jelölését. Ezután folytassa a fürt létrehozásának további lépéseit.

![Gyorsított írási beállítások engedélyezése a HDInsight Apache HBase-hez](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Egyéb szempontok

Az adatok tartósságának megőrzése érdekében hozzon létre egy fürtöt legalább három munkavégző csomókkal. Létrehozása után nem skálázhatja le a fürtöt háromnál kevesebb munkavégző csomópontra.

A fürt törlése előtt ürítse ki vagy tiltsa le a HBase-táblákat, hogy ne veszítse el az Előretolási napló adatait.

```
flush 'mytable'
```

```
disable 'mytable'
```

Kövesse a hasonló lépéseket a fürt leskálázásakor: ürítse ki a táblákat, és tiltsa le a táblákat a bejövő adatok leállításához. A fürt nem skálázható le három csomópontnál kevesebbre.

Az alábbi lépések végrehajtásával biztosítható a sikeres leskálázás, és elkerülheti annak lehetőségét, hogy egy névcsomópont csökkentett módban legyen az alulreplikált vagy ideiglenes fájlok miatt.

Ha a namenode nem megy safemode után egy skálázás le, hdfs parancsok segítségével re-replikálása az alulreplikált blokkokat, és kap hdfs ki a csökkentett módban. Ez az újrareplikáció lehetővé teszi a HBase sikeres újraindítását.

## <a name="next-steps"></a>További lépések

* Hivatalos Apache HBase dokumentáció az [Előretigó napló szolgáltatásban](https://hbase.apache.org/book.html#wal)
* A HDInsight Apache HBase fürt gyorsíráshasználatára való frissítéséhez olvassa el Az [Apache HBase-fürt áttelepítése új verzióra](apache-hbase-migrate-new-version.md)című témakört.
