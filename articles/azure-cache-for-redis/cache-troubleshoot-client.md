---
title: Az Azure cache hibaelhárítása Redis ügyféloldali problémák esetén
description: Ismerje meg, hogy miként oldhatók meg az Azure cache szolgáltatással kapcsolatos gyakori ügyféloldali problémák, például a Redis-ügyfelek Redis, a forgalom Bursa, a nagy CPU, a korlátozott sávszélesség, a nagyméretű kérelmek vagy a nagy válaszok mérete.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523339"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Az Azure cache hibaelhárítása Redis ügyféloldali problémák esetén

Ez a szakasz azokat a hibaelhárítási problémákat ismerteti, amelyek az alkalmazás által használt Redis-ügyfél feltétele miatt következnek be.

- [Memória-nyomás a Redis-ügyfélen](#memory-pressure-on-redis-client)
- [Forgalom burst](#traffic-burst)
- [Magas ügyféloldali CPU-használat](#high-client-cpu-usage)
- [Ügyféloldali sávszélesség korlátozása](#client-side-bandwidth-limitation)
- [Nagyméretű kérelem vagy válasz mérete](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Memória-nyomás a Redis-ügyfélen

Az ügyfélgépen a memória terhelése minden olyan teljesítménnyel kapcsolatos problémát eredményez, amely késleltetheti a válaszok feldolgozását a gyorsítótárból. Ha a memória terhelése eléri a memóriát, előfordulhat, hogy a rendszer lemezre küldi az adatlapot. Az _oldal meghibásodása_ miatt a rendszerleállás jelentősen lelassul.

A memória terhelésének észlelése az ügyfélen:

- Figyelje meg a memória használatát a gépen, és győződjön meg róla, hogy nem lépi túl a rendelkezésre álló memóriát.
- Az ügyfél `Page Faults/Sec` teljesítményszámláló figyelése. Normál működés esetén a legtöbb rendszernek van néhány lapja. A kérelmek időtúllépésével megegyező lapokon található tüskék a memória nyomását jelezhetik.

A nagy mennyiségű memória terhelése az ügyfélen több módon is enyhíthető:

- Az ügyfélen a memóriahasználat csökkentése érdekében vegye figyelembe a memória-használati mintákat.
- Frissítse az ügyfél virtuális gépét nagyobb méretűre, több memóriával.

## <a name="traffic-burst"></a>Forgalom burst

A gyenge `ThreadPool`i beállításokkal kombinált adatforgalom miatt a Redis-kiszolgáló által már elküldhető adatok feldolgozása késéseket eredményezhet, de az ügyfél oldalán még nem használták.

Figyelje meg, hogy a `ThreadPool` statisztikája hogyan változik az idő múlásával [egy példa `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)használatával. `TimeoutException` üzeneteket a StackExchange. Redis-ből is használhatja, így tovább vizsgálhatja a következőket:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Az előző kivételben számos érdekes probléma van:

- Figyelje meg, hogy a `IOCP` szakaszban és a `WORKER` szakaszban `Busy` értéke nagyobb, mint a `Min` érték. Ez a különbség azt jelenti, hogy a `ThreadPool` beállításait módosítani kell.
- `in: 64221`is megtekintheti. Ez az érték azt jelzi, hogy az ügyfél kernel szoftvercsatorna rétegében 64 211 bájt érkezett, de az alkalmazás nem olvasta el. Ez a különbség általában azt jelenti, hogy az alkalmazás (például a StackExchange. Redis) nem olvas be adatokat a hálózatról olyan gyorsan, ahogy a kiszolgáló elküldi Önnek.

[Konfigurálhatja a `ThreadPool` beállításait](cache-faq.md#important-details-about-threadpool-growth) , hogy ellenőrizze, hogy a szál készlete gyorsan méretezhető-e a burst forgatókönyvek alatt.

## <a name="high-client-cpu-usage"></a>Magas ügyféloldali CPU-használat

A magas ügyféloldali CPU-használat azt jelzi, hogy a rendszer nem tud lépést tartani a kért munkával. Bár a gyorsítótár gyorsan elküldötte a választ, előfordulhat, hogy az ügyfél időben nem tud feldolgozni a választ.

Az ügyfél rendszerszintű CPU-használatának figyelése a Azure Portal vagy a számítógépen található teljesítményszámlálók használatával. Ügyeljen arra, hogy ne figyelje a *folyamat* processzorát, mert egy folyamat alacsony CPU-használattal rendelkezhet, de a rendszerszintű CPU magas lehet. Figyelje meg a CPU-használathoz tartozó, időtúllépésekkel egyező tüskéket. A magas CPU a [forgalmi burst](#traffic-burst) című szakaszban leírtak szerint a `TimeoutException` hibaüzenetekben is magas `in: XXX` értékeket eredményezhet.

> [!NOTE]
> A StackExchange. Redis 1.1.603 és újabb verziója `TimeoutException` hibaüzenetek `local-cpu` metrikáját tartalmazza. Győződjön meg arról, hogy a [StackExchange. Redis NuGet-csomag](https://www.nuget.org/packages/StackExchange.Redis/)legújabb verzióját használja. A kódban folyamatosan rögzített hibák teszik hatékonyabbá az időtúllépéseket, így a legújabb verzió fontos.
>

Az ügyfél magas CPU-használatának csökkentése:

- Vizsgálja meg, mi okozza a CPU-tüskéket.
- Frissítse az ügyfelet egy nagyobb méretű virtuálisgép-méretre több CPU-kapacitással.

## <a name="client-side-bandwidth-limitation"></a>Ügyféloldali sávszélesség korlátozása

Az ügyfélgépek architektúrája alapján előfordulhat, hogy korlátozásokkal rendelkeznek a rendelkezésre álló hálózati sávszélesség mennyiségétől függően. Ha az ügyfél túllépi a rendelkezésre álló sávszélességet a hálózati kapacitás túlterhelésével, akkor a rendszer nem dolgozza fel az adatokat az ügyfél oldalán, ha a kiszolgáló elküldi azt. Ez a helyzet időtúllépéseket eredményezhet.

Figyelje meg, hogy a sávszélesség-használat hogyan módosul az idő múlásával [egy példa `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)használatával. Előfordulhat, hogy ez a kód nem fut sikeresen bizonyos korlátozott engedélyekkel rendelkező környezetekben (például az Azure webhelyek szolgáltatásban).

A szolgáltatás mérséklése érdekében csökkentse a hálózati sávszélesség-használatot, vagy növelje az ügyfél virtuális gép méretét egy nagyobb hálózati kapacitással.

## <a name="large-request-or-response-size"></a>Nagyméretű kérelem vagy válasz mérete

A nagyméretű kérések/válaszok időtúllépést okozhatnak. Tegyük fel például, hogy az ügyfélen konfigurált időtúllépési érték 1 másodperc. Az alkalmazás két kulcsot kér (például "A" és "B") egy időben (ugyanazokkal a fizikai hálózati kapcsolatok használatával). A legtöbb ügyfél támogatja a "pipeline" kérést, ahol az "A" és a "B" kérelmeket egymás után küldi el a rendszer, anélkül, hogy megvárná a válaszokat. A kiszolgáló ugyanezen sorrendben küldi vissza a válaszokat. Ha az "A" válasz nagy, akkor a későbbi kérelmek esetében az időtúllépés nagy részét is megeszik.

A következő példában az "A" és a "B" kérést a rendszer gyorsan elküldi a kiszolgálónak. A kiszolgáló gyorsan megkezdi az "A" és A "B" válaszok küldését. Az adatátviteli időpontok miatt a "B" válasznak az "A" válasz mögött kell várnia, noha a kiszolgáló gyorsan reagál.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Ezt a kérést/választ nehéz mérni. A nagyméretű kérelmek és válaszok nyomon követéséhez az ügyfél kódját is felhasználhatja.

A nagyméretű válaszok méretének felbontása változó, azonban a következők:

1. Néhány nagy érték helyett nagy mennyiségű kis értékre optimalizálhatja alkalmazását.
    - Az előnyben részesített megoldás az adatoknak a kapcsolódó kisebb értékekre való bontása.
    - Tekintse meg a post [Mi az ideális érték a Redis számára? Túl nagy a 100 KB?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) További információ a kisebb értékek ajánlott megoldásáról.
1. Növelje a virtuális gép méretét, hogy nagyobb sávszélesség-képességeket kapjon
    - Az ügyfél vagy a kiszolgáló virtuális gépe további sávszélessége csökkentheti az adatátviteli időpontokat a nagyobb válaszok érdekében.
    - Hasonlítsa össze az aktuális hálózati használatot mindkét gépen a virtuális gép jelenlegi méretének korlátaival. Több sávszélesség csak a kiszolgálón, vagy csak az ügyfélen lehet elég.
1. Növelje az alkalmazás által használt kapcsolatok objektumainak számát.
    - A különböző kapcsolati objektumokra irányuló kérelmeket ciklikus multiplexelés használatával hajthatja végre.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása](cache-troubleshoot-server.md)
- [Hogyan lehet teljesítménytesztet és tesztelni a gyorsítótár teljesítményét?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
