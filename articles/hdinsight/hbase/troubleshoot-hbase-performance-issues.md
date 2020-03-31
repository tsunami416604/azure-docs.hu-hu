---
title: Az Apache HBase teljesítményproblémáinak elhárítása az Azure HDInsightban
description: Az Apache HBase teljesítményhangolási irányelvei és az Azure HDInsight optimális teljesítményének eléréséhez adott tippek.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887155"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Az Apache HBase teljesítményproblémáinak elhárítása az Azure HDInsightban

Ez a cikk az Apache HBase teljesítményhangolási irányelveit ismerteti, és tippeket az Azure HDInsight optimális teljesítményéhez. Sok ilyen tippek függ az adott számítási feladatok és az olvasási/írási/beolvasási minta. Mielőtt konfigurációs módosításokat alkalmazna egy éles környezetben, alaposan tesztelje őket.

## <a name="hbase-performance-insights"></a>HBase teljesítményelemzések

A legtöbb HBase-munkaterhelés ben a legnagyobb szűk keresztmetszet a Write Ahead Log (WAL). Ez súlyosan befolyásolja az írási teljesítményt. A HDInsight HBase külön tárolási számítási modellel rendelkezik. Az adatok tárolása távolról történik az Azure Storage-ban, még akkor is, ha a virtuális gépek üzemeltetik a régiókiszolgálókat. Egészen a közelmúltig a WAL is írt az Azure Storage.Until recently, the WAL was also written to Azure Storage. A HDInsightban ez a viselkedés felerősítette ezt a szűk keresztmetszetet. A [Gyorsított írások](./apache-hbase-accelerated-writes.md) funkciót a probléma megoldására tervezték. A WAL-t az Azure Premium SSD által felügyelt lemezekre írja. Ez óriási előnyökkel jár az írási teljesítmény, és ez segít számos problémát, amellyel az írás-igényes számítási feladatok.

Az olvasási műveletek jelentős javulásához használja [a prémium szintű blokkblob-tárolófiókot](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) távtárolóként. Ez a beállítás csak akkor lehetséges, ha a WAL funkció engedélyezve van.

## <a name="compaction"></a>Tömörítés

A tömörítés egy másik lehetséges szűk keresztmetszet, amelyről alapvetően megállapodnak a közösségben. Alapértelmezés szerint a nagyobb tömörítés le van tiltva a HDInsight HBase-fürtökön. A tömörítés le van tiltva, mert annak ellenére, hogy erőforrás-igényes folyamatról van szó, az ügyfelek teljes rugalmassággal ütemezhetik a munkaterhelésük nek megfelelően. Például előfordulhat, hogy a menetrend, hogy a csúcsidőn kívül. Emellett az adatok helysége nem jelent problémát, mert a tárhely távoli (az Azure Storage által támogatott) helyett a helyi Hadoop elosztott fájlrendszer (HDFS).

Az ügyfeleknek a nagyobb tömörítést saját kényelmük szerint kell ütemezniük. Ha nem teszik meg ezt a karbantartást, a tömörítés hosszú távon hátrányosan befolyásolja az olvasási teljesítményt.

A bekéselt műveletek esetén a 100 ezredmásodpercnél jóval magasabb átlagos késések aggodalomra adnak okot. Ellenőrizze, hogy a nagyobb tömörítés pontosan van-e ütemezve.

## <a name="apache-phoenix-workload"></a>Apache Phoenix munkaterhelés

A következő kérdések megválaszolása segít jobban megérteni az Apache Phoenix munkaterhelését:

* Az összes "olvasás" lefordítva vizsgál?
    * Ha igen, mik a jellemzői ezeknek a vizsgálatoknak?
    * Optimalizálta a Phoenix táblasémáját ezekre a vizsgálatokra, beleértve a megfelelő indexelést is?
* Használta az `EXPLAIN` utasítást a "olvasási" lekérdezési tervek megértéséhez?
* Az írásaid "upsert-selects"?
    * Ha igen, akkor ők is csinál vizsgál. Várható késés a vizsgál átlagosan körülbelül 100 ezredmásodperc, szemben a 10 ezredmásodperc pont bekerül a HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Vizsgálati módszertan és mérőszámok nyomon követése

Ha ön használ referenciaértékek, mint a Yahoo! Cloud szolgáló Benchmark, JMeter, vagy Pherf a teljesítmény teszteléséhez és hangolásához, győződjön meg arról, hogy:

- Az ügyfélgépek nem válnak szűk keresztmetszetté. Ehhez ellenőrizze a CPU-használat az ügyfélgépeken.

- Az ügyféloldali konfigurációk, például a szálak száma, megfelelően vannak hangolva az ügyfél sávszélességének telítéséhez.

- A vizsgálati eredményeket pontosan és szisztematikusan rögzítik.

Ha a lekérdezések hirtelen sokkal rosszabbul kezdtek el, mint korábban, ellenőrizze, hogy vannak-e lehetséges hibák az alkalmazáskódjában. Hirtelen nagy mennyiségű érvénytelen adatot generál? Ha ez így van, növelheti az olvasási késéseket.

## <a name="migration-issues"></a>Migrációs problémák

Ha az Azure HDInsightba migrál, győződjön meg arról, hogy az áttelepítés szisztematikusan és pontosan történik, lehetőleg automatizálással. Kerülje a manuális áttelepítést. Ellenőrizze a következőket:

- A táblaattribútumok áttelepítése pontosan történik. Az attribútumok tartalmazhatnak tömörítést, virágzási szűrőket és így tovább.

- A Főnix-táblák sózási beállításai megfelelően vannak leképezve az új fürtmérethez. A sógyűjtők számának például a fürtben lévő munkavégző csomópontok többszörösének kell lennie. És olyan többszöröst kell használnia, amely a forró pecsételő mennyiség tényezője.

## <a name="server-side-configuration-tunings"></a>Kiszolgálóoldali konfigurációs hangolások

A HDInsight HBase alkalmazásban a HFiles a távtárolón tárolódik. Ha van egy gyorsítótár-hiba, az olvasási költség magasabb, mint a helyszíni rendszerek, mert a helyszíni rendszereken lévő adatok at a helyi HDFS támogatja. A legtöbb esetben a HBase-gyorsítótárak (blokk-gyorsítótár és gyűjtőgyorsítótár) intelligens használatát úgy tervezték, hogy megkerülje ezt a problémát. Azokban az esetekben, ahol a probléma nem kerülik meg, egy prémium szintű blokk blob fiók használata segíthet a probléma. A Windows Azure Storage Blob illesztőprogramja `fs.azure.read.request.size` bizonyos tulajdonságokra támaszkodik, például az adatok lekérése blokkokban az alapján, hogy mit állapít meg az olvasási mód (szekvenciális és véletlenszerű), így előfordulhat, hogy továbbra is nagyobb késések olvasások példányai. Empirikus kísérletek, azt találtuk, hogy az olvasási kérelem blokk méretének`fs.azure.read.request.size`() 512 KB-ra állítása és a HBase-táblák blokkméretének azonos méretűre állítása a gyakorlatban a legjobb eredményt eredményezi.

A legtöbb nagy méretű csomópont-fürt esetében a `bucketcache` HDInsight HBase fájlként szolgál a virtuális géphez csatlakoztatott helyi `regionservers`prémium szintű SSD-n, amely fut. A halommemória-gyorsítótár használata némi javulást eredményezhet. Ez a megoldás a rendelkezésre álló memória használatát és a fájlalapú gyorsítótárnál kisebb méretűvé teszi a lehetőséget, ezért nem mindig a legjobb választás.

Az alábbiakban néhány más konkrét paramétereket, hogy mi hangolt, és úgy tűnt, hogy segítsen különböző mértékben:

- Növelje `memstore` a méretet az alapértelmezett 128 MB-ról 256 MB-ra. Ez a beállítás általában nehéz írási esetekben ajánlott.

- Növelje a tömörítésre kijelölt szálak számát az **1-es** alapértelmezett beállításról **4-re.** Ez a beállítás akkor fontos, ha gyakori kisebb műveleteket észlelünk.

- Kerülje `memstore` a kiürítés blokkolását a bolti korlát miatt. A puffer biztosításához `Hbase.hstore.blockingStoreFiles` növelje a beállítást **100-ra.**

- Az öblítések vezérléséhez használja az alábbi beállításokat:

    - `Hbase.regionserver.maxlogs`: **140** (elkerüli a flusheket a WAL-határértékek miatt)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-specifikus konfigurációk szálkészlet-hangoláshoz:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Egyéb Phoenix-specifikus konfigurációk:

    - `Phoenix.rpc.index.handler.count`: **50** (ha nagy vagy sok index-keres)

    - `Phoenix.stats.updateFrequency`: **1 óra**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 óra**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC időkiállások: **3 perc**

   - Az RPC időmeghosszabbításai közé tartozik a HBase RPC időmeghosszabbítása, a HBase ügyfélolvasó időmeghosszabbítása és a Főnix lekérdezés időmeghosszabbítása. 
   - Győződjön meg `hbase.client.scanner.caching` arról, hogy a paraméter a kiszolgáló és az ügyfél végén is ugyanarra az értékre van beállítva. Ha nem azonosak, ez a beállítás ügyfél-végponti `OutOfOrderScannerException`hibákhoz vezet, amelyek a alkalmazáshoz kapcsolódnak. Ezt a beállítást alacsony értékre kell állítani a nagy beolvasások esetén. Ezt az értéket **100-ra**állítottuk.

## <a name="other-considerations"></a>Egyéb szempontok

A következő további paramétereket kell figyelembe venni a hangolás:

- `Hbase.rs.cacheblocksonwrite`– alapértelmezés szerint a HDI, ez a beállítás értéke **igaz**.

- Olyan beállítások, amelyek lehetővé teszik a kisebb tömörítés későbbi elhalasztását.

- Kísérleti beállítások, például az olvasási és írási kérelmek számára fenntartott várólisták százalékos arányának módosítása.

## <a name="next-steps"></a>További lépések

Ha a probléma továbbra is megoldatlan marad, további támogatásért látogasson el az alábbi csatornák egyikére:

- Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

- Kapcsolat [@AzureSupport](https://twitter.com/azuresupport)a segítségével. Ez a hivatalos Microsoft Azure-fiók az ügyfélélmény javítása érdekében. Összeköti az Azure-közösséget a megfelelő erőforrásokkal: válaszokkal, támogatással és szakértőkkel.

- Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) A Microsoft Azure-előfizetés hozzáférést biztosít az előfizetés-kezelési és számlázási támogatáshoz, és technikai támogatást nyújt az Azure egyik [támogatási csomagján](https://azure.microsoft.com/support/plans/)keresztül.
