---
title: Az Azure cache hibaelhárítása Redis-kiszolgálókkal kapcsolatos problémák esetén | Microsoft Docs
description: Ismerje meg, Hogyan oldhatók fel a Redis Azure cache szolgáltatással kapcsolatos gyakori kiszolgálóoldali problémák
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: 4310c31f2a59ae30b7317d043dc6d92b93fee050
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819615"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Az Azure cache hibaelhárítása a Redis-kiszolgálókkal kapcsolatos problémák esetén

Ez a szakasz azokat a hibaelhárítási problémákat ismerteti, amelyek az Azure cache Redis vagy az azt üzemeltető virtuális gép (ek) feltétele miatt fordulnak elő.

- [Memória-nyomás a Redis-kiszolgálón](#memory-pressure-on-redis-server)
- [Magas CPU-használat vagy-kiszolgáló terhelése](#high-cpu-usage-or-server-load)
- [Hosszan futó parancsok](#long-running-commands)
- [Kiszolgálóoldali sávszélesség-korlátozás](#server-side-bandwidth-limitation)

> [!NOTE]
> Az útmutatóban ismertetett hibaelhárítási lépések többek között a Redis-parancsok futtatására és a különböző teljesítmény-mérőszámok figyelésére vonatkozó utasításokat tartalmaznak. További információkért és útmutatásért tekintse meg a [További információ](#additional-information) szakasz cikkeit.
>

## <a name="memory-pressure-on-redis-server"></a>Memória-nyomás a Redis-kiszolgálón

A kiszolgálóoldali memória-terhelés a kérelmek feldolgozását késleltető teljesítményproblémák összes típusához vezet. Ha a memória terhelése eléri a memóriát, előfordulhat, hogy a rendszer lemezre küldi az adatlapot. Az _oldal meghibásodása_ miatt a rendszerleállás jelentősen lelassul. Ennek a memória-nyomásnak több lehetséges oka van:

- A gyorsítótár megtelik a maximális kapacitás közelében lévő adattal.
- A Redis nagy memória töredezettségét látja. Ezt a töredezettséget leggyakrabban a nagyméretű objektumok tárolása okozza, mivel a Redis kis objektumokra van optimalizálva.

A Redis a "used_memory" és a "used_memory_rss" nevű [info](https://redis.io/commands/info) paranccsal tesz elérhetővé két statisztikát. Ezek a [metrikák](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portál használatával tekinthetők meg.

Több lehetséges módosítást is végezhet a memóriahasználat kifogástalan megőrzése érdekében:

- [Állítsa be a memória-házirendet](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , és állítsa be a lejárati időt a kulcsokra. Előfordulhat, hogy ez a szabályzat nem elegendő, ha töredezettsége van.
- [Állítson be egy olyan maxmemory foglalt értéket](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , amely elég nagy a memória töredezettségének kompenzálására.
- Bontsa ki a nagy gyorsítótárban lévő objektumokat kisebb kapcsolódó objektumokra.
- [Riasztásokat hozhat létre](cache-how-to-monitor.md#alerts) a metrikák, például a használt memória számára, hogy korán értesüljön a lehetséges hatásokról.
- Nagyobb méretű gyorsítótár-méretre [méretezheti](cache-how-to-scale.md) a memóriát.

## <a name="high-cpu-usage-or-server-load"></a>Magas CPU-használat vagy-kiszolgáló terhelése

A kiszolgálók nagy terhelése vagy CPU-kihasználtsága azt jelenti, hogy a kiszolgáló nem tudja időben feldolgozni a kérelmeket. Előfordulhat, hogy a kiszolgáló nem válaszol, és nem tud lépést tartani a kérelmek díjszabásával.

A metrikák, például a CPU vagy a kiszolgáló terhelésének [figyelése](cache-how-to-monitor.md#view-metrics-with-azure-monitor) . Figyelje meg a CPU-használathoz tartozó, időtúllépésekkel egyező tüskéket.

Több módosítást is végezhet a kiszolgálók nagy terhelésének csökkentése érdekében:

- Vizsgálja meg, hogy mi okozza a CPU-tüskéket, például a [hosszú ideig futó parancsokat](#long-running-commands) , amelyek a nagy memória-nyomás miatt az alább vagy az oldalon hibásak.
- [Riasztásokat hozhat létre](cache-how-to-monitor.md#alerts) olyan mérőszámokon, mint a CPU vagy a kiszolgáló terhelése, hogy korán értesüljön a lehetséges hatásokról.
- Nagyobb méretű gyorsítótár-méretre [Méretezés](cache-how-to-scale.md) nagyobb CPU-kapacitással.

## <a name="long-running-commands"></a>Hosszan futó parancsok

Egyes Redis-parancsok drágábbak, mint mások. A [Redis-parancsok dokumentációja](https://redis.io/commands) az egyes parancsok időbeli összetettségét mutatja. Mivel a Redis-parancsok feldolgozása egyszálas, a Futtatás ideje alatt álló parancs letiltja az összes többiet, amely azután következik be. Tekintse át a Redis-kiszolgálónak kiadott parancsokat, hogy megértsék a teljesítményre gyakorolt hatásukat. A [kulcsok](https://redis.io/commands/keys) parancs például gyakran használatos az O (N) művelet ismerete nélkül. A kulcsok a [vizsgálat](https://redis.io/commands/scan) használatával elkerülhetők a CPU-tüskék csökkentése érdekében.

A [slowlog parancs kimenetét](https://redis.io/commands/slowlog) parancs használatával a kiszolgálón végrehajtott költséges parancsok mérhetők.

## <a name="server-side-bandwidth-limitation"></a>Kiszolgálóoldali sávszélesség-korlátozás

A különböző gyorsítótár-méretek eltérő hálózati sávszélességgel rendelkeznek. Ha a kiszolgáló meghaladja a rendelkezésre álló sávszélességet, akkor a rendszer nem küldi el az adatforgalmat az ügyfélnek gyorsan. Az ügyfelek kérései időtúllépést okoznak, mert a kiszolgáló nem tud elég gyorsan leküldeni az adott ügyfélnek az adatmennyiséget.

A "cache Read" és a "cache Write" metrikák használatával megtekintheti, hogy mennyi kiszolgálóoldali sávszélességet használ a rendszer. Ezek a [metrikák](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portálon tekinthetők meg.

Az olyan helyzetek enyhítése, amikor a hálózati sávszélesség-használat a maximális kapacitáshoz közeledik:

- Az ügyfél hívási viselkedésének módosítása a hálózati igény csökkentése érdekében.
- [Riasztásokat hozhat létre](cache-how-to-monitor.md#alerts) olyan mérőszámokon, mint például a gyorsítótár olvasási vagy gyorsítótár-írási lehetősége, hogy korán értesüljön a lehetséges hatásokról.
- Nagyobb méretű gyorsítótár-méretre [Méretezés](cache-how-to-scale.md) nagyobb hálózati sávszélesség-kapacitással.

## <a name="additional-information"></a>További információk

- [Az Azure cache hibaelhárítása Redis ügyféloldali problémák esetén](cache-troubleshoot-client.md)
- [Milyen Azure cache-t használ a Redis-ajánlat és-méret használatához?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Az Azure cache figyelése a Redis](cache-how-to-monitor.md)
- [Hogyan Futtathatok Redis-parancsokat?](cache-faq.md#how-can-i-run-redis-commands)
