---
title: Az Apache HBase-teljesítménnyel kapcsolatos problémák elhárítása az Azure HDInsight
description: Különböző Apache HBase Performance tuning-irányelvek és tippek az optimális teljesítmény az Azure HDInsight-on való beszerzéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266652"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Az Apache HBase-teljesítménnyel kapcsolatos problémák elhárítása az Azure HDInsight

Ez a dokumentum a különböző Apache HBase teljesítmény-hangolási irányelveit és az Azure HDInsight optimális teljesítményének megkezdésére vonatkozó tippeket ismerteti. Ezek a tippek sok esetben az adott számítási feladattól, valamint az írási/olvasási/beolvasási mintáktól függenek. Éles környezetben való alkalmazása előtt alaposan tesztelje a konfigurációt.

## <a name="hdinsight-hbase-performance-insights"></a>HDInsight HBase teljesítmény-áttekintés

A legtöbb HBase-számítási feladat leggyakoribb szűk keresztmetszete az írási előre látható napló (WAL). Ez súlyosan befolyásolja az írási teljesítményt. A HDInsight HBase elválasztott tárolási modellje – vagyis az adatok tárolása távolról történik az Azure Storage-ban, de a régió-kiszolgálók a virtuális gépeken futnak. Egészen a közelmúltig az írási előre írt napló az Azure Storage-ba is bekerült, így a szűk keresztmetszetet a HDInsight esetében kiterjesztjük. A [gyorsított](./apache-hbase-accelerated-writes.md) írási funkció úgy van kialakítva, hogy megoldja ezt a problémát az Azure Premium SSD által felügyelt lemezekre való írás előtt. Ez nagy mértékben csökkenti a teljesítményt, és számos olyan problémát tesz lehetővé, amely a sok írási igényű munkaterheléssel szembesül.

A [Prémium szintű Block Blob Storage-fiókok](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) távoli tárhelyként való használatával jelentősen javíthatja az olvasási műveleteket. Ez a lehetőség csak akkor érhető el, ha az Előre írt naplók funkció engedélyezve van.

## <a name="compaction"></a>Tömörítés

A tömörítés egy másik potenciális szűk keresztmetszet, amely alapvetően megállapodik a Közösségben.  Alapértelmezés szerint a nagyobb tömörítés le van tiltva a HDInsight HBase-fürtökön. Ennek az az oka, hogy ez egy erőforrás-igényes folyamat, ezért teljes rugalmasságot szeretnénk biztosítani ügyfeleinknek a számítási feladatok jellemzőinek megfelelően, azaz a munkaidőn kívül. Mivel a tárhely távoli (az Azure Storage által támogatott) helyett a helyi HDFS, amely a legtöbb helyszíni példány esetében gyakori, az adatközpont nem aggodalomra ad okot, amely a főbb tömörítés elsődleges céljainak egyike.

Ennek feltételezése az, hogy az ügyfél gondoskodik a nagyobb tömörítésnek a kényelem alapján történő megadásáról. Ha ez a karbantartás nem történt meg, a tömörítés nagy mértékben hatással lesz az olvasási teljesítményre hosszú távon.

Különösen a vizsgálati műveletek esetében a 100 MS-nál sokkal nagyobb késésnek kell lennie, ami aggodalomra ad okot. Ellenőrizze, hogy a fő tömörítés ütemezése pontosan megtörtént-e.

## <a name="know-your-apache-phoenix-workload"></a>Apache Phoenix munkaterhelésének megismerése

A következő kérdések megválaszolásával jobban megismerheti az Apache Phoenix munkaterhelést:

* A rendszer az összes "olvasást" lefordítja a vizsgálatokba?
    * Ha igen, Mik a vizsgálatok jellemzői?
    * Optimalizálta a Phoenix Table sémáját ezekhez a vizsgálatokhoz, beleértve a megfelelő indexelést is?
* Használta a `EXPLAIN` utasítást, hogy megértse a "olvasások" létrehozásához szükséges lekérdezési terveket.
* Az írásai "upsert – kiválasztja"?
    * Ha igen, akkor is végeznek vizsgálatokat. A keresések várható késése átlagosan 100 MS, a HBase pedig a ponthoz képest 10 ms.  

## <a name="test-methodology-and-metrics-monitoring"></a>Tesztelési módszertan és metrikák figyelése

Ha olyan teljesítménymutatókat használ, mint például a YCSB, a JMeter vagy a Pherf a Perf teszteléséhez és finomhangolásához, ügyeljen a következőkre:

1. Az ügyfélgépek nem lesznek szűk keresztmetszetek (a CPU-használat ellenőrzését az ügyfélgépeken).

1. Az ügyféloldali konfigurációk – például a szálak száma stb. – megfelelően vannak hangolva az ügyfelek sávszélességének csökkentése érdekében.

1. A tesztek eredményei pontosan és szisztematikusan vannak rögzítve.

Ha a lekérdezések váratlanul sokkal rosszabbak voltak, mint korábban – a lehetséges hibák keresése az alkalmazás kódjában – hirtelen nagy mennyiségű érvénytelen adatmennyiséget generál, így természetesen növekszik az olvasási késés?

## <a name="migration-issues"></a>Áttelepítési problémák

Ha az Azure HDInsight-ba végez áttelepítést, győződjön meg arról, hogy az áttelepítést szisztematikusan és pontosan, lehetőleg automatizáláson keresztül végzi el. Kerülje a manuális áttelepítést. Ügyeljen a következőkre:

