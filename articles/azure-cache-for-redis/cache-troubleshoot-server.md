---
title: Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása
description: Ismerje meg, hogyan oldhatja meg az Azure Cache for Redis gyakori kiszolgálóoldali problémáit, például a memórianyomást, a magas PROCESSZOR-, hosszú ideig futó parancsokat vagy a sávszélesség-korlátozásokat.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277933"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása

Ez a szakasz ismerteti a redis-i Azure-gyorsítótár vagy az azt üzemeltető virtuális gép(ek) egyik feltétele miatt felmerülő hibaelhárítási problémákat.

- [A Redis-kiszolgáló memóriaterhelése](#memory-pressure-on-redis-server)
- [Magas processzorhasználat vagy kiszolgálóterhelés](#high-cpu-usage-or-server-load)
- [Hosszú ideig futó parancsok](#long-running-commands)
- [Kiszolgálóoldali sávszélesség-korlátozás](#server-side-bandwidth-limitation)

> [!NOTE]
> Ebben az útmutatóban a hibaelhárítási lépések közé tartozik a Redis-parancsok futtatására és a különböző teljesítménymutatók figyelésére vonatkozó utasítások. További információt és útmutatást a [További információk](#additional-information) című részben található cikkben talál.
>

## <a name="memory-pressure-on-redis-server"></a>A Redis-kiszolgáló memóriaterhelése

A kiszolgálóoldalon a memórianyomás mindenféle teljesítményproblémát okoz, ami késleltetheti a kérelmek feldolgozását. Amikor a memórianyomás eléri, a rendszer adatokat paged lemezre. Ez _a laphiba_ miatt a rendszer jelentősen lelassul. Ennek a memórianyomásnak számos oka lehet:

- A gyorsítótár a maximális kapacitásközelében lévő adatokkal van feltöltve.
- Redis nagy memóriatöredezettséget lát. Ezt a töredezettséget leggyakrabban a nagy objektumok tárolása okozza, mivel a Redis kis objektumokra van optimalizálva.

A Redis két statisztikát tár fel az [INFO](https://redis.io/commands/info) paranccsal, amelyek segíthetnek a probléma azonosításában: "used_memory" és "used_memory_rss". [Ezeket a mutatókat](cache-how-to-monitor.md#view-metrics-with-azure-monitor) a portál on keresztül tekintheti meg.

A memóriahasználat kifogástalan állapotának megőrzése érdekében számos lehetséges módosítást tehet meg:

- [Állítson be egy memóriaházirendet,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) és állítsa be a kulcsok lejárati idejét. Ez a házirend nem feltétlenül elegendő, ha töredezettség.
- [Olyan maxmemória-fenntartott érték konfigurálása,](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) amely elég nagy ahhoz, hogy kompenzálja a memória töredezettségét.
- A nagy gyorsítótárazott objektumokat kisebb kapcsolódó objektumokra bontsa.
- [Hozzon létre riasztásokat](cache-how-to-monitor.md#alerts) a metrikák, például a használt memória értesítést kell kapnia a lehetséges hatásokról.
- [Nagyobb](cache-how-to-scale.md) gyorsítótárméretre méretezhető, nagyobb memóriakapacitással.

## <a name="high-cpu-usage-or-server-load"></a>Magas processzorhasználat vagy kiszolgálóterhelés

A nagy kiszolgálóterhelés vagy a processzorhasználat azt jelenti, hogy a kiszolgáló nem tudja időben feldolgozni a kérelmeket. Előfordulhat, hogy a kiszolgáló lassan válaszol, és nem tud lépést tartani a kérelmek arányával.

[Monitor metrikák,](cache-how-to-monitor.md#view-metrics-with-azure-monitor) például a CPU vagy a kiszolgáló terhelése. Figyelje az időelőnyöknek megfelelő cpu-használat kiugrásait.

A kiszolgáló terhelésének csökkentése érdekében számos módosítást is elérhet:

- Vizsgálja meg, hogy mi okozza a [CPU-csúcsokat,](#long-running-commands) például az alábbiakban vagy laphibákat a magas memórianyomás miatt.
- [Hozzon létre riasztásokat](cache-how-to-monitor.md#alerts) a metrikák, például a CPU vagy a kiszolgáló terhelése, hogy korán értesítést kapjon a lehetséges hatásokról.
- [Nagyobb](cache-how-to-scale.md) gyorsítótárméretre és nagyobb CPU-kapacitásra méretezhető.

## <a name="long-running-commands"></a>Hosszú ideig futó parancsok

Egyes Redis-parancsok végrehajtása drágább, mint mások. A [Redis parancsok dokumentációja](https://redis.io/commands) az egyes parancsok időösszetettségét mutatja. Mivel a Redis parancsfeldolgozás egyszálas, a futtatáshoz szükséges parancs blokkolja az összes többi, hogy jön utána. Tekintse át a Redis-kiszolgálónak kiadott parancsokat, hogy tisztában legyen a teljesítményükkel. Például a [KEYS](https://redis.io/commands/keys) parancsot gyakran használják anélkül, hogy tudnánk, hogy ez egy O(N) művelet. A PROCESSZORcsúcsok csökkentéséhez a [SCAN](https://redis.io/commands/scan) segítségével elkerülheti a KEYS billentyűket.

A [SLOWLOG](https://redis.io/commands/slowlog) paranccsal megmérheti a kiszolgálón végrehajtott költséges parancsokat.

## <a name="server-side-bandwidth-limitation"></a>Kiszolgálóoldali sávszélesség-korlátozás

A különböző gyorsítótárméretek különböző hálózati sávszélesség-kapacitással rendelkeznek. Ha a kiszolgáló túllépi a rendelkezésre álló sávszélességet, akkor az adatok nem lesznek olyan gyorsan elküldve az ügyfélnek. Az ügyfelek kérései időtúlodhatnak, mert a kiszolgáló nem tudja elég gyorsan leadni az adatokat az ügyfélnek.

A "Gyorsítótár olvasása" és a "Gyorsítótár írása" metrikák segítségével megtekintheti, hogy mennyi kiszolgálóoldali sávszélességet használ. Ezeket a mutatókat a portálon [tekintheti](cache-how-to-monitor.md#view-metrics-with-azure-monitor) meg.

Az olyan helyzetek csökkentése érdekében, amikor a hálózati sávszélesség-használat közel van a maximális kapacitáshoz:

- Módosítsa az ügyfélhívás viselkedését a hálózati igények csökkentése érdekében.
- [Hozzon létre riasztásokat](cache-how-to-monitor.md#alerts) a metrikák, például a gyorsítótár olvasása vagy a gyorsítótár írási értesítést kell kapnia a lehetséges hatásokról.
- [Nagyobb](cache-how-to-scale.md) gyorsítótárméretre és nagyobb hálózati sávszélesség-kapacitással.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis ügyféloldali hibáinak elhárítása](cache-troubleshoot-client.md)
- [Milyen Azure-gyorsítótárat kell használni a Redis-ajánlathoz és a mérethez?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Hogyan tesztelhetem és tesztelhetem a gyorsítótár teljesítményét?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Az Azure-gyorsítótár figyelése a Redis számára](cache-how-to-monitor.md)
- [Hogyan futtathatom a Redis parancsokat?](cache-faq.md#how-can-i-run-redis-commands)
