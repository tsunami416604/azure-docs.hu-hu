---
title: Feladatátvétel és javítás – Azure Cache for Redis
description: Ismerje meg a feladatátvételt, a javítást és a Redis-hez való Azure Cache frissítési folyamatát.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122191"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Feladatátvétel és javítás a Redis Azure-gyorsítótárához

Rugalmas és sikeres ügyfélalkalmazások létrehozásához fontos, hogy a Redis-gyorsítótár használatával tisztában ismerje meg a feladatátvételt. A feladatátvétel a tervezett felügyeleti műveletek része lehet, vagy nem tervezett hardver- vagy hálózati hibák okozhatják. A gyorsítótár feladatátvétel gyakori használata akkor történik, amikor a felügyeleti szolgáltatás az Azure-gyorsítótárat redis bináris fájlokhoz javítja. Ez a cikk ismerteti, mi a feladatátvétel, hogyan történik a javítás során, és hogyan hozhat létre egy rugalmas ügyfélalkalmazás.

## <a name="what-is-a-failover"></a>Mi az a feladatátvétel?

Kezdjük az Azure Cache for Redis feladatátvétel áttekintésével.

### <a name="a-quick-summary-of-cache-architecture"></a>A gyorsítótár-architektúra gyors összefoglalása

A gyorsítótár több virtuális gépből áll, különálló, privát IP-címekkel. Minden virtuális gép, más néven csomópont, egyetlen virtuális IP-címmel rendelkező megosztott terheléselosztóhoz csatlakozik. Minden csomópont futtatja a Redis-kiszolgálófolyamatot, és az állomásnév és a Redis portok segítségével érhető el. Minden csomópont mesternek vagy replikacsomópontnak minősül. Amikor egy ügyfélalkalmazás gyorsítótárhoz csatlakozik, a forgalma ezen a terheléselosztón megy keresztül, és automatikusan a fő csomóponthoz kerül.

Az alapszintű gyorsítótárban az egyetlen csomópont mindig egy főkiszolgáló. A standard vagy prémium szintű gyorsítótárban két csomópont van: az egyik a fő, a másik pedig a replika. Mivel a Standard és a Prémium szintű gyorsítótárak több csomóponton, előfordulhat, hogy egy csomópont nem érhető el, míg a másik továbbra is a kérelmek feldolgozása. Fürtözött gyorsítótárak készülnek sok szilánkok, mindegyik különböző fő- és replika-csomópontokkal. Lehet, hogy az egyik szilánk nem, míg a többi elérhető marad.

> [!NOTE]
> Az alapszintű gyorsítótár nem rendelkezik több csomóponttal, és nem kínál szolgáltatásiszint-szerződést (SLA) a rendelkezésre álláshoz. Az alapszintű gyorsítótárak csak fejlesztési és tesztelési célokra ajánlottak. A rendelkezésre állás növelése standard vagy prémium szintű gyorsítótár használata többcsomópontos központi telepítéshez.

### <a name="explanation-of-a-failover"></a>Feladatátvétel magyarázata

Feladatátvétel akkor történik, amikor egy replikacsomópont főcsomóponttá lépteti magát, és a régi főcsomópont bezárja a meglévő kapcsolatokat. Miután a fő csomópont jön vissza, észreveszi a szerepkörök változását, és lefekálja magát, hogy legyen egy replika. Ezután csatlakozik az új főkiszolgálóhoz, és szinkronizálja az adatokat. A feladatátvétel lehet tervezett vagy nem tervezett.

A *tervezett feladatátvétel* a rendszerfrissítések során történik, például a Redis-javítás vagy az operációs rendszer frissítése, valamint a felügyeleti műveletek, például a méretezés és az újraindítás során. Mivel a csomópontok előzetes értesítést kapnak a frissítésről, együttműködhetnek a szerepkörök cseréjével, és gyorsan frissíthetik a változás terheléselosztóját. A tervezett feladatátvétel általában kevesebb, mint 1 másodperc alatt befejeződik.

