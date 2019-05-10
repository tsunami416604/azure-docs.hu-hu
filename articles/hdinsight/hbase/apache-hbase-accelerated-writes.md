---
title: A gyorsított írási műveletek az Azure HDInsight az Apache HBase
description: Áttekintést ad az Azure HDInsight gyorsított ír funkció, amely az Apache HBase írási előre napló teljesítményének növelése prémium szintű felügyelt lemezek használja.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 219899c2e336f544ff6572589cc79f84f555490d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233835"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>A gyorsított írási műveletek az Azure HDInsight az Apache HBase

Ez a cikk ismerteti a háttérben lévő a **gyorsított ír** Apache hbase az Azure HDInsight szolgáltatást, és hogyan használat hatékonyan javítása írási teljesítmény. **A gyorsított írások** használ [Azure prémium szintű SSD managed disks](../../virtual-machines/linux/disks-types.md#premium-ssd) , az Apache HBase írási előre Log (WAL) a teljesítmény javítása. Az Apache HBase kapcsolatos további információkért lásd: [Mi az Apache HBase a HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>A HBase-architektúra áttekintése

A HBase a **sor** áll egy vagy több **oszlopok** azonosíthatók, és egy **sorkulcs**. Több sor alkotó egy **tábla**. Oszlopok tartalmaznak **cellák**, melyek adott oszlopban lévő időbélyegzővel verzióit. Oszlopok szerint vannak csoportosítva **oszlopcsaláddal**, és az összes oszlopa egy oszlopcsalád-fájlokban van tárolva együtt tárolási nevű **HFiles**.

**Régiók** a HBase segítségével osztja el az adatok feldolgozási terhelést. HBase először egy adott régióban tárolja a tábla sorait. A sorok vannak elosztva több régióban, mint a tábla növeli az adatok mennyisége. **Régiókiszolgálók** kezelheti a több régióra vonatkozó kéréseket.

## <a name="write-ahead-log-for-apache-hbase"></a>Előre írt napló Apache hbase

HBase először ír adatokat. egy írási című lépéssel Log (WAL) nevű véglegesítési naplótípus. Miután a frissítés a WAL tárolva van, a memóriabeli írás **kapott**. Amikor az adatokat a memóriában eléri a maximális kapacitást, mint a lemezre írás- **HFile**.

Ha egy **RegionServer** összeomlik, vagy nem érhető el a kapott ki van ürítve, mielőtt az írási című lépéssel napló visszajátszani frissítéseket is használható. A WAL nélkül Ha egy **RegionServer** előtt írja az frissítései összeomlik egy **HFile**, ezek a frissítések összes elveszett.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Az Azure HDInsight az Apache HBase gyorsított írások szolgáltatása

A gyorsított ír funkció megoldja a problémát, a nagyobb írási-késése okozott írási című lépéssel naplókban, amelyek felhőalapú tárolás használatával.  A gyorsított ír funkció HDInsight az Apache hbase fürtök, rendeli prémium szintű SSD-managed disksbe történő minden RegionServer (feldolgozó csomópontot). Az írási előre naplók majd írták, a Hadoop fájlrendszer (HDFS) csatlakoztatva van a prémium szintű managed disksen – felhőalapú tárolás helyett.  Prémium szintű managed disks Solid-State lemezek (SSD) használja, és remek i/o-teljesítményt kínál a hibatűréssel.  Ellentétben nem felügyelt lemezek Ha egy tárolási egység leáll, ez nem lesz hatással más tárolási egységeket azonos rendelkezésre állási csoportban.  Ennek eredményeképpen a felügyelt lemezek adja meg, írási-késést és nagyobb rugalmasság alkalmazásai számára. Azure által felügyelt lemezekkel kapcsolatos további tudnivalókért lásd: [Bevezetés az Azure-ba, felügyelt lemezek](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>A gyorsított ír a HDInsight HBase engedélyezése

Hozzon létre egy új HBase fürtöt a gyorsított ír funkcióval, kövesse a [állítsa be a HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md) addig **3. lépésben, a tárhely**. A **Metaadattár beállításai**, jelölje be a jelölőnégyzetet a **ír (előzetes verzió) engedélyezése a gyorsított**. Ezt követően folytassa a hátralévő lépéseket a fürt létrehozásához.

![Engedélyezze a gyorsított írási lehetőség a HDInsight Apache hbase-hez](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Egyéb szempontok

Megőrizheti az adatok tartós megőrzését, hozzon létre egy fürt legalább három feldolgozó csomópontot. Létrehozása után nem csökkentheti a fürt legalább három feldolgozó csomópontot.

Kiürítési, vagy tiltsa le a HBase-táblákat a fürt törlése előtt, hogy ne veszítse el írási című lépéssel naplóadatokat.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>További lépések

* A hivatalos Apache HBase-dokumentáció a [előre napló írási funkció](https://hbase.apache.org/book.html#wal)
* Gyorsított ír használatára a HDInsight az Apache HBase fürt frissítéséhez lásd [Apache HBase-fürt áttelepítése új verzióra](apache-hbase-migrate-new-version.md).
