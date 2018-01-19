---
title: "A prémium szintű Azure Redis Cache adatmegőrzés konfigurálásáról"
description: "Megtudhatja, hogyan konfigurálhatja és kezelheti az adatmegőrzés a Premium szint Azure Redis Cache példányt"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: wesmc
ms.openlocfilehash: 270158bbf85a58a48a367a091ad2b09a9d114b2b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>A prémium szintű Azure Redis Cache adatmegőrzés konfigurálásáról
Azure Redis Cache-gyorsítótár mérete és a szolgáltatások, például a fürtszolgáltatás, az adatmegőrzésre és a virtuális hálózat támogatásának Premium szint szolgáltatásainak rugalmasság biztosító másik gyorsítótármappa ajánlatok rendelkezik. Ez a cikk ismerteti, hogyan adatmegőrzés konfigurálása prémium szintű Azure Redis Cache példányt.

Más prémium gyorsítótár funkciókról további információért lásd: [az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Mi az adatmegőrzés?
[Redis-adatmegőrzés](https://redis.io/topics/persistence) lehetővé teszi a Redis tárolt adatok megőrzéséhez. Pillanatképek és hardver meghibásodása esetén is betölthet adatainak biztonsági mentését is. Ez az egy hatalmas előnyös használhatóságát Basic vagy Standard szint, ahol az adatok a memóriában tárolja, és esetleges adatvesztés, ha a gyorsítótár-csomópontok nem működnek meghibásodása esetén is lehet. 

Azure Redis Cache Redis adatmegőrzési használatával a következő modellt kínál:

* **Rekordadatbázis adatmegőrzési** -amikor Rekordadatbázis (Redis-adatbázis) adatmegőrzési van konfigurálva, az Azure Redis Cache továbbra is fennáll, a Redis gyorsítótárt egy Redis lemezre bináris formátumot egy konfigurálható biztonsági mentési gyakoriság alapján a pillanatképet. Ha egy katasztrofális esemény történik, akkor az elsődleges és a replika-gyorsítótár, a gyorsítótár újraépíti a legutóbbi pillanatkép. További információ a [előnyeit](https://redis.io/topics/persistence#rdb-advantages) és [hátrányai](https://redis.io/topics/persistence#rdb-disadvantages) a Rekordadatbázis megőrzését.
* **AOF adatmegőrzési** -amikor AOF (hozzáfűzés egyetlen fájl) adatmegőrzési van konfigurálva, az Azure Redis Cache minden írási művelet menti az Azure Storage-fiók legalább egyszer másodpercenként mentett napló. Ha egy katasztrofális esemény történik, akkor az elsődleges és a replika-gyorsítótár, a gyorsítótár újraépíti a tárolt írási műveletek használatával. További információ a [előnyeit](https://redis.io/topics/persistence#aof-advantages) és [hátrányai](https://redis.io/topics/persistence#aof-disadvantages) a AOF megőrzését.

Adatmegőrzési állítható a **új Redis Cache** gyorsítótár létrehozása során, majd a panel a **erőforrás menü** meglévő prémium gyorsítótárazza.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

A prémium tarifacsomag kijelölése után kattintson **Redis-adatmegőrzés**.

![Redis-adatmegőrzés][redis-cache-persistence]

A következő szakaszban a lépések azt ismertetik, hogyan Redis-adatmegőrzés konfigurálása az új premium gyorsítótár. Miután beállította a Redis-adatmegőrzés, kattintson a **létrehozása** Redis-adatmegőrzés az új premium gyorsítótár létrehozásához.

## <a name="enable-redis-persistence"></a>A Redis-adatmegőrzés engedélyezése

Redis adatmegőrzési engedélyezve van a **Redis-adatmegőrzés** panelen válassza ki vagy **Rekordadatbázis** vagy **AOF** megőrzését. Az új gyorsítótárakhoz ezen a panelen érhető el a gyorsítótár létrehozása során az előző szakaszban leírtak szerint. Meglévő gyorsítótárak esetében a **Redis-adatmegőrzés** panel elérése a **erőforrás menü** a gyorsítótárhoz.

![Redis beállítások][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>Rekordadatbázis adatmegőrzési konfigurálása

Engedélyezi az adatmegőrzést Rekordadatbázis, kattintson a **Rekordadatbázis**. Az előzőekben engedélyezett prémium gyorsítótár Rekordadatbázis adatmegőrzési letiltásához kattintson **letiltott**.

![Redis Rekordadatbázis adatmegőrzési][redis-cache-rdb-persistence]

A biztonsági mentési időközt konfigurálásához jelölje ki a **biztonsági mentési gyakoriság** a legördülő listából. Választható a **15 perc**, **30 perc**, **60 perc**, **6 óra**, **12 óra**, és **24 óra**. Ez az időtartam alatt elindítja a számbavételi, az előző biztonsági mentési művelet sikeres befejezését, és új biztonsági kezdeményezett amikor azt lejárta után.

Kattintson a **Tárfiók** jelölje be a tárfiók, és válassza a **elsődleges kulcs** vagy **másodlagos kulcs** használata a **kulcs** legördülő listán. Választania kell, a tárfiók ugyanabban a régióban, mint a gyorsítótárban, és egy **prémium szintű Storage** fiók használata ajánlott, mivel a prémium szintű storage rendelkezik nagyobb átviteli sebességgel. 

> [!IMPORTANT]
> Ha újragenerálják a kulcsot az adatmegőrzési fiók, újra kell konfigurálnia a kívánt kulcsát a **Biztonságitár-kulcs** legördülő listán.
> 
> 

Kattintson a **OK** a adatmegőrzési konfigurációjának mentéséhez.

A következő biztonsági mentés (vagy az új gyorsítótárakhoz első biztonsági mentés) a biztonsági mentés gyakoriságát időtartam után indítható.

## <a name="configure-aof-persistence"></a>AOF adatmegőrzési konfigurálása

Engedélyezi az adatmegőrzést AOF, kattintson a **AOF**. Az előzőekben engedélyezett prémium gyorsítótár AOF adatmegőrzési letiltásához kattintson **letiltott**.

![Redis AOF adatmegőrzési][redis-cache-aof-persistence]

Az AOF adatmegőrzési megadásához adjon meg egy **első Tárfiók**. Ezt a tárfiókot a gyorsítótárat ugyanabban a régióban kell lennie, és egy **prémium szintű Storage** fiók használata ajánlott, mivel a prémium szintű storage rendelkezik nagyobb átviteli sebességgel. Konfigurálhatja egy további nevű tárfiók **második Tárfiók**. Ha egy másik tárolási fiókot van konfigurálva, a replika gyorsítótárba írása a második tárfiók kerülnek. Válassza minden konfigurált tárfiók a **elsődleges kulcs** vagy **másodlagos kulcs** használata a **Biztonságitár-kulcs** legördülő. 

> [!IMPORTANT]
> Ha újragenerálják a kulcsot az adatmegőrzési fiók, újra kell konfigurálnia a kívánt kulcsát a **Biztonságitár-kulcs** legördülő listán.
> 
> 

Ha AOF adatmegőrzési engedélyezve van, az írási műveletek a gyorsítótárba kerülnek a kijelölt tárfiókkal (vagy ha egy másik tárolási fiókot konfigurált fiókok). A katasztrofális hibája esetén, amely le az elsődleges és a replika gyorsítótár a tárolt AOF napló szolgál, hogy a gyorsítótár.

## <a name="persistence-faq"></a>Adatmegőrzési – gyakori kérdések
Az alábbi lista az Azure Redis Cache adatmegőrzési gyakran feltett kérdésekre adott válaszokat tartalmazza.

* [A korábban létrehozott gyorsítótáron adatmegőrzés engedélyezése](#can-i-enable-persistence-on-a-previously-created-cache)
* [Egy időben AOF és Rekordadatbázis adatmegőrzési engedélyezése](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Adatmegőrzési modellt kell választanom?](#which-persistence-model-should-i-choose)
* [Mi történik, ha szeretnék rendelkezik méretezve, hogy különböző méretű és biztonsági másolatból állítottak vissza, amely a méretezési művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>Rekordadatbázis adatmegőrzési
* [Módosíthatom a Rekordadatbázis biztonsági mentés gyakoriságát, a gyorsítótár létrehozása után?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Miért Ha egy Rekordadatbázis biztonsági mentési gyakoriság 60 perc több mint 60 perc között van biztonsági mentések?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Mi történik a régi Rekordadatbázis biztonsági mentések, amikor egy új biztonsági másolat legyen?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF adatmegőrzési
* [Mikor kell használni egy másik tárolási fiókot?](#when-should-i-use-a-second-storage-account)
* [AOF adatmegőrzési hatása teljes, a késleltetés vagy a teljesítmény a gyorsítótár nem?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hogyan eltávolíthatja a második tárfiókot?](#how-can-i-remove-the-second-storage-account)
* [Mi az, hogy egy átdolgozás, és hogyan azt befolyásolja a gyorsítótár?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Mit kell várhatnak, amikor a gyorsítótár méretezést, AOF engedélyezve van?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hogyan vannak rendezve AOF adataimat storage?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>A korábban létrehozott gyorsítótáron adatmegőrzés engedélyezése
Igen, a Redis-adatmegőrzés is konfigurálható gyorsítótár létrehozását, mind a meglévő prémium gyorsítótárak.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Egy időben AOF és Rekordadatbázis adatmegőrzési engedélyezése

Nem, csak Rekordadatbázis vagy AOF, de nem mindkettőt egyszerre is engedélyezheti.

### <a name="which-persistence-model-should-i-choose"></a>Adatmegőrzési modellt kell választanom?

AOF adatmegőrzési napló, amely hatással van az átviteli sebesség, minden egyes menti Rekordadatbázis adatmegőrzési, amely tárolja a biztonsági mentési időköz, a teljesítményre gyakorolt minimális hatás mellett alapú biztonsági mentések képest. AOF adatmegőrzési akkor válassza, ha elsődleges célja, hogy a adatveszteség minimálisra, és a teljesítmény csökkenése kezelheti a gyorsítótárhoz. Válassza ki Rekordadatbázis adatmegőrzési, ha optimális átviteli sebességet a gyorsítótár karbantartása, de továbbra is szeretné, hogy egy olyan mechanizmus az adat-helyreállítást.

* További információ a [előnyeit](https://redis.io/topics/persistence#rdb-advantages) és [hátrányai](https://redis.io/topics/persistence#rdb-disadvantages) a Rekordadatbázis megőrzését.
* További információ a [előnyeit](https://redis.io/topics/persistence#aof-advantages) és [hátrányai](https://redis.io/topics/persistence#aof-disadvantages) a AOF megőrzését.

A teljesítményre AOF adatmegőrzési használata esetén további információkért lásd: [Does AOF adatmegőrzési hatása teljes, a késleltetés vagy a teljesítmény a gyorsítótár?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Mi történik, ha szeretnék rendelkezik méretezve, hogy különböző méretű és biztonsági másolatból állítottak vissza, amely a méretezési művelet előtt történt?

A Rekordadatbázis és a AOF megőrzéséhez:

* Ha nagyobb méretűre kell méretezni, akkor ennek nincs hatása.
* Ha Ön rendelkezik méretezve, hogy kisebb méretet, és van egy egyéni [adatbázisok](cache-configure.md#databases) beállítástól, amely érték nagyobb, mint a [adatbázisok korlát](cache-configure.md#databases) az új méretéhez ezeket az adatbázisokat az adatok visszaállítása nem. További információkért lásd: [a skálázás során érintett beállítás egyéni adatbázisok?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Ha Ön rendelkezik méretezve, hogy kisebb méretet, és nincs elegendő hely utolsó biztonsági adatok tárolásához kisebb méretű, kulcsok ki lesz zárva a visszaállítás során, általában használatával a [allkeys-lru](http://redis.io/topics/lru-cache) kiürítés házirend.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Módosíthatom a Rekordadatbázis biztonsági mentés gyakoriságát, a gyorsítótár létrehozása után?
Igen, akkor módosítsa a biztonsági mentési gyakorisága Rekordadatbázis adatmegőrzési a **Redis-adatmegőrzés** panelen. Útmutatásért lásd: [konfigurálása Redis adatmegőrzési](#configure-redis-persistence).

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Miért Ha egy Rekordadatbázis biztonsági mentési gyakoriság 60 perc több mint 60 perc között van biztonsági mentések?
A Rekordadatbázis adatmegőrzési biztonsági mentési időköz nem indul el, amíg az előző biztonsági mentési folyamat sikeresen befejeződött. Ha a biztonsági mentési gyakoriság érték 60 perc, és azt a biztonsági mentés 15 percet vesz igénybe sikeresen befejeződik, a következő biztonsági mentés után a korábbi biztonsági mentés kezdési idejét 75 perccel nem indul.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Mi történik a régi Rekordadatbázis biztonsági mentések, amikor egy új biztonsági másolat legyen?
A legutóbbi egy kivételével az összes Rekordadatbázis adatmegőrzési biztonsági automatikusan törlődnek. Ez a törlés nem fordulhat elő, azonnal, de a régebbi biztonsági másolatok nem határozatlan ideig maradnak meg.


### <a name="when-should-i-use-a-second-storage-account"></a>Mikor kell használni egy másik tárolási fiókot?

Ha úgy véli, magasabb, mint a gyorsítótár várt műveletek van egy másik tárolási fiókot kell használatra AOF adatmegőrzési.  A másodlagos tárfiók beállításával biztosíthatja a gyorsítótár nem eléri a tárolási sávszélesség korlátja.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF adatmegőrzési hatása teljes, a késleltetés vagy a teljesítmény a gyorsítótár nem?

AOF adatmegőrzési befolyásolja átviteli által körülbelül 15 – 20 %, ha a gyorsítótár maximális terhelés alatt van (Processzor- és kiszolgáló egyaránt betöltése 90 %-a). Nem szabadna késési problémák szempontjából a gyorsítótár esetén ezeken a határokon. Azonban a gyorsítótár eléri a működés felső korlátjának hamarabb rendelkező AOF engedélyezve van.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hogyan eltávolíthatja a második tárfiókot?

Eltávolíthatja a AOF adatmegőrzési másodlagos tárfiók úgy, hogy a második tárfiókot az első tárfiókkal azonosnak kell lennie. Útmutatásért lásd: [konfigurálása AOF adatmegőrzési](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Mi az, hogy egy átdolgozás, és hogyan azt befolyásolja a gyorsítótár?

A AOF fájl méretének elég nagy, akkor egy átdolgozás automatikusan a gyorsítótár várólistájára. A átdolgozás átméretezi a minimálisan szükséges az aktuális adatkészlet létrehozásához szükséges műveletek AOF fájl. Újraírások, során várható teljesítménykorlátok hamarabb elérni, különösen akkor, ha nagyméretű adatkészletekkel foglalkozó. Újraírások elő kisebb gyakran a AOF fájl nagyobb válik, de ha történik egy jelentős ideig eltarthat.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Mit kell várhatnak, amikor a gyorsítótár méretezést, AOF engedélyezve van?

Jelentősen nagyobb méretezés időpontjában AOF fájl esetén majd várhatóan az skálázási művelet, mert azt fog kell újból betölteni a fájlt skálázás befejezése után a vártnál tovább tart.

A méretezés további információkért lásd: [mi történik, ha szeretnék rendelkezik méretezve, hogy különböző méretű és biztonsági másolatból állítottak vissza, amely a méretezési művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hogyan vannak rendezve AOF adataimat storage?

AOF fájlban tárolt adatok mentése az adatok tárolási teljesítményének javítása érdekében csomópontonként több lapblobokat van osztva. Az alábbi táblázatban láthatók az egyes tarifacsomagok hány lapblobokat használnak:

| Premium szintű csomag | Blobok |
|--------------|-------|
| P1           | a shard / 4    |
| P2           | a shard 8    |
| P3           | a shard / 16   |
| P4           | a shard 20   |

Amikor a fürtszolgáltatás engedélyezve van, minden egyes shard a gyorsítótárban saját rendelkezik a lapblobokat, az előző táblázatban leírtak szerint. Például három szilánkok P2 gyorsítótár az AOF fájl elosztása 24 lapblobokat (8 blobok / shard, a 3 szilánkok).

Egy átdolgozás után két készlet AOF fájlok tárolási szerepel. Újraírások a háttérben történik, és az első fájlkészlet hozzáfűzése, míg a második készlet hozzáfűzése set műveletek során a módosítsa úgy a gyorsítótár küldött. A biztonsági mentés során hiba esetén újraírások ideiglenesen tárolja, de egy átdolgozás befejeződése után azonnal törlődik.


## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan további premium gyorsítótár-funkciók használatára.

* [Az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