1. A táblázat attribútumait – például a tömörítést, a Bloom szűrőket stb. – pontosan át kell telepíteni.

1. A Phoenix-táblák esetében a sós beállításokat az új fürt méretének megfelelően kell leképezni. Előfordulhat például, hogy a rendszer a fürtben lévő munkavégző csomópontok számának többszörösét ajánlja a sós gyűjtők számára.  

## <a name="server-side-config-tunings"></a>Kiszolgálóoldali konfiguráció-hangolások

A HDInsight-HBase a HFiles-ket a rendszer a távoli tárolóban tárolja – így ha van gyorsítótár-hiány, a beolvasások díja határozottan magasabb, mint a helyszíni rendszerek, és a hálózati késésnek köszönhetően a helyi HDFS által támogatott adatokat is megtalálhatja. A legtöbb esetben a HBase cache-gyorsítótárak intelligens használata (a gyorsítótár és a gyűjtő gyorsítótárának blokkolása) úgy lett kialakítva, hogy kikerülje ezt a problémát. Azonban továbbra is esetenként előfordulnak olyan esetek, amikor ez problémát jelenthet az ügyfelek számára. A prémium szintű blokk blob-fiók használata némileg segítette ezt. A WASB (Windows Azure Storage illesztőprogram) blob azonban bizonyos tulajdonságokra támaszkodik, például az `fs.azure.read.request.size` értékre, hogy a blokkokban tárolt adatokat az olvasási mód alapján (szekvenciális vagy véletlenszerű) tekintse meg, ezért továbbra is láthatjuk a nagyobb késésű példányokat az olvasásokkal. Olyan empirikus kísérleteken keresztül találtuk, hogy az olvasási kérések blokkjának mérete (`fs.azure.read.request.size`) 512 KB-ra van állítva, és a HBase táblák blokk-méretének megfeleltetése a legjobb eredményt adja a gyakorlatban.

A HDInsight HBase a legtöbb nagy méretű csomópontos fürtök esetében `bucketcache` fájlt biztosít a virtuális géphez csatlakoztatott helyi SSD-hez, amely a `regionservers`-et futtatja. Időnként a kikapcsolt halom-gyorsítótár kikapcsolásával némi javulást biztosíthat. Ezzel a korlátozással csökkenthető a rendelkezésre álló memória használata, és elképzelhető, hogy a fájl-alapú gyorsítótár mérete kisebb, így ez nem mindig a legjobb választás.

Néhány más megadott paraméter, amely úgy tűnik, hogy a következő indoklással több különböző fokkal segített volna:

1. Növelje `memstore` méretet az alapértelmezett 128 MB-ról 256 MB-ra – ez a beállítás általában nagy mennyiségű írási forgatókönyv esetén ajánlott.

1. A tömörítéshez dedikált szálak számának növelése – az alapértelmezett 1 és 4 közötti érték. Ez a beállítás akkor fontos, ha betartjuk a gyakori kisebb tömörítést.

1. Ne blokkolja a `memstore` ürítést a tároló korlátja miatt. a `Hbase.hstore.blockingStoreFiles` érték 100-ra növelhető a puffer megadásához.

1. A kiürítések szabályozásához az alapértelmezett értékek az alábbiak szerint kezelhetők:

    1. a `Hbase.regionserver.maxlogs` a 32-től 140-ig lehet (a upped elkerülése érdekében).

    1. @no__t – 0 = 0,55.

    1. @no__t – 0 = 0,60.

1. Phoenix-specifikus konfigurációk a szál-készlet hangolásához:

    1. a `Phoenix.query.queuesize` a 10000-re növelhető.

    1. a `Phoenix.query.threadpoolsize` a 512-re növelhető.

1. Egyéb Phoenix-specifikus konfigurációk:

    1. a `Phoenix.rpc.index.handler.count` értéke 50, ha nagy vagy sok indexelési keresési lehetőség van.

    1. @no__t – 0 – az alapértelmezett 15 percből 1 órára lehet upped.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` – 30 percen belül 1 órára lehet upped.

    1. `Phoenix.coprocessor.maxmetadatacachesize` – 20 MB-tól 50 MB-ra lehet upped.

1. RPC időtúllépések – a HBase RPC-időtúllépés, a HBase-ügyfél képolvasó-időtúllépése és a Phoenix-lekérdezés időtúllépése 3 percig is növekedhet. Fontos megjegyezni, hogy a `hbase.client.scanner.caching` paraméter olyan értékre van beállítva, amely megegyezik a kiszolgáló végén és az ügyfél végén. Ellenkező esetben ez a beállítás az ügyfél végén található `OutOfOrderScannerException`-hoz kapcsolódó hibákhoz vezet. Ezt a beállítást kis értékre kell állítani a nagyméretű keresések esetében. Ezt az értéket 100-re állítjuk be.

## <a name="other-considerations"></a>Egyéb szempontok

A hangoláshoz szükséges egyéb paraméterek:

1. `Hbase.rs.cacheblocksonwrite` – Ez a beállítás alapértelmezés szerint True (igaz) értékre van állítva a HDI esetében.

1. Beállítások, amelyek lehetővé teszik a kisebb tömörítés késleltetését később.

1. Olyan kísérleti beállítások, mint például az olvasási és írási kérelmek számára fenntartott várólisták százalékos arányának beállítása.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

- Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiók a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

- Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
