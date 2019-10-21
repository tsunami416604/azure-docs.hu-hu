---
title: Feladatátvétel és javítás – Azure cache a Redis-hez | Microsoft Docs
description: Ismerje meg a feladatátvételt, a javítást, valamint az Azure cache Redis-hez készült frissítési folyamatát.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675901"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Feladatátvétel és javítás a Redis készült Azure cache-hez

A rugalmas és sikeres ügyfélalkalmazások kiépítése szempontjából fontos, hogy megértsük, mi a feladatátvétel a Redis szolgáltatáshoz készült Azure cache-vel összefüggésben. A gyorsítótár-feladatátvétel gyakori oka, hogy a Redis bináris fájljait javító kezelési szolgáltatásból származik. Ez a cikk ismerteti a feladatátvételt, a javítások során felmerülő tudnivalókat és a rugalmas ügyfélalkalmazás felépítését.

## <a name="what-is-a-failover"></a>Mi az a feladatátvétel?

### <a name="a-quick-summary-of-our-architecture"></a>Architektúra gyors összefoglalása

A gyorsítótárak több, külön magánhálózati IP-címmel rendelkező virtuális gépről épülnek fel. Minden virtuális gép, más néven csomópont, egyetlen virtuális IP-címmel csatlakozik egy megosztott terheléselosztóhoz. Mindegyik csomópont futtatja a Redis-kiszolgáló folyamatát, és az állomásnév és a Redis-portok segítségével érhető el. Az egyes csomópontok fő vagy replika csomópontnak tekintendők. Amikor egy ügyfélalkalmazás egy gyorsítótárhoz csatlakozik, a forgalma áthalad ezen a terheléselosztóon, és automatikusan átirányítja a fő csomóponthoz.

Alapszintű gyorsítótárban az egyetlen csomópont mindig a főkiszolgáló. Standard vagy prémium szintű gyorsítótárban két csomópont van, ahol az egyik a főkiszolgáló, a másik pedig a replika. Mivel a standard és a prémium gyorsítótárak több csomóponttal rendelkeznek, előfordulhat, hogy egy csomópont nem érhető el, míg a másik folytatja a kérelmek feldolgozását. A fürtözött gyorsítótárak számos szegmensből állnak, amelyek mindegyike különböző fő-és replika-csomópontokkal rendelkezik. Előfordulhat, hogy az egyik szegmens leáll, miközben a többi elérhető marad.

> [!NOTE]
> Az alapszintű gyorsítótár nem rendelkezik több csomóponttal, és nem biztosít SLA-t a rendelkezésre álláshoz. Az alapszintű gyorsítótárak csak fejlesztési és tesztelési célokra ajánlottak. A rendelkezésre állás növeléséhez használjon standard vagy prémium szintű gyorsítótárat a több csomópontos telepítéshez.

### <a name="a-failover-explained"></a>A feladatátvétel magyarázata

Feladatátvétel akkor történik, amikor egy replika csomópontja maga is kiszolgálja a fő csomópontot, a régi főcsomópont pedig lezárja a meglévő kapcsolatokat. A főcsomópont biztonsági mentésének elvégzése után a rendszer észreveszi a szerepkörök változását, és lefokozza magát replikaként. Ezután csatlakozik az új főkiszolgálóhoz, és szinkronizálja az adatokat. A feladatátvétel lehet tervezett vagy nem tervezett.

A tervezett feladatátvétel olyan rendszerfrissítések során zajlik, mint a Redis javítása vagy az operációs rendszer frissítése és a felügyeleti műveletek, például a skálázás és az újraindítás. Mivel a csomópontok speciális értesítést kapnak a frissítésről, a szerepkörök együttműködési felcserélése és a változás terheléselosztó gyors frissítése is megtörténik. Egy tervezett feladatátvételnek 1 másodpercnél rövidebbnek kell lennie.

A nem tervezett feladatátvétel hardverhiba, hálózati hiba vagy más váratlan kimaradás miatt fordulhat elő a főcsomópontban. A replika csomópont előlépteti magát a főkiszolgálóval, de a folyamat tovább tart. A replika-csomópontnak először meg kell állapítania, hogy a fő csomópontja nem érhető el, mielőtt kezdeményezni tudja a feladatátvételi folyamatot. A replika csomópontnak azt is ellenőriznie kell, hogy ez a nem tervezett hiba nem átmeneti vagy helyi a túlbuzgó feladatátvétel elkerüléséhez. Ez az észlelési késleltetés azt jelenti, hogy a nem tervezett feladatátvétel általában 10 – 15 másodpercen belül befejeződik.

## <a name="how-does-patching-occur"></a>Hogyan történik a javítás?

A Redis szolgáltatás Azure cache szolgáltatása rendszeresen frissíti a gyorsítótárat a legújabb platform-funkciókkal és javításokkal. A gyorsítótár javításához a szolgáltatás a következő lépéseket követi:

1. A kezelési szolgáltatás kiválasztja az egyik csomópontot a javításhoz.
1. Ha a kijelölt csomópont egy fő csomópont, akkor a replika csomópontja maga is támogatja magát. Ez az előléptetés tervezett feladatátvételnek minősül.
1. A kiválasztott csomópont újraindul az új módosítások elvégzéséhez, és biztonsági másolatot készít a replika csomópontként. A replika-csomópontok csatlakoznak a fő csomóponthoz, és szinkronizálják az adatokat.
1. Amikor az adatszinkronizálás befejeződik, a javítási folyamat megismétli a többi csomópontot.

Mivel a javítás egy tervezett feladatátvétel, a replika csomópont gyorsan megkezdi magát a főkiszolgálónak, és megkezdi a karbantartási kérelmek és az új kapcsolatok megkezdését. Az alapszintű gyorsítótárak nem rendelkeznek replika-csomóponttal, és nem érhetők el, amíg a frissítés be nem fejeződik. A fürtözött gyorsítótár minden szegmense külön van kijavítottan, és nem zárul a kapcsolat egy másik szegmenshez.