Nem *tervezett feladatátvétel* történhet hardverhiba, hálózati hiba vagy a fő csomópont egyéb váratlan leállása miatt. A replikacsomópont előlépteti magát mesterré, de a folyamat hosszabb időt vesz igénybe. A replikacsomópontnak először észlelnie kell, hogy a fő csomópontja nem érhető el a feladatátvételi folyamat kezdeményezése előtt. A replikacsomópontnak azt is ellenőriznie kell, hogy ez a nem tervezett hiba nem átmeneti vagy helyi, a szükségtelen feladatátvétel elkerülése érdekében. Az észlelés késleltetése azt jelenti, hogy a nem tervezett feladatátvétel általában 10–15 másodpercen belül befejeződik.

## <a name="how-does-patching-occur"></a>Hogyan történik a javítás?

Az Azure Cache for Redis szolgáltatás rendszeresen frissíti a gyorsítótárat a legújabb platform funkciókkal és javításokkal. Gyorsítótár javításához a szolgáltatás az alábbi lépéseket követi:

1. A felügyeleti szolgáltatás kiválaszt egy csomópontot a javításhoz.
1. Ha a kijelölt csomópont fő csomópont, a megfelelő replikacsomópont együttműködően népszerűsíti magát. Ez a promóció tervezett feladatátvételnek minősül.
1. A kijelölt csomópont újraindul az új módosítások, és jön vissza a replika csomópont.
1. A replikacsomópont csatlakozik a főcsomóponthoz, és szinkronizálja az adatokat.
1. Amikor az adatszinkronizálás befejeződött, a javítási folyamat megismétlődik a többi csomópont esetében.

Mivel a javítás tervezett feladatátvétel, a replikacsomópont gyorsan előlépteti magát főkiszolgálóvá, és megkezdi a kérelmek és az új kapcsolatok kiszolgálását. Az alapszintű gyorsítótárak nem rendelkeznek replikacsomól, és a frissítés befejezéséig nem érhetők el. A fürtözött gyorsítótár minden egyes szegmense külön-külön van javítva, és nem zárja be a kapcsolatot egy másik szegmenshez.

> [!IMPORTANT]
> A csomópontok at az adatvesztés megelőzése érdekében egyenként javítják. Az alapvető gyorsítótárak adatvesztést okoznak. Fürtözött gyorsítótárak javítása egy shard egy időben.

Ugyanabban az erőforráscsoportban és régióban több gyorsítótár is egyesével van javítva.  A különböző erőforráscsoportokban vagy különböző régiókban lévő gyorsítótárak egyszerre javíthatók.

