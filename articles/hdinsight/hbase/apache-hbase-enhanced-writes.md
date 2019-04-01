---
title: Az Azure HDInsight fokozott írja az Apache hbase (előzetes verzió)
description: Áttekintést ad az Azure HDInsight fokozott ír funkció, amely az Apache HBase írási előre napló teljesítményének növelése prémium szintű felügyelt lemezek használja.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: 37e6f316a5202429396ddd2a31cb14fe61341e89
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759486"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Az Azure HDInsight fokozott írja az Apache hbase (előzetes verzió)

Ez a cikk ismerteti a háttérben lévő a **fokozott ír** Apache hbase az Azure HDInsight szolgáltatást, és hogyan használat hatékonyan javítása írási teljesítmény. **Írási műveletek továbbfejlesztett** használ [Azure prémium szintű SSD managed disks](../../virtual-machines/linux/disks-types.md#premium-ssd) , az Apache HBase írási előre Log (WAL) a teljesítmény javítása. Az Apache HBase kapcsolatos további információkért lásd: [Mi az Apache HBase a HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>A HBase-architektúra áttekintése

A HBase a **sor** áll egy vagy több **oszlopok** azonosíthatók, és egy **sorkulcs**. Több sor alkotó egy **tábla**. Oszlopok tartalmaznak **cellák**, melyek adott oszlopban lévő időbélyegzővel verzióit. Oszlopok szerint vannak csoportosítva **oszlopcsaláddal**, és az összes oszlopa egy oszlopcsalád-fájlokban van tárolva együtt tárolási nevű **HFiles**.

**Régiók** a HBase segítségével osztja el az adatok feldolgozási terhelést. HBase először tárolja a sorokat a tábla egy adott régióban, és ezután terjeszti a sorok több régióban, a tábla növeli az adatok mennyisége. **Régiókiszolgálók** kezelheti a több régióra vonatkozó kéréseket.

## <a name="write-ahead-log-for-apache-hbase"></a>Előre írt napló Apache hbase

Adatfrissítések, a HBase első írt egy írási című lépéssel Log (WAL) nevű véglegesítési napló típusa. Miután a frissítés a WAL tárolva van, a memóriabeli írás **kapott**. Amikor az adatokat a memóriában eléri a maximális kapacitást, mint a lemezre írás- **HFile**.

Ha egy **RegionServer** összeomlik, vagy nem érhető el a kapott ki van ürítve, mielőtt az írási című lépéssel napló visszajátszani frissítéseket is használható. Nélkül a WAL Ha egy **RegionServer** összeomlott előtt írja az frissítéseit egy **HFile**, elvesznek ezek a frissítések mindegyikét.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Az Azure HDInsight az Apache HBase továbbfejlesztett írások szolgáltatása

A fokozott ír funkció megoldja a problémát, a nagyobb írási-késése okozott írási című lépéssel naplókban, amelyek felhőalapú tárolás használatával.  A HDInsight Apache HBase-fürtök a fokozott ír funkció a nevű, prémium szintű SSD felügyelt lemezek minden RegionServer (feldolgozó csomópontot). Az írási előre naplók majd írták, a Hadoop fájlrendszer (HDFS) csatlakoztatva a felhőalapú tárolás helyett a prémium szintű felügyelt lemezek.  Prémium szintű felügyelt lemezek Solid-State lemezek (SSD) használja, és remek i/o-teljesítményt kínál a hibatűréssel.  Ellentétben nem felügyelt lemezek Ha egy tárolási egység leáll, ez nem lesz hatással más tárolási egységeket azonos rendelkezésre állási csoportban.  Ennek eredményeképpen a felügyelt lemezek írási-késést és nagyobb rugalmasság az alkalmazások számára biztosítanak. További tudnivalók az Azure managed disks használatát, lásd: [Bevezetés az Azure-ba, felügyelt lemezek](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>A HDInsight HBase fokozott ír engedélyezése

Hozzon létre egy új HBase fürtöt a fokozott ír funkcióval, kövesse a [állítsa be a HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md) addig **3. lépésben, a tárhely**. A **Metaadattár beállításai**, jelölje be a jelölőnégyzetet a **engedélyezése (előzetes verzió) fokozott ír**. Ezt követően folytassa a hátralévő lépéseket a fürt létrehozásához.

![Engedélyezze a fokozott írási lehetőség a HDInsight Apache hbase-hez](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

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

* A hivatalos Apache HBase-dokumentáció a [előre napló írási funkció](https://hbase.apache.org/book.html#wal).