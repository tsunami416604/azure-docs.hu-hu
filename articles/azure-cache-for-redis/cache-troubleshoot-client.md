---
title: Az Azure Cache for Redis ügyféloldali hibáinak elhárítása
description: Ismerje meg, hogyan oldhatja meg az Azure Cache for Redis gyakori ügyféloldali problémáit, például a Redis ügyfélmemória-nyomást, a forgalom robbanását, a nagy processzort, a korlátozott sávszélességet, a nagy kéréseket vagy a nagy válaszméretet.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277946"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Az Azure Cache for Redis ügyféloldali hibáinak elhárítása

Ez a szakasz az alkalmazás által használt Redis-ügyfél egyik feltétele miatt felmerülő hibaelhárítási problémákat ismerteti.

- [Memórianyomás a Redis ügyfélen](#memory-pressure-on-redis-client)
- [Forgalom robbanás](#traffic-burst)
- [Magas ügyfélprocesszor-használat](#high-client-cpu-usage)
- [Ügyféloldali sávszélesség korlátozása](#client-side-bandwidth-limitation)
- [Nagy kérés- vagy válaszméret](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Memórianyomás a Redis ügyfélen

Az ügyfélgépen lévő memórianyomás mindenféle teljesítményproblémákhoz vezet, amelyek késleltethetik a gyorsítótárból érkező válaszok feldolgozását. Amikor a memórianyomás eléri, a rendszer adatokat paged lemezre. Ez _a laphiba_ miatt a rendszer jelentősen lelassul.

Az ügyfélre nehezedő memórianyomás észlelése:

- Figyelje a számítógép memóriahasználatát, és győződjön meg arról, hogy az nem haladja meg a rendelkezésre álló memóriát.
- Az ügyfél teljesítményszámlálójának figyelése. `Page Faults/Sec` Normál működés esetén a legtöbb rendszer oldalhibákkal rendelkezik. A kérelem időtúlterhelésének megfelelő laphibák kiugrásamemória-nyomást jelezhet.

Az ügyfélre nehezedő magas memórianyomás többféleképpen mérsékelhető:

- A memóriahasználati minták basaa, hogy csökkentse az ügyfél memóriafelhasználását.
- Frissítse az ügyfél virtuális gép egy nagyobb méretű, több memóriával.

## <a name="traffic-burst"></a>Forgalom robbanás

A rossz `ThreadPool` beállításokkal kombinált adatlöketek a Redis Server által már elküldött, de az ügyféloldalon még fel nem használott adatok feldolgozásának késését eredményezhetik.

Egy `ThreadPool` [példa `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)segítségével figyelemmel kísérheti, hogyan változnak a statisztikák az idő múlásával. A StackExchange.Redis üzeneteit az alábbihoz hasonlóan további vizsgálatra használhatja: `TimeoutException`

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Az előző kivételben számos érdekes probléma van:

- Figyelje meg, `IOCP` hogy `WORKER` a szakaszban és a szakaszban van egy `Busy` érték, amely nagyobb, mint az `Min` érték. Ez a `ThreadPool` különbség azt jelenti, hogy a beállításokat módosítani kell.
- Azt is `in: 64221`látni . Ez az érték azt jelzi, hogy 64 211 bájt érkezett az ügyfél kernelsocket rétegére, de az alkalmazás nem olvasta be őket. Ez a különbség általában azt jelenti, hogy az alkalmazás (például StackExchange.Redis) nem olvassa az adatokat a hálózatról olyan gyorsan, mint a kiszolgáló küld i.

[Beállíthatja a `ThreadPool` beállításokat,](cache-faq.md#important-details-about-threadpool-growth) hogy a szálkészlet gyorsan felskálázható burst forgatókönyvek alatt.

## <a name="high-client-cpu-usage"></a>Magas ügyfélprocesszor-használat

A magas ügyfélprocesszor-használat azt jelzi, hogy a rendszer nem tud lépést tartani a feladattal, amelyet meg kellett tennie. Annak ellenére, hogy a gyorsítótár gyorsan elküldte a választ, előfordulhat, hogy az ügyfél nem dolgozza fel a választ időben.

Az ügyfél rendszerszintű cpu-használatának figyelése az Azure Portalon vagy a számítógépen elérhető teljesítményszámlálók segítségével. Ügyeljen arra, hogy ne figyelje a *folyamat* PROCESSZORát, mert egyetlen folyamat alacsony CPU-használattal rendelkezhet, de a rendszerszintű CPU magas lehet. Figyelje az időelőnyöknek megfelelő cpu-használat kiugrásait. A magas PROCESSZOR `in: XXX` is `TimeoutException` okozhat magas értékeket a hibaüzenetek, ahogy azt a Forgalom burst szakaszban [leírtak](#traffic-burst) szerint.

> [!NOTE]
> StackExchange.Redis 1.1.603 és `local-cpu` újabb `TimeoutException` tartalmazza a metrika a hibaüzenetekben. Győződjön meg arról, hogy a [StackExchange.Redis NuGet csomag](https://www.nuget.org/packages/StackExchange.Redis/)legújabb verzióját használja. Vannak hibák folyamatosan rögzítik a kódot, hogy ez robusztusabb időtúlállások, így miután a legújabb verzió fontos.
>

Az ügyfél magas processzorhasználatának csökkentése:

- Vizsgálja meg, mi okozza a CPU-csúcsokat.
- Frissítse az ügyfelet nagyobb virtuális gépméretre, nagyobb processzorkapacitással.

## <a name="client-side-bandwidth-limitation"></a>Ügyféloldali sávszélesség korlátozása

Az ügyfélgépek architektúrájától függően előfordulhat, hogy korlátozták a rendelkezésre álló hálózati sávszélességet. Ha az ügyfél túllépi a rendelkezésre álló sávszélességet a hálózati kapacitás túlterhelésével, akkor az ügyféloldalon a rendszer nem dolgozza fel az adatokat olyan gyorsan, mint ahogy azt a kiszolgáló küldi. Ez a helyzet időhosszabbításhoz vezethet.

Egy [példa `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)segítségével figyelemmel kísérheti, hogyan változik a sávszélesség-használat az idő múlásával. Előfordulhat, hogy ez a kód nem fut sikeresen bizonyos korlátozott engedélyekkel rendelkező környezetekben (például az Azure-webhelyeken).

A hálózati sávszélesség-felhasználás csökkentése vagy az ügyfél virtuális gépméretének növelése egy nagyobb hálózati kapacitással.

## <a name="large-request-or-response-size"></a>Nagy kérés vagy válasz mérete

Egy nagy kérés/válasz időkéréseket okozhat. Tegyük fel például, hogy az ügyfélen beállított időtúlhasználati érték 1 másodperc. Az alkalmazás egyszerre két kulcsot kér (például "A" és "B") (ugyanazt a fizikai hálózati kapcsolatot használva). A legtöbb ügyfél támogatja a "csővezeték" kérést, ahol mindkét "A" és "B" kérés egymás után érkezik anélkül, hogy megvárná a válaszaikat. A kiszolgáló ugyanabban a sorrendben küldi vissza a válaszokat. Ha az "A" válasz nagy, a későbbi kérelmek időtúllépések többségének felemészthető.

A következő példában az "A" és a "B" kérés tüstént gyorsan elküldi a kiszolgálónak. A szerver gyorsan megkezdi az "A" és "B" válaszok küldését. Az adatátviteli idők miatt a "B" válasznak az "A" válasz mögött kell várnia, még akkor is, ha a kiszolgáló gyorsan reagált.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Ezt a kérést/választ nehéz mérni. Az ügyfélkódot a nagy kérések és válaszok nyomon követéséhez is bevetheti.

A nagy válaszméretek felbontása változó, de a következőket tartalmazza:

1. Optimalizálja az alkalmazást nagy számú kis értékek, nem pedig néhány nagy értékeket.
    - Az előnyben részesített megoldás az adatok kapcsolódó kisebb értékekre való bontása.
    - Lásd a post [Mi az ideális érték méret tartományban redis? A 100 KB túl nagy?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) a kisebb értékek ajánlottjainak részleteiért.
1. A virtuális gép méretének növelése nagyobb sávszélesség-képességek hez
    - Nagyobb sávszélességet az ügyfél vagy a kiszolgáló virtuális gép csökkentheti az adatátviteli idő a nagyobb válaszok.
    - Hasonlítsa össze a jelenlegi hálózati használat mindkét gépen, hogy a jelenlegi virtuális gép mérete. Lehet, hogy csak a kiszolgálón vagy csak az ügyfélen nem elegendő a nagyobb sávszélesség.
1. Növelje az alkalmazás által használt kapcsolatobjektumok számát.
    - Ciklikus multiplexelési megközelítést használva különböző kapcsolatobjektumokon keresztül imitoléket intézz.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása](cache-troubleshoot-server.md)
- [Hogyan tesztelhetem és tesztelhetem a gyorsítótár teljesítményét?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
