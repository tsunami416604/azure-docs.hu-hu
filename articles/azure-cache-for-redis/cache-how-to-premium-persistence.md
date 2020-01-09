---
title: Az adatmegőrzés konfigurálása – prémium szintű Azure cache a Redis-hez
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az adatmegőrzést a prémium szintű Azure cache Redis-példányok esetében
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 6ff7500712f57d7cf2adad1fc73f68a29f3afc20
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412824"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Az adatmegőrzés konfigurálása prémium szintű Azure cache-Redis
A Redis készült Azure cache különböző gyorsítótárazási ajánlatokat tartalmaz, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és funkcióinak, beleértve a prémium szintű funkciókat, például a fürtözést, az adatmegőrzést és a virtuális hálózatok támogatását. Ez a cikk bemutatja, hogyan konfigurálhatja az adatmegőrzést egy prémium szintű Azure cache-ben a Redis-példány esetében.

További információ a prémium szintű gyorsítótár-funkciókról: [Bevezetés az Azure cache for Redis Premium](cache-premium-tier-intro.md)csomagba.

## <a name="what-is-data-persistence"></a>Mi az adatmegőrzés?
A Redis megőrzése lehetővé teszi a Redis-ben tárolt [adatmegőrzést](https://redis.io/topics/persistence) . Pillanatképeket és biztonsági mentést is készíthet, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az alapszintű és a standard szint esetében, ahol az összes adat a memóriában tárolódik, és előfordulhat, hogy a gyorsítótár-csomópontok leállításakor hiba történt. 

A Redis-hez készült Azure cache a következő modellek használatával nyújt Redis-megőrzést:

* **RDB-megőrzés** – ha a RDB (Redis-adatbázis) megőrzése konfigurálva van, az Azure cache a Redis számára megőrzi az Azure cache-t a Redis bináris formátumból a lemezre egy konfigurálható biztonsági mentési gyakoriság alapján. Ha végzetes esemény következik be, amely letiltja az elsődleges és a replika gyorsítótárat is, a rendszer a legutóbbi pillanatkép használatával újraépíti a gyorsítótárat. További információ az RDB megőrzésének [előnyeiről](https://redis.io/topics/persistence#rdb-advantages) és [hátrányairól](https://redis.io/topics/persistence#rdb-disadvantages) .
* **AOF-megőrzés** – ha a AOF (csak Hozzáfűzés) adatmegőrzés konfigurálva van, az Azure cache for Redis minden írási műveletet egy olyan naplóba ment, amely másodpercenként legalább egyszer ment egy Azure Storage-fiókba. Ha olyan katasztrofális esemény következik be, amely letiltja az elsődleges és a replika gyorsítótárat is, a gyorsítótárat a tárolt írási műveletek használatával rekonstruáljuk. További információ az AOF megőrzésének [előnyeiről](https://redis.io/topics/persistence#aof-advantages) és [hátrányairól](https://redis.io/topics/persistence#aof-disadvantages) .

Az adatmegőrzés a **Redis panel új Azure-gyorsítótárában** , a gyorsítótár létrehozásakor és a meglévő prémium gyorsítótárak **erőforrás menüjében** van konfigurálva.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

A prémium szintű árképzési szint kiválasztása után kattintson a **Redis megőrzése**elemre.

![Redis megőrzése][redis-cache-persistence]

A következő szakaszban ismertetett lépések azt ismertetik, hogyan konfigurálható a Redis megőrzése az új prémium szintű gyorsítótárban. Ha a Redis megőrzése beállítás be van állítva, a **Létrehozás** gombra kattintva hozza létre az új prémium gyorsítótárat a Redis-megőrzéssel.

## <a name="enable-redis-persistence"></a>Redis megőrzésének engedélyezése

A Redis-megőrzés engedélyezve van a **Redis adatmegőrzési** paneljén a **RDB** vagy a **AOF** -megőrzés kiválasztásával. Új gyorsítótárak esetén ez a panel a gyorsítótár-létrehozási folyamat során érhető el az előző szakaszban leírtak szerint. A meglévő gyorsítótárak esetében a **Redis adatmegőrzési** panelje a gyorsítótár **erőforrás menüjéből** érhető el.

![Redis-beállítások][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RDB megőrzésének konfigurálása

A RDB megőrzésének engedélyezéséhez kattintson a **RDB**elemre. Ha le szeretné tiltani a RDB megőrzését egy korábban engedélyezett prémium gyorsítótárban, kattintson a **Letiltva**gombra.

![Redis RDB megőrzése][redis-cache-rdb-persistence]

A biztonsági mentési időköz konfigurálásához válassza ki a **biztonsági mentés gyakoriságát** a legördülő listából. A választható lehetőségek közé tartozik a **15 perc**, **30 perc**, **60 perc**, **6 óra**, **12 óra**és **24 óra**. Ez az intervallum az előző biztonsági mentési művelet sikeres befejezését és az új biztonsági mentés eltelte után kezdődik.

Kattintson **a Storage-fiók** lehetőségre a használni kívánt Storage-fiók kiválasztásához, majd válassza ki az **elsődleges kulcsot** vagy a **másodlagos kulcsot** , amelyet a **tárolási kulcs** legördülő listából szeretne használni. Ki kell választania egy Storage-fiókot a gyorsítótárral azonos régióban, és a **Premium Storage** fiók használata javasolt, mivel a Premium Storage nagyobb átviteli sebességgel rendelkezik. 

> [!IMPORTANT]
> Ha az adatmegőrzési fiók tárolási kulcsa újragenerált, újra kell konfigurálnia a kívánt kulcsot a **tárolási kulcs** legördülő menüjéből.
> 
> 

Az adatmegőrzési konfiguráció mentéséhez kattintson **az OK** gombra.

A következő biztonsági mentés (vagy az új gyorsítótárak első biztonsági mentése) a biztonsági mentés gyakorisági intervallumának eltelte után indul el.

## <a name="configure-aof-persistence"></a>AOF megőrzésének konfigurálása

A AOF megőrzésének engedélyezéséhez kattintson a **AOF**elemre. Ha le szeretné tiltani a AOF megőrzését egy korábban engedélyezett prémium gyorsítótárban, kattintson a **Letiltva**gombra.

![Redis AOF megőrzése][redis-cache-aof-persistence]

A AOF megőrzésének konfigurálásához adja meg az **első Storage-fiókot**. Ennek a Storage-fióknak ugyanabban a régióban kell lennie, mint a gyorsítótárnak, és a **Premium Storage** fiók használata ajánlott, mert a Premium Storage nagyobb átviteli sebességgel rendelkezik. Opcionálisan beállíthat egy **második Storage-fiók**nevű további Storage-fiókot is. Ha egy második Storage-fiók van konfigurálva, a replika gyorsítótárba való írás a második Storage-fiókba kerül. Minden konfigurált Storage-fióknál válassza ki a **Storage-kulcs** legördülő menüjében használni kívánt **elsődleges kulcsot** vagy **másodlagos kulcsot** . 

> [!IMPORTANT]
> Ha az adatmegőrzési fiók tárolási kulcsa újragenerált, újra kell konfigurálnia a kívánt kulcsot a **tárolási kulcs** legördülő menüjéből.
> 
> 

Ha a AOF megőrzése engedélyezve van, a gyorsítótárba való írási műveletek a kijelölt Storage-fiókba lesznek mentve (vagy ha konfigurált egy második Storage-fiókot). Abban az esetben, ha az elsődleges és a replika gyorsítótárat is igénybe vevő végzetes hiba esetén a rendszer a tárolt AOF-naplót használja a gyorsítótár újraépítéséhez.

## <a name="persistence-faq"></a>Adatmegőrzés – gyakori kérdések
Az alábbi lista az Azure cache Redis-megőrzési szolgáltatásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Engedélyezhető az adatmegőrzés egy korábban létrehozott gyorsítótárban?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Egyszerre engedélyezhető a AOF és a RDB megőrzése?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Milyen adatmegőrzési modellt választok?](#which-persistence-model-should-i-choose)
* [Mi történik, ha egy másik méretre skálázást végezek, és a rendszer visszaállítja a biztonsági mentést, amely a skálázási művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB megőrzése
* [Módosíthatom a RDB biztonsági mentésének gyakoriságát a gyorsítótár létrehozása után?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Miért van a RDB biztonsági mentés gyakorisága 60 percnél több, mint 60 perc a biztonsági másolatok között?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Mi történik a régi RDB biztonsági mentésével, amikor új biztonsági mentés készül?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF megőrzése
* [Mikor érdemes egy második Storage-fiókot használni?](#when-should-i-use-a-second-storage-account)
* [A AOF-megőrzés a gyorsítótár egészére, késésére vagy teljesítményére is hatással van?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hogyan lehet eltávolítani a második Storage-fiókot?](#how-can-i-remove-the-second-storage-account)
* [Mi az az újraírás, és hogyan befolyásolja a gyorsítótárat?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Mit várhatok a gyorsítótár és a AOF engedélyezése esetén?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hogyan történik a AOF-adataim tárolása?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Engedélyezhető az adatmegőrzés egy korábban létrehozott gyorsítótárban?
Igen, a Redis megőrzése a gyorsítótár létrehozásakor és a meglévő prémium szintű gyorsítótárban is konfigurálható.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Egyszerre engedélyezhető a AOF és a RDB megőrzése?

Nem, engedélyezheti csak a RDB vagy a AOF, de egyszerre nem.

### <a name="which-persistence-model-should-i-choose"></a>Milyen adatmegőrzési modellt választok?

A AOF megőrzése minden írást egy naplóba ment, amely hatással van az átviteli sebességre, a RDB-megőrzéssel összehasonlítva, amely a biztonsági mentéseket a beállított biztonsági mentési intervallum alapján menti, és minimális hatással van a teljesítményre. Válassza a AOF megőrzése lehetőséget, ha az elsődleges célja az adatvesztés csökkentése, és az átviteli sebesség csökkentése a gyorsítótárban. Válassza a RDB megőrzése lehetőséget, ha az optimális teljesítményt szeretné fenntartani a gyorsítótárban, de továbbra is szeretne egy mechanizmust használni az adatok helyreállításához.

* További információ az RDB megőrzésének [előnyeiről](https://redis.io/topics/persistence#rdb-advantages) és [hátrányairól](https://redis.io/topics/persistence#rdb-disadvantages) .
* További információ az AOF megőrzésének [előnyeiről](https://redis.io/topics/persistence#aof-advantages) és [hátrányairól](https://redis.io/topics/persistence#aof-disadvantages) .

A AOF megőrzése esetén a teljesítményre vonatkozó további információkért lásd: a [AOF megőrzése a gyorsítótár egészére, késésére vagy teljesítményére vonatkozik?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Mi történik, ha egy másik méretre skálázást végezek, és a rendszer visszaállítja a biztonsági mentést, amely a skálázási művelet előtt történt?

Mind a RDB, mind a AOF megőrzése esetén:

* Ha nagyobb méretre méretezett, nincs hatása.
* Ha kisebb méretre méretezett, és rendelkezik olyan egyéni [adatbázis](cache-configure.md#databases) -beállítással, amely nagyobb, mint az új mérethez tartozó [adatbázis-korlát](cache-configure.md#databases) , az ezekben az adatbázisokban tárolt adatmennyiség nem lesz visszaállítva. További információ: az [Egyéni adatbázisok beállítása a skálázás során?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Ha kisebb méretre méretezett, és nincs elég hely a kisebb méretekben az utolsó biztonsági mentésből származó összes adatok tárolásához, a kulcsok a visszaállítási folyamat során törlődnek, jellemzően a [allkeys-LRU](https://redis.io/topics/lru-cache) kizárási házirend használatával.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Módosíthatom a RDB biztonsági mentésének gyakoriságát a gyorsítótár létrehozása után?
Igen, a **Redis adatmegőrzési** paneljén módosíthatja a RDB megőrzésének biztonsági mentésének gyakoriságát. Útmutatásért lásd: a Redis megőrzésének konfigurálása.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Miért van a RDB biztonsági mentés gyakorisága 60 percnél több, mint 60 perc a biztonsági másolatok között?
A RDB megőrzése biztonsági mentési gyakorisága nem indul el, amíg az előző biztonsági mentési folyamat sikeresen befejeződött. Ha a biztonsági mentés gyakorisága 60 perc, és a biztonsági mentési folyamat 15 percet vesz igénybe, a következő biztonsági mentés nem indul el, amíg az előző biztonsági mentés kezdési időpontja nem fog megjelenni a 75 percben.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Mi történik a régi RDB biztonsági mentésével, amikor új biztonsági mentés készül?
Az összes RDB-megőrzési biztonsági mentést, kivéve a legutóbbit, automatikusan törlődnek. Előfordulhat, hogy ez a törlés nem azonnal történik, de a régebbi biztonsági mentések határozatlan ideig nem maradnak meg.


### <a name="when-should-i-use-a-second-storage-account"></a>Mikor érdemes egy második Storage-fiókot használni?

Ha úgy gondolja, hogy a gyorsítótárban a vártnál több művelet van, használjon egy második Storage-fiókot a AOF megőrzéséhez.  A másodlagos Storage-fiók beállítása segít biztosítani, hogy a gyorsítótár ne érje el a tárolási sávszélesség korlátozásait.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A AOF-megőrzés a gyorsítótár egészére, késésére vagy teljesítményére is hatással van?

A AOF-megőrzés az átviteli sebességet körülbelül 15% – 20%-kal befolyásolja, ha a gyorsítótár a maximális terhelés alatt van (a processzor és a kiszolgáló terhelése egyaránt 90% alatt). A gyorsítótár ezen korlátok között nem lehet késéssel kapcsolatos probléma. A gyorsítótár azonban hamarabb eléri ezeket a korlátokat, ha a AOF engedélyezve van.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hogyan lehet eltávolítani a második Storage-fiókot?

A AOF megőrzése másodlagos Storage-fiókot úgy távolíthatja el, hogy a második Storage-fiókot az első Storage-fiókkal megegyezőre állítja. Útmutatásért lásd: a [AOF megőrzésének konfigurálása](#configure-aof-persistence).

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Mi az az újraírás, és hogyan befolyásolja a gyorsítótárat?

Ha a AOF-fájl mérete elég nagy, az újraírás automatikusan várólistára kerül a gyorsítótárban. Az újraírás átméretezi a AOF-fájlt az aktuális adathalmaz létrehozásához szükséges minimális műveletek közül. Az újraírások során várhatóan a teljesítményre vonatkozó korlátok hamarabb, különösen a nagyméretű adatkészletek kezelésekor is elérhetők. Az újraírások ritkábban történnek, mert a AOF-fájl nagyobb lesz, de jelentős időt vesz igénybe.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Mit várhatok a gyorsítótár és a AOF engedélyezése esetén?

Ha a skálázáskor a AOF-fájl mérete jelentősen nagyobb, akkor a méretezési művelet a vártnál hosszabb időt vesz igénybe, mert a skálázás befejeződése után újra be lesz töltve a fájl.

A skálázással kapcsolatos további információkért lásd: [Mi történik, ha egy másik méretre skálázást végezek, és a rendszer visszaállítja a biztonsági mentést, amely a skálázási művelet előtt történt?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hogyan történik a AOF-adataim tárolása?

A AOF-fájlokban tárolt adatmennyiségek csomóponton több blobra vannak osztva, így növelhetik az adattárolóba való mentés teljesítményét. Az alábbi táblázat azt mutatja, hogy az egyes díjszabási szintek hány oldal blobot használnak:

| Premium szint | Blobok |
|--------------|-------|
| P1           | 4/szilánk    |
| P2           | 8/szilánk    |
| P3           | 16/szilánk   |
| P4           | 20/szegmens   |

Ha a fürtözés engedélyezve van, a gyorsítótárban lévő összes szegmens saját blob-készlettel rendelkezik, ahogy azt az előző táblázatban is említettük. Egy három szegmensből álló P2-gyorsítótár például elosztja a AOF-fájlt a 24 oldal Blobok között (8 blob/szilánk, 3 szegmenssel).

Az újraírást követően két AOF-fájl létezik a tárolóban. Az újraírások a háttérben történnek, és a rendszer hozzáfűzi a fájlok első készletéhez, míg a gyorsítótárba az újraírás során elküldett műveleteket a második készlethez adja meg. Hiba esetén a rendszer átmenetileg tárolja a biztonsági mentést, de az újraírás befejeződése után azonnal törölve lesz.


## <a name="next-steps"></a>Következő lépések
További információ a prémium szintű gyorsítótár-funkciók használatáról.

* [A prémium szintű Redis készült Azure cache bemutatása](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
