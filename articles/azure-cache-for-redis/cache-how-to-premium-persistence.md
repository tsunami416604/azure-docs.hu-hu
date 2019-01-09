---
title: Adatok megőrzését egy prémium szintű Azure Cache Redis konfigurálása
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az adatmegőrzést a prémium szintű Azure Cache a Redis-példány
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 60f9baf7fb54706dc9d31c6920c0df24173d7b35
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105846"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Adatok megőrzését egy prémium szintű Azure Cache Redis konfigurálása
Az Azure Cache redis rendelkezik másik Cache gyorsítótárazási szolgáltatások, ami rugalmasságot biztosít a gyorsítótár méretét és a szolgáltatásait, beleértve a Prémiumszintű funkciókkal, például a fürtözés, az adatmegőrzés és a virtuálishálózat-támogatást is nyújt. Ez a cikk ismerteti egy prémium szintű Azure Cache Redis-példányt az adatmegőrzés konfigurálása.

Más prémiumszintű gyorsítótár funkcióival kapcsolatos tudnivalókért lásd: [Bevezetés az Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Mi az adatmegőrzés?
[Redis-adatmegőrzés](https://redis.io/topics/persistence) segítségével megőrizheti a Redis-ban tárolt adatokkal. Pillanatfelvételt és biztonsági mentése az adatok betölthetők egy hardverhiba. Ez az egy hatalmas használja ki az alapszintű vagy Standard csomag, ahol a memóriában tárolt összes adatot és az esetleges adatvesztés, ahol a gyorsítótár-csomópontok le hiba esetén is lehet. 

Az Azure Cache redis kínál a Redis megőrzési funkciója az alábbi minták használatával:

* **RDB-fájlba való megőrzését** – Ha RDB (Redis-adatbázis) adatmegőrzés van konfigurálva, az Azure Cache redis az Azure Cache pillanatképet egy redis-lemezre bináris formátumot egy konfigurálható biztonsági mentési gyakoriság alapján redis továbbra is fennáll. Ha egy katasztrofális esemény történik, amely letiltja az elsődleges és replika gyorsítótár, a gyorsítótár újraépíti a legutóbbi pillanatképből. Tudjon meg többet a [előnyeit](https://redis.io/topics/persistence#rdb-advantages) és [hátrányait](https://redis.io/topics/persistence#rdb-disadvantages) RDB-fájlba való maradandó.
* **AOF adatmegőrzés** – Ha AOF (csak fájl hozzáfűzése) adatmegőrzés van konfigurálva, Azure Cache redis minden írási művelet menti egy Azure Storage-fiókba másodpercenként legalább egyszer mentett naplóba. Ha egy katasztrofális esemény történik, amely letiltja az elsődleges és replika gyorsítótár, a gyorsítótár újraépíti a tárolt írási műveletek használatával. Tudjon meg többet a [előnyeit](https://redis.io/topics/persistence#aof-advantages) és [hátrányait](https://redis.io/topics/persistence#aof-disadvantages) AOF maradandó.

Adatmegőrzés állítható a **új Azure Cache redis** gyorsítótár létrehozása során, majd a panel a **erőforrás menüben** meglévő prémium gyorsítótárazza.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Amikor prémium tarifacsomag be van jelölve, kattintson a **Redis-adatmegőrzés**.

![Redis-adatmegőrzés][redis-cache-persistence]

A következő szakaszban ismertetett lépések bemutatják, hogyan Redis-adatmegőrzés konfigurálása az új prémium szintű gyorsítótár. Miután a Redis megőrzési funkciója van konfigurálva, kattintson a **létrehozás** az új prémium szintű gyorsítótár létrehozása a Redis megőrzési funkciója.

## <a name="enable-redis-persistence"></a>A Redis-adatmegőrzés engedélyezése

Redis adatmegőrzés engedélyezve van a **Redis-adatmegőrzés** panelen válassza ki vagy **RDB-fájlba való** vagy **AOF** megőrzését. Az új gyorsítótárakhoz ezen a panelen érhető el a gyorsítótár létrehozása során az előző szakaszban leírtak szerint. Meglévő gyorsítótárak esetében a **Redis-adatmegőrzés** panelen érhető el a **erőforrás menüben** a gyorsítótárhoz.

![A redis-beállítások][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RDB-fájlba való adatmegőrzés konfigurálása

RDB-fájlba való adatmegőrzés engedélyezéséhez kattintson **RDB-fájlba való**. Az előzőekben engedélyezett prémium gyorsítótár RDB-fájlba való megőrzését letiltásához kattintson **letiltott**.

![A redis RDB-fájlba való megőrzése][redis-cache-rdb-persistence]

A biztonsági mentési időköz konfigurálásához válasszon egy **biztonsági mentés gyakorisága** a legördülő listából. Választási lehetőségek **15 perc**, **30 perc**, **60 perc**, **6 óra**, **12 óra**, és **24 óra**. Ez az időtartam alatt elindítja a leltár, miután az előző biztonsági mentési művelet sikeresen befejeződött, és eltelt, amikor egy új biztonsági másolatot a rendszer kezdeményezi.

Kattintson a **Tárfiók** , válassza ki a tárfiókot használja, és válassza a **elsődleges kulcs** vagy **másodlagos kulcs** használatát a **Tárkulcs** listából. Ki kell választania a gyorsítótárat, és ugyanabban a régióban a storage-fiók és a egy **prémium szintű Storage** fiók ajánlott, mert a premium storage nagyobb átviteli sebességet. 

> [!IMPORTANT]
> Ha a tárfiók hívóbetűjét, az adatmegőrzés fiók újragenerálják, újra kell konfigurálnia a kívánt kulcsot a **Tárkulcs** listából.
> 
> 

Kattintson a **OK** adatmegőrzési-konfigurációjának mentése.

A biztonsági mentés gyakorisága időtartam után a rendszer kezdeményezi a következő biztonsági mentés (vagy az új gyorsítótárakhoz első biztonsági mentés).

## <a name="configure-aof-persistence"></a>AOF adatmegőrzés konfigurálása

AOF adatmegőrzés engedélyezéséhez kattintson **AOF**. Az előzőekben engedélyezett prémium gyorsítótár AOF adatmegőrzés letiltásához kattintson **letiltott**.

![Redis-adatmegőrzés AOF][redis-cache-aof-persistence]

AOF adatmegőrzés konfigurálása, adjon meg egy **első Tárfiók**. Ez a tárfiók a gyorsítótárat ugyanabban a régióban kell lennie és a egy **prémium szintű Storage** fiók ajánlott, mert a premium storage nagyobb átviteli sebességet. Igény szerint konfigurálható egy tárfiókot nevű **második Tárfiók**. Ha egy második tárfiók van konfigurálva, az írási műveletek a replika gyorsítótár írja a második tárfiók. Válassza minden beállított storage-fiókok a **elsődleges kulcs** vagy **másodlagos kulcs** használatát a **Tárkulcs** listából. 

> [!IMPORTANT]
> Ha a tárfiók hívóbetűjét, az adatmegőrzés fiók újragenerálják, újra kell konfigurálnia a kívánt kulcsot a **Tárkulcs** listából.
> 
> 

Adatmegőrzés AOF engedélyezve van, ha írási műveletek a gyorsítótárba menti a kijelölt tárfiók (vagy ha egy második tárfiók konfigurált fiókok). Katasztrofális hiba, amely az elsődleges és replika gyorsítótár le a tárolt AOF napló segítségével építse újra a gyorsítótárhoz.

## <a name="persistence-faq"></a>Adatmegőrzés – gyakori kérdések
Az alábbi lista a Redis megőrzési funkciója az Azure Cache kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Egy korábban létrehozott gyorsítótár az adatmegőrzés engedélyezése](#can-i-enable-persistence-on-a-previously-created-cache)
* [Engedélyezheti a egyszerre AOF és RDB-fájlba való megőrzését?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Adatmegőrzés modellt érdemes választani?](#which-persistence-model-should-i-choose)
* [Mi történik, ha szeretnék méretezte más méretre és a biztonsági másolat visszaállítása, amely a skálázási művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB-fájlba való megőrzése
* [Módosíthatom a RDB-fájlba való biztonsági mentés gyakoriságát, a gyorsítótár létrehozása után?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Miért van egy RDB-fájlba való biztonsági mentés gyakorisága 60 perc van-e több mint 60 perc között biztonsági mentések?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Mi történik a régi RDB-fájlba való biztonsági mentést egy új biztonsági másolat készül?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF adatmegőrzés
* [Mikor célszerű használni egy második tárfiókot?](#when-should-i-use-a-second-storage-account)
* [Nem egész AOF adatmegőrzés hatással, várakozási ideje vagy a gyorsítótár teljesítményének?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hogyan távolíthatja el a második tárfiók?](#how-can-i-remove-the-second-storage-account)
* [Mi az, hogy egy újraírást, és hogyan befolyásolja a gyorsítótár?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Mit kell számítson, ha a gyorsítótár horizontális az AOF engedélyezve van?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hogyan rendszerezése a storage AOF adataimat?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Egy korábban létrehozott gyorsítótár az adatmegőrzés engedélyezése
Igen, a Redis megőrzési funkciója konfigurálható, mind pedig a gyorsítótár létrehozása a meglévő prémium gyorsítótárak.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Engedélyezheti a egyszerre AOF és RDB-fájlba való megőrzését?

Nem, csak RDB-fájlba való vagy AOF, de nem mindkettőt egyszerre is engedélyezheti.

### <a name="which-persistence-model-should-i-choose"></a>Adatmegőrzés modellt érdemes választani?

AOF adatmegőrzés minden írási menti a napló, amely hatással van az átviteli sebesség, RDB-fájlba való megőrzési funkció, amely tárolja a biztonsági mentések teljesítményére gyakorolt minimális hatása mellett a beállított biztonsági mentési időköz alapján képest. AOF adatmegőrzés akkor válassza, ha az elsődleges cél, hogy az adatvesztést, és a gyorsítótár kezelhetik a teljesítmény csökkenését. Válassza ki a RDB-fájlba való megőrzését, ha szeretné a gyorsítótár az optimális átviteli sebesség szinten tartható, de továbbra is szeretné az adat-helyreállítási mechanizmusként.

* Tudjon meg többet a [előnyeit](https://redis.io/topics/persistence#rdb-advantages) és [hátrányait](https://redis.io/topics/persistence#rdb-disadvantages) RDB-fájlba való maradandó.
* Tudjon meg többet a [előnyeit](https://redis.io/topics/persistence#aof-advantages) és [hátrányait](https://redis.io/topics/persistence#aof-disadvantages) AOF maradandó.

A teljesítmény AOF adatmegőrzés használata esetén további információkért lásd: [Does AOF adatmegőrzés hatással egész, késés és a gyorsítótár teljesítményének?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Mi történik, ha szeretnék méretezte más méretre és a biztonsági másolat visszaállítása, amely a skálázási művelet előtt történt?

Az RDB-fájlba való és a AOF megőrzése:

* Ha méretezte egy nagyobb méretű, ez nincs hatással.
* Ha méretezte egy kisebb méretű, és a egy egyéni rendelkezik [adatbázisok](cache-configure.md#databases) beállítás értéke nagyobb, mint a [adatbázisok korlát](cache-configure.md#databases) az új méret ezeket az adatbázisokat a adatainak visszaállítása nem. További információkért lásd: [saját egyéni adatbázisok méretezése során érintett beállítás?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Ha méretezte egy kisebb méretű, és nincs elég hely a kisebb méretű az utolsó biztonsági adatok tárolására, kulcsok ki lesz zárva a visszaállítás során, általában a [allkeys-lru](https://redis.io/topics/lru-cache) kiürítési szabályzatot.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Módosíthatom a RDB-fájlba való biztonsági mentés gyakoriságát, a gyorsítótár létrehozása után?
Igen, módosíthatja a biztonsági mentés gyakorisága RDB-fájlba való megőrzésre a **Redis-adatmegőrzés** panelen. Útmutatásért lásd: [megőrzése a redis Cache](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Miért van egy RDB-fájlba való biztonsági mentés gyakorisága 60 perc van-e több mint 60 perc között biztonsági mentések?
Az RDB-fájlba való megőrzését biztonsági mentési időköz nem indul el addig, amíg az előző biztonsági mentési folyamat sikeresen befejeződött. Ha a biztonsági mentés gyakorisága 60 perc és egy biztonsági mentési folyamat 15 percet vesz sikeresen befejeződik, a következő biztonsági mentés az előző biztonsági mentés kezdő időpontja után 75 perccel nem indul.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Mi történik a régi RDB-fájlba való biztonsági mentést egy új biztonsági másolat készül?
Legutóbbi kivételével az összes RDB-fájlba való megőrzését biztonsági automatikusan törlődnek. Ez a törlés nem fordulhat elő, azonnal, de a régebbi biztonsági másolatok határozatlan ideig nem rögzíti.


### <a name="when-should-i-use-a-second-storage-account"></a>Mikor célszerű használni egy második tárfiókot?

AOF megőrzéshez egy második tárfiókot használjon, ha úgy gondolja, hogy a magasabb, mint a gyorsítótár várt halmazműveletek rendelkezik.  A másodlagos tárfiók beállítása biztosíthatja a gyorsítótár nem éri el a storage sávszélességkorláttal rendelkeznek.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Nem egész AOF adatmegőrzés hatással, várakozási ideje vagy a gyorsítótár teljesítményének?

AOF adatmegőrzés befolyásolja átviteli sebesség szerint körülbelül 15 – 20 % amikor a gyorsítótár maximális terhelés alatt (Processzor és a kiszolgáló mindkét betölteni a 90 %). Nem kell késési problémák amikor a gyorsítótár ezeken a határokon. Azonban a gyorsítótárban fogják tudni elérni ezeket a korlátokat hamarabb az AOF engedélyezve van.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hogyan távolíthatja el a második tárfiók?

Eltávolíthatja az AOF adatmegőrzés másodlagos tárfiók ugyanaz, mint az első storage-fiókot kell a második tárfiók beállításával. Útmutatásért lásd: [konfigurálása AOF adatmegőrzés](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Mi az, hogy egy újraírást, és hogyan befolyásolja a gyorsítótár?

Az AOF fájl lesz elég nagy, amikor egy újraírási automatikusan várólistára helyezve a gyorsítótárban. Az újraírási átméretezi a AOF-fájlt a minimálisan szükséges az aktuális adatkészlet létrehozásához szükséges műveleteket. Újraírások, során várható teljesítménykorlátok hamarabb elérni, különösen akkor, ha nagy adatkészletekkel. Újraírások elő kisebb gyakran az AOF fájl nagyobb lesz, de eltarthat egy jelentős mennyiségű időt, amikor történik.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Mit kell számítson, ha a gyorsítótár horizontális az AOF engedélyezve van?

Skálázás alkalmával AOF fájl esetén jelentősen nagy hatással vannak a skálázási művelet, mert azt fogja betöltődnek a fájl méretezés befejezése után a vártnál több időt vesz igénybe.

Méretezéssel kapcsolatos további információkért lásd: [mi történik, ha szeretnék méretezte más méretre és a biztonsági másolat visszaállítása, amely a skálázási művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hogyan rendszerezése a storage AOF adataimat?

AOF-fájlokban tárolt adatok mentése folyamatban van az adatok tárolási teljesítményének növelése érdekében csomópontonként több lapblobok oszlik. Az alábbi táblázat jeleníti meg, hány lapblobok minden egyes tarifacsomagja használhatók:

| Premium szintű csomag | Blobok |
|--------------|-------|
| P1           | 4 partíciónkénti    |
| P2           | 8 partíciónkénti    |
| P3           | 16 partíciónkénti   |
| P4           | 20 partíciónkénti   |

Ha fürtözés engedélyezve van, minden egyes szegmens a gyorsítótárban rendelkezik a saját lapblobok, az előző táblázatban leírtak szerint. Három szegmensek P2 szintű gyorsítótár például elosztja az AOF fájl 24 lapblobok (a 3 szegmensek partíciónkénti 8 blobokat).

Egy újraírási után két készletnyi AOF-fájlokat tároló szerepel. Újraírások a háttérben történik, és első beállítását, hozzáfűzése, amíg a gyorsítótár átírása során küldött halmazműveletek hozzáfűzése a második csoporton. A biztonsági mentés során hiba esetén újraírások ideiglenesen tárolja, de egy újraírási befejezése után azonnal törlődik.


## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan használja a további prémiumszintű gyorsítótár funkcióival.

* [Bevezetés az Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