Mivel a teljes adatszinkronizálás a folyamat megismétlése előtt történik, nem valószínű, hogy adatvesztés következne be standard vagy prémium szintű gyorsítótár használata esetén. Az adatvesztés elleni védelem az adatok [exportálásával](cache-how-to-import-export-data.md#export) és az adatmegőrzés engedélyezésével tovább védheti az [adatvesztést.](cache-how-to-premium-persistence.md)

## <a name="additional-cache-load"></a>További gyorsítótár-terhelés

Feladatátvétel esetén a standard és prémium szintű gyorsítótáraknak replikálniuk kell az adatokat az egyik csomópontról a másikra. Ez a replikáció némi terhelésnövekedést okoz mind a kiszolgálómemóriában, mind a processzorban. Ha a gyorsítótár-példány már erősen be van töltve, az ügyfélalkalmazások megnövekedett késést tapasztalhatnak. Szélsőséges esetekben az ügyfélalkalmazások időbeli elállási kivételeket kaphatnak. A további terhelés hatásának csökkentése érdekében [konfigurálja](cache-configure.md#memory-policies) a gyorsítótár beállítását. `maxmemory-reserved`

## <a name="how-does-a-failover-affect-my-client-application"></a>Hogyan befolyásolja a feladatátvétel az ügyfélalkalmazást?

Az ügyfélalkalmazás által látott hibák száma attól függ, hogy hány művelet volt függőben a kapcsolaton a feladatátvétel időpontjában. Minden kapcsolat, amely a kapcsolatokat bezáró csomóponton keresztül továbbítja a kapcsolatokat, hibákat fog látni. Számos ügyfélkódtárak különböző típusú hibákat okozhatnak a kapcsolatok megszakításakor, beleértve az időtúllépési kivételeket, a kapcsolatkivételeket vagy a szoftvercsatorna-kivételeket. A kivételek száma és típusa attól függ, hogy a kódelérési úton hol van a kérelem, amikor a gyorsítótár bezárja kapcsolatait. Például egy művelet, amely küld egy kérelmet, de nem kapott választ, ha a feladatátvétel esetén előfordulhat, hogy időtúllépési kivételt kaphat. A lezárt kapcsolatobjektumúj kérései csatlakozási kivételeket kapnak, amíg az újracsatlakozás sikeresen meg nem történik.

A legtöbb ügyfélkódtárak megpróbálnak újra csatlakozni a gyorsítótárhoz, ha erre vannak konfigurálva. Az előre nem látható hibák azonban időnként helyreállíthatatlan állapotba helyezhetik a könyvtár objektumait. Ha a hibák egy előre beállított nál hosszabb ideig maradnak meg, a kapcsolatobjektumot újra létre kell hozni. A Microsoft.NET és más objektumorientált nyelveken a kapcsolat újrateremtése az alkalmazás újraindítása nélkül [a Lazy\<\> T minta](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern)használatával valósítható meg.

### <a name="how-do-i-make-my-application-resilient"></a>Hogyan tehetem rugalmassá az alkalmazásomat?

Mivel nem tudja teljesen elkerülni a feladatátvételt, írja meg az ügyfélalkalmazásokat a kapcsolattörések és a sikertelen kérelmek rugalmassága érdekében. Bár a legtöbb ügyfélkódtár automatikusan újracsatlakozik a gyorsítótár végpontjára, néhány közülük megpróbálja újramegpróbálni a sikertelen kérelmeket. Az alkalmazásforgatókönyvtől függően érdemes lehet újrapróbálkozási logikát használni a visszalépéssel.

Az ügyfélalkalmazások rugalmasságának teszteléséhez használja az [újraindítást](cache-administration.md#reboot) manuális eseményindítóként a kapcsolatmegszakítások hoz. Azt is javasoljuk, hogy a frissítéseket a gyorsítótárba [ütemezze.](cache-administration.md#schedule-updates) Mondja meg a felügyeleti szolgáltatásnak, hogy alkalmazza a Redis futásidejű javításokat a megadott heti ablakokban. Ezek az ablakok általában olyan időszakok, amikor az ügyfélalkalmazás forgalma alacsony, a lehetséges incidensek elkerülése érdekében.

### <a name="client-network-configuration-changes"></a>Ügyfél hálózati konfigurációjának módosításai

Bizonyos ügyféloldali hálózati konfigurációs módosítások "Nincs elérhető kapcsolat" hibát okozhatnak. Ezek a változások a következőklehetnek:

- Az ügyfélalkalmazás virtuális IP-címének felcserélése az átmeneti és az éles tárolóhelyek között.
- Az alkalmazás példányainak méretének vagy számának méretezése.

Az ilyen módosítások egy percnél rövidebb kapcsolódási problémát okozhatnak. Az ügyfélalkalmazás valószínűleg elveszíti a kapcsolatot más külső hálózati erőforrások mellett az Azure Cache for Redis szolgáltatás.

## <a name="next-steps"></a>További lépések

- [Ütemezze](cache-administration.md#schedule-updates) a gyorsítótár frissítéseit.
- Tesztelje az alkalmazások rugalmasságát [újraindítással.](cache-administration.md#reboot)
- [Állítsa be a](cache-configure.md#memory-policies) memóriafoglalásokat és -házirendeket.