> [!IMPORTANT]
> Az adatvesztés elkerülése érdekében a csomópontok egyenként is kijavítottak. Az alapszintű gyorsítótárak adatvesztéssel fognak rendelkezni. A fürtözött gyorsítótárak egyszerre csak egy szegmenst javítottak.

Egyszerre több gyorsítótár is található egy adott erőforráscsoport és régió esetében.  A különböző erőforráscsoportok vagy különböző régiókba tartozó gyorsítótárak egyidejűleg is kijavíthatók.

Mivel a folyamat megismétlése előtt a teljes adatszinkronizálás történik, az adatvesztés nem valószínű, ha standard vagy prémium szintű gyorsítótárat használ. További védelmet biztosíthat az adatvesztés ellen az [adatexportálás](cache-how-to-import-export-data.md#export) és az [adatmegőrzés](cache-how-to-premium-persistence.md)engedélyezése mellett.

### <a name="additional-cache-load"></a>További gyorsítótár-betöltés

Ha feladatátvétel történik, a standard és a prémium gyorsítótárnak az egyik csomópontról a másikra kell replikálnia az adatait. Ez a replikálás némi terhelést okoz a kiszolgáló memóriájában és a CPU-ban is. Ha a gyorsítótár-példány már be van töltve, az ügyfélalkalmazások nagyobb késést tapasztalhatnak. Szélsőséges esetekben előfordulhat, hogy az ügyfélalkalmazások időtúllépési kivételeket kapnak. [Konfigurálja](cache-configure.md#memory-policies) a gyorsítótár `maxmemory-reserved` beállítást a további terhelés hatásának enyhítése érdekében.

## <a name="how-does-a-failover-impact-my-client-application"></a>Hogyan befolyásolja a feladatátvétel az ügyfélalkalmazás alkalmazását?

Az ügyfélalkalmazás által látott hibák száma attól függ, hogy a feladatátvétel során hány művelet van függőben a kapcsolatban. A lezárt kapcsolatok csomóponton keresztül átirányított kapcsolatok hibaüzeneteket fognak látni. Számos ügyfél-függvénytár különböző típusú hibákat okozhat, például időtúllépési kivételeket, csatlakozási kivételeket vagy szoftvercsatorna-kivételeket a kapcsolatok megszakításakor. A kivételek száma és típusa attól függ, hogy hol található a kód elérési útja, amikor a gyorsítótár lezárja a kapcsolatait. Például egy olyan művelet, amely egy kérést küld, de nem kapott választ a feladatátvétel bekövetkeztekor, időtúllépési kivételt kaphat. A lezárt kapcsolatok objektumon lévő új kérések a kapcsolatok kivételeit kapják, amíg az újracsatlakoztatás sikeresen megtörténik.

A legtöbb ügyfél-függvénytár megpróbál újracsatlakozni a gyorsítótárhoz, ha erre van konfigurálva, de az előre nem látható hibák időnként nem állíthatók helyre a függvénytár-objektumok. Ha a hibák hosszabbak maradnak, mint az előre konfigurált idő, a rendszer újból létrehozza a kapcsolatok objektumát. A .NET-ben és más objektum-orientált nyelveken a kapcsolatok újraindítása nélkül újra létre lehet tenni a kapcsolatokat [egy lusta \<T \> mintával](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>Mi a teendő az alkalmazásban?

Mivel a feladatátvételt nem lehet teljesen elkerülni, az ügyfélalkalmazások a kapcsolódási szünetek és a sikertelen kérések rugalmassága érdekében megírhatók. Annak ellenére, hogy a legtöbb ügyfél-függvénytár automatikusan újracsatlakozik a gyorsítótár-végponthoz, néhány ügyfél-függvénytár megkísérli a sikertelen kérelmek újrapróbálkozását. Az alkalmazási forgatókönyvtől függően érdemes lehet újrapróbálkozni a logikával.

Az ügyfélalkalmazás rugalmasságának teszteléséhez használjon [újraindítást](cache-administration.md#reboot) manuális triggerként a kapcsolódási szünetekhez. Emellett javasoljuk, hogy a [frissítések](cache-administration.md#schedule-updates) egy gyorsítótárban való beírásával tájékoztassa a felügyeleti szolgáltatást, hogy a Redis Runtime-javítások érvényesek legyenek a megadott heti Windows rendszerben. Ezek a Windows jellemzően olyan időszakokra vannak kiválasztva, amikor az ügyfélalkalmazások forgalma alacsonyabb a lehetséges incidensek elkerülése érdekében.

### <a name="client-network-configuration-changes"></a>Az ügyfél hálózati konfigurációjának módosításai

Bizonyos ügyféloldali hálózati konfiguráció módosításai a "nincs elérhető kapcsolatok" hibaüzenetet válthatják ki.  Az ügyfélalkalmazások virtuális IP-címének átmeneti és üzemi tárolóhelyek közötti, illetve az alkalmazás példányai méretének vagy méretezésének skálázása egy percnél kevesebb kapcsolódási problémát okozhat. Az ügyfélalkalmazás valószínűleg elveszti a kapcsolódást más külső hálózati erőforrásokhoz is, a Redis mellett.

## <a name="next-steps"></a>Következő lépések

- A gyorsítótár [frissítéseinek ütemezett időpontja](cache-administration.md#schedule-updates) .
- Az alkalmazások rugalmasságának tesztelése [újraindítással](cache-administration.md#reboot).
- Memória-foglalások és-házirendek [konfigurálása](cache-configure.md#memory-policies) .
