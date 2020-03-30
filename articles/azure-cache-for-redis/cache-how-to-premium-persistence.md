---
title: Adatmegőrzés konfigurálása – Prémium szintű Azure-gyorsítótár a Redis számára
description: Ismerje meg, hogyan konfigurálhatja és kezelheti az adatmegőrzést a prémium szintű Azure-gyorsítótár redis-példányok esetén
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 84a5b4784a36fb22ae50a7a1ec4fcb7e5ef5b7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245276"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Az adatmegőrzés konfigurálása a Redis prémium szintű Azure-gyorsítótárához
A Redis-gyorsítótár különböző gyorsítótár-ajánlatokkal rendelkezik, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és szolgáltatásainak kiválasztásában, beleértve a prémium szintű funkciókat, például a fürtözést, az adatmegőrzést és a virtuális hálózati támogatást. Ez a cikk ismerteti, hogyan konfigurálhatja az adatmegőrzési egy prémium szintű Azure-gyorsítótár redis-példányban.

Az egyéb prémium szintű gyorsítótár-funkciókról [a Bevezetés az Azure Cache for Redis Premium tier című témakörbe.](cache-premium-tier-intro.md)

## <a name="what-is-data-persistence"></a>Mi az adatmegőrzés?
[A Redis perzisztenciája](https://redis.io/topics/persistence) lehetővé teszi a Redis-ben tárolt adatok megőrzését. Pillanatképeket is készíthet, és biztonsági másolatot készíthet az adatokról, amelyeket hardverhiba esetén betölthet. Ez óriási előnye az alapszintű vagy standard szintű, ahol az összes adatot tárolja a memóriában, és nem lehet lehetséges adatvesztés hiba esetén, ahol a gyorsítótár csomópontok nem. 

A Redis Azure Cache for Redis a következő modellek használatával kínálja a Redis-megőrzést:

* **RDB-megőrzés –** Ha RDB (Redis-adatbázis) adatmegőrzési konfigurálva van, az Azure Cache for Redis megőrzi az Azure Cache for Redis egy Redis bináris formátumban a lemez egy konfigurálható biztonsági mentési gyakoriság alapján. Ha egy olyan katasztrofális esemény következik be, amely letiltja mind az elsődleges, mind a replika-gyorsítótárat, a gyorsítótár a legutóbbi pillanatkép használatával rekonstruálódik. Tudjon meg többet az RDB perzisztenciájának [előnyeiről](https://redis.io/topics/persistence#rdb-advantages) és [hátrányairól.](https://redis.io/topics/persistence#rdb-disadvantages)
* **AOF adatmegőrzés –** Ha a AOF (csak hozzáfűzése fájl) adatmegőrzési konfigurálása, az Azure Cache for Redis ment minden írási műveletet egy naplóba, amely menti legalább másodpercenként egyszer egy Azure Storage-fiókba. Ha olyan katasztrofális esemény következik be, amely letiltja mind az elsődleges, mind a replika-gyorsítótárat, a gyorsítótár a tárolt írási műveletek használatával rekonstruálódik. Tudjon meg többet az AOF perzisztenciájának [előnyeiről](https://redis.io/topics/persistence#aof-advantages) és [hátrányairól.](https://redis.io/topics/persistence#aof-disadvantages)

Az adatmegőrzés redis-adatokat ír egy Azure Storage-fiókba, amely az Ön tulajdonában van és kezeli. Konfigurálhatja az **új Azure-gyorsítótár Redis** panel gyorsítótár létrehozása során, és az **erőforrás menüben** a meglévő prémium szintű gyorsítótárak.

> [!NOTE]
> 
> Az Azure Storage automatikusan titkosítja az adatokat, ha azok megőrződnek. Használhatja a saját kulcsokat a titkosításhoz. További információ: [Ügyfél által felügyelt kulcsok az Azure Key Vault.](/azure/storage/common/storage-service-encryption)
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Ha kiválasztott egy prémium szintű tarifacsomagot, kattintson a **Redis adatmegőrzési gombra.**

![Redis perzisztencia][redis-cache-persistence]

A következő szakaszban leírt lépések ismertetik, hogyan konfigurálhatja a Redis adatmegőrzési az új prémium szintű gyorsítótár. Miután a Redis adatmegőrzési konfigurálása, kattintson a **Create** az új prémium szintű gyorsítótár létrehozása redis adatmegőrzési.

## <a name="enable-redis-persistence"></a>Redis perzisztenciájának engedélyezése

A Redis perzisztencia engedélyezve van az **Adatmegőrzés panelen** az **RDB** vagy **az AOF** perzisztencia kiválasztásával. Az új gyorsítótárak esetében ez a panel a gyorsítótár-létrehozási folyamat során érhető el, az előző szakaszban leírtak szerint. A meglévő gyorsítótárak esetében az **adatmegőrzési** panel a gyorsítótár **Erőforrás menüjéből** érhető el.

![Redis-beállítások][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RdB adatmegőrzés konfigurálása

Az RDB adatmegőrzés engedélyezéséhez kattintson az **RDB**gombra. Ha korábban engedélyezett prémium szintű gyorsítótárban le szeretné tiltani az RDB-tartalommegőrzést, kattintson a **Letiltva**gombra.

![Redis RDB perzisztencia][redis-cache-rdb-persistence]

A biztonsági mentési időköz beállításához válasszon **egy biztonsági mentési gyakoriságot** a legördülő listából. A lehetőségek között szerepel **a 15 perc**, **a 30 perc**, a **60 perc**, a 6 **óra**, **a 12 óra**és a **24 óra**. Ez az időköz az előző biztonsági mentési művelet sikeres befejezése után és az új biztonsági mentés megkezdésekor kezdi meg a visszaszámlálást.

Kattintson **a Tárfiók** elemre a használni kívánt tárfiók kiválasztásához, és válassza az **elsődleges kulcsot** vagy a **Másodlagos kulcsot** a **tárolókulcs** legördülő menüből. A gyorsítótárral azonos régióban kell választania egy tárfiókot, és a **prémium szintű storage-fiók** ajánlott, mert a prémium szintű storage nagyobb átviteli kapacitással rendelkezik. 

> [!IMPORTANT]
> Ha az adatmegőrzési fiók tárolási kulcsa újragenerálódik, újra kell konfigurálnia a kívánt kulcsot a **tárolókulcs** legördülő menüből.
> 
> 

Kattintson **az OK** gombra az adatmegőrzési konfiguráció mentéséhez.

A következő biztonsági mentés (vagy az új gyorsítótárak első biztonsági mentése) a biztonsági mentés gyakorisági időközének eltelte után kezdődik.

## <a name="configure-aof-persistence"></a>AOF adatmegőrzés konfigurálása

Az AOF adatmegőrzés engedélyezéséhez kattintson **az AOF gombra.** Ha korábban engedélyezett prémium szintű gyorsítótárban le szeretné tiltani az AOF adatmegőrzést, kattintson a **Letiltva**gombra.

![Redis AOF perzisztencia][redis-cache-aof-persistence]

Az AOF adatmegőrzés konfigurálásához adjon meg egy **első tárfiókot.** Ez a tárfiók kell ugyanabban a régióban, mint a gyorsítótár, és a **prémium szintű storage-fiók** ajánlott, mert a prémium szintű storage nagyobb átviteli. Tetszés szerint konfigurálhat egy **további tárfiókot,** a Second Storage Account nevet. Ha egy második tárfiók van konfigurálva, a replika-gyorsítótárba írt írások erre a második tárfiókba kerülnek. Minden konfigurált tárfiókhoz válassza az **elsődleges kulcsot** vagy a **Másodlagos kulcsot** a storage **kulcs** legördülő menüből. 

> [!IMPORTANT]
> Ha az adatmegőrzési fiók tárolási kulcsa újragenerálódik, újra kell konfigurálnia a kívánt kulcsot a **tárolókulcs** legördülő menüből.
> 
> 

Ha az AOF adatmegőrzés engedélyezve van, a gyorsítótárba irányuló írási műveletek et a rendszer a kijelölt tárfiókba (vagy fiókokba menti, ha egy második tárfiókot konfigurált). Abban az esetben, ha egy katasztrofális hiba, amely leveszi az elsődleges és a replika-gyorsítótár, a tárolt AOF-napló a gyorsítótár újjáépítésére szolgál.

## <a name="persistence-faq"></a>Állandó megmaradás – gyakori kérdések
Az alábbi lista az Azure-gyorsítótár redis-i adatmegőrzéssel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Engedélyezhetem az adatmegőrzést egy korábban létrehozott gyorsítótárban?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Engedélyezhetem egyszerre az AOF és az RDB perzisztenciát?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Melyik perzisztencia modellt válasszam?](#which-persistence-model-should-i-choose)
* [Mi történik, ha más méretűre méretezve vagyok, és a méretezési művelet előtt készített biztonsági mentés ta-](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB perzisztencia
* [Módosíthatom az RDB biztonsági mentésgyakoriságát a gyorsítótár létrehozása után?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Miért, ha van egy RDB biztonsági mentési gyakorisága 60 perc van több mint 60 perc között biztonsági mentések?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Mi történik a régi RDB biztonsági mentésekkel, amikor új biztonsági mentés készül?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF perzisztencia
* [Mikor érdemes második tárfiókot használni?](#when-should-i-use-a-second-storage-account)
* [Az AOF-megőrzés hatással van a gyorsítótár teljes, késési vagy teljesítményére?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hogyan távolíthatom el a második tárfiókot?](#how-can-i-remove-the-second-storage-account)
* [Mi az átírás, és milyen hatással van a gyorsítótáramra?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Mire számíthatok, ha az AOF-val rendelkező gyorsítótárméretezés engedélyezve van?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hogyan vannak az AOF-adatok tárolása?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Engedélyezhetem az adatmegőrzést egy korábban létrehozott gyorsítótárban?
Igen, a Redis adatmegőrzéskonfigurálható mind a gyorsítótár létrehozása kor, mind a meglévő prémium szintű gyorsítótárakon.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Engedélyezhetem egyszerre az AOF és az RDB perzisztenciát?

Nem, csak rdb vagy AOF engedélyezhető, de mindkettő egyszerre nem.

### <a name="which-persistence-model-should-i-choose"></a>Melyik perzisztencia modellt válasszam?

Az AOF adatmegőrzés minden írást naplóba ment, amely hatással van az átviteli teljesítményre, összehasonlítva az RDB adatmegőrzési idővel, amely a beállított biztonsági mentési időköz alapján menti a biztonsági mentéseket, minimális hatással a teljesítményre. Válassza az AOF adatmegőrzést, ha az elsődleges cél az adatvesztés minimalizálása, és kezelni tudja a gyorsítótár átviteli értékének csökkenését. Válassza az RDB adatmegőrzési lehetőséget, ha meg szeretné tartani az optimális átviteli forgalmat a gyorsítótárban, de továbbra is szeretne egy mechanizmust az adatok helyreállításához.

* Tudjon meg többet az RDB perzisztenciájának [előnyeiről](https://redis.io/topics/persistence#rdb-advantages) és [hátrányairól.](https://redis.io/topics/persistence#rdb-disadvantages)
* Tudjon meg többet az AOF perzisztenciájának [előnyeiről](https://redis.io/topics/persistence#aof-advantages) és [hátrányairól.](https://redis.io/topics/persistence#aof-disadvantages)

Az AOF adatmegőrzés használata során a teljesítményről az [AOF adatmegőrzési hatásáról a gyorsítótár teljes, késési vagy teljesítményére hatással van?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Mi történik, ha más méretűre méretezve vagyok, és a méretezési művelet előtt készített biztonsági mentés ta-

Mind az RDB, mind az AOF perzisztenciája esetében:

* Ha nagyobb méretre méretezett, nincs hatása.
* Ha kisebb méretre méretezett, és olyan egyéni [adatbázis-beállítással](cache-configure.md#databases) rendelkezik, amely nagyobb, mint az új méret [adatbázisokra vonatkozó korlátja,](cache-configure.md#databases) az adatbázisokban lévő adatok nem lesznek visszaállítva. További információ: [Az egyéni adatbázisok beállítása érintett a méretezés során?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Ha kisebb méretre méretezett, és nincs elég hely a kisebb méretben az utolsó biztonsági mentés összes adatának tárolásához, a kulcsok kilesznek zárva a visszaállítási folyamat során, általában az [allkeys-lru](https://redis.io/topics/lru-cache) kilakoltatási házirend használatával.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Módosíthatom az RDB biztonsági mentésgyakoriságát a gyorsítótár létrehozása után?
Igen, módosíthatja az RDB adatmegőrzési panelen az RDB **adatmegőrzési** gyakoriságának biztonsági mentési gyakoriságát. További információt a Redis adatmegőrzés konfigurálása című témakörben talál.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Miért, ha van egy RDB biztonsági mentési gyakorisága 60 perc van több mint 60 perc között biztonsági mentések?
Az RDB adatmegőrzési biztonsági mentési gyakorisági időköze nem indul el, amíg az előző biztonsági mentési folyamat sikeresen le nem zárul. Ha a biztonsági mentés gyakorisága 60 perc, és a biztonsági mentési folyamat 15 percet vesz igénybe, a következő biztonsági mentés csak az előző biztonsági mentés kezdési időpontja után 75 perccel indul el.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Mi történik a régi RDB biztonsági mentésekkel, amikor új biztonsági mentés készül?
A legutóbbi kivételével az összes RDB adatmegőrzési biztonsági mentés automatikusan törlődik. Előfordulhat, hogy ez a törlés nem történik meg azonnal, de a régebbi biztonsági mentések nem maradnak meg határozatlan ideig.


### <a name="when-should-i-use-a-second-storage-account"></a>Mikor érdemes második tárfiókot használni?

Egy második tárfiókot kell használnia az AOF adatmegőrzéshez, ha úgy gondolja, hogy a vártnál magasabb készletműveletek vannak a gyorsítótárban.  A másodlagos tárfiók beállítása segít biztosítani, hogy a gyorsítótár ne érje el a tárhely sávszélesség-korlátait.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Az AOF-megőrzés hatással van a gyorsítótár teljes, késési vagy teljesítményére?

Az AOF adatmegőrzés körülbelül 15– 20%-kal befolyásolja az átviteli értéket, ha a gyorsítótár a maximális terhelés alatt van (a CPU és a kiszolgáló terhelése 90% alatt egyaránt). Nem lehet késési problémák, ha a gyorsítótár ezeken a korlátokon belül van. Azonban a gyorsítótár hamarabb eléri ezeket a korlátokat, ha az AOF engedélyezve van.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hogyan távolíthatom el a második tárfiókot?

Eltávolíthatja az AOF megőrzési másodlagos tárfiókot a második tárfiók beállításával, hogy megegyezik az első tárfiók. További információt az [AOF adatmegőrzés konfigurálása című](#configure-aof-persistence)témakörben talál.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Mi az átírás, és milyen hatással van a gyorsítótáramra?

Amikor az AOF fájl elég nagy lesz, az újraírás automatikusan várólistára kerül a gyorsítótárban. Az újraírás átméretezi az AOF-fájlt az aktuális adatkészlet létrehozásához szükséges minimális műveletkészlettel. Az újraírások során várhatóan hamarabb éri el a teljesítménykorlátokat, különösen nagy adatkészletek kezelése esetén. Az újraírások ritkábban fordulnak elő, amikor az AOF fájl nagyobb lesz, de jelentős időt vesz igénybe, amikor ez megtörténik.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Mire számíthatok, ha az AOF-val rendelkező gyorsítótárméretezés engedélyezve van?

Ha az AOF-fájl a méretezés időpontjában jelentősen nagy, akkor a méretezési művelet várhatóan a vártnál tovább tart, mivel a méretezés befejezése után újratölti a fájlt.

A méretezésről további információt a [Mi történik, ha más méretűre méretezve van, és a méretezési művelet előtt készített biztonsági mentés ta-](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hogyan vannak az AOF-adatok tárolása?

Az AOF-fájlokban tárolt adatok csomópontonként több lapblobra vannak osztva, hogy növeljék az adatok tárolóba mentésének teljesítményét. Az alábbi táblázat azt mutatja be, hogy hány lapblob ot használ az egyes tarifacsomagokhoz:

| Premium szintű csomag | Blobok |
|--------------|-------|
| P1           | 4 per shard    |
| P2           | 8 per shard    |
| P3           | 16 per shard   |
| P4           | 20 per shard   |

Ha a fürtözés engedélyezve van, a gyorsítótárban lévő minden egyes szegmens saját lapblobkészlettel rendelkezik, ahogy az az előző táblázatban is szerepel. Például egy P2-gyorsítótár három szilánkok osztja el a AOF-fájl között 24 lapblobok (8 blobok szegmensenként, 3 szilánkok) között.

Az újraírás után két AOF-fájlkészlet található a tárolóban. Az újraírások a háttérben történnek, és hozzáfűzik a fájlok első készletét, míg a gyorsítótárba küldött műveletek et a második készletújraírási hozzáfűzése során a gyorsítótárba helyezi. A biztonsági mentés átmenetileg tárolásra kerül az újraírások során hiba esetén, de az újraírás befejezése után azonnal törlődik.


## <a name="next-steps"></a>További lépések
További információ a további prémium szintű gyorsítótár-funkciók használatáról.

* [Bevezetés az Azure Cache for Redis premium szintű rétegbe](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
