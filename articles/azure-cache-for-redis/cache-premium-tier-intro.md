---
title: Bevezetés az Azure Cache for Redis premium szintű rétegbe
description: Megtudhatja, hogyan hozhat létre és kezelhet Redis Persistence, Redis fürtözés és VNET-támogatás a prémium szintű Azure-gyorsítótár redis-példányokhoz
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74121670"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Bevezetés az Azure Cache for Redis premium szintű rétegbe
Az Azure Cache for Redis egy elosztott, felügyelt gyorsítótár, amely segít a jól méretezhető és válaszkész alkalmazások létrehozásában azáltal, hogy szupergyors hozzáférést biztosít az adatokhoz. 

Az új prémium szintű csomag egy nagyvállalati használatra kész szint, amely tartalmazza az összes standard szintű funkciót és egyebeket, például a jobb teljesítményt, a nagyobb számítási feladatokat, a vészhelyreállítást, az importálást/exportálást és a fokozott biztonságot. Folytatódik olvasó, hogy többet tudjon meg a prémium szintű gyorsítótár-réteg további funkcióiról.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Jobb teljesítmény a standard vagy alapszintű szinthez képest
**Jobb teljesítmény standard vagy alapszintű szinten.** A prémium szintű gyorsítótárak olyan hardveren vannak telepítve, amely gyorsabb processzorokkal rendelkezik, és jobb teljesítményt nyújt az alap- vagy standard szinthez képest. A prémium szintű gyorsítótárak nagyobb átviteli és alacsonyabb késési. 

**Az azonos méretű gyorsítótár átviteli igénye magasabb a prémium szintű, mint a standard szintű.** Egy 53 GB-os P4 (Premium) gyorsítótár átviteli igénye például 250 Ezer kérelem másodpercenként, szemben a C6 (Standard) 150 Ezer-es értékével.

A méretről, az átviteli fokról és a prémium szintű gyorsítótárakkal kapcsolatos sávszélességről az [Azure Cache for Redis – gyakori kérdések című témakörben](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) talál további információt.

## <a name="redis-data-persistence"></a>Redis-adatmegőrzés
A prémium szint lehetővé teszi, hogy egy Azure Storage-fiókban őrizzék meg a gyorsítótár-adatokat. Alapszintű/szabványos gyorsítótárban az összes adat csak a memóriában tárolódik. Az alapul szolgáló infrastrukturális problémák esetén potenciális adatvesztés léphet fel. Azt javasoljuk, hogy a Redis adatmegőrzési funkció a prémium szinten, hogy növelje az adatvesztés sel szembeni rugalmasság növelése. Az Azure Cache for Redis RDB és AOF (hamarosan) lehetőségeket kínál a [Redis persistence-ben.](https://redis.io/topics/persistence) 

Az adatmegőrzés konfigurálásához a [Perzisztencia szolgáltatás konfigurálása a Redis prémium szintű Azure-gyorsítótárának adatmegőrzési konfigurálása című témakörben talál.](cache-how-to-premium-persistence.md)

## <a name="redis-cluster"></a>Redis-fürt
Ha 53 GB-nál nagyobb gyorsítótárakat szeretne létrehozni, vagy több Redis-csomóponton szeretné az adatokat szilánkolni, használhatja a Redis-fürtözést, amely elérhető a prémium szinten. Minden csomópont egy elsődleges/replika gyorsítótár-pár azure által kezelt magas rendelkezésre állás érdekében. 

**A Redis fürtözés maximális léptéket és átviteli értéket biztosít.** Átviteli szint lineárisan növekszik, ahogy növeli a szegmensek (csomópontok) a fürtben. Például Ha 10 szegmensből álló P4-fürtöt hoz létre, akkor a rendelkezésre álló átviteli érték 250K *10 = 2,5 millió kérelem másodpercenként. Kérjük, olvassa el az [Azure Cache for Redis GYIK](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) további részleteket a méret, átviteli és sávszélesség prémium szintű gyorsítótárak.

A fürtözés megkezdéséhez olvassa [el a fürtözés konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis hez című témakört.](cache-how-to-premium-clustering.md)

## <a name="enhanced-security-and-isolation"></a>Fokozott biztonság és elkülönítés
Az alapszintű vagy standard szinten létrehozott gyorsítótárak a nyilvános interneten érhetők el. A gyorsítótárhoz való hozzáférés a hozzáférési kulcs alapján korlátozott. A prémium szint del biztosíthatja továbbá, hogy csak egy adott hálózaton belüli ügyfelek férhetnek hozzá a gyorsítótárhoz. Az Azure Cache for Redis egy [Azure virtuális hálózatban (VNet)](https://azure.microsoft.com/services/virtual-network/)telepítheti. Használhatja a VNet összes funkcióját, köztük az alhálózatokat, a hozzáférés-vezérlési házirendeket, és a Redishez való hozzáférést korlátozó többi egyéb funkciókat.

További információ: [A virtuális hálózat támogatáskonfigurálása a Prémium szintű Azure-gyorsítótár redishez című témakörben.](cache-how-to-premium-vnet.md)

## <a name="importexport"></a>Import/Export
Az importálás/exportálás egy Azure-gyorsítótár a Redis-adatkezelési művelethez, amely lehetővé teszi az adatok importálását az Azure Cache for Redis számára, vagy adatok exportálása az Azure Cache for Redis-ből egy Azure Cache for Redis Database (RDB) pillanatkép importálásával és exportálásával egy prémium szintű gyorsítótárból egy Azure Storage-fiókban lévő lapblobba. Ez lehetővé teszi, hogy a Redis-példányok különböző Azure-gyorsítótárai között migráljon, vagy használat előtt adatokkal feltöltse a gyorsítótárat.

Az importálás segítségével redis kompatibilis RDB fájl(ok) bármely Redis szerver fut bármilyen felhőben vagy környezetben, beleértve a Redis futó Linux, Windows, vagy bármely felhő szolgáltató, mint az Amazon Web Services és mások. Az adatok importálásával egyszerűen hozhat létre gyorsítótárat előre kitöltött adatokkal. Az importálási folyamat során az Azure Cache for Redis betölti az RDB-fájlokat az Azure storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi, hogy exportálja a tárolt adatokat az Azure Cache for Redis a Redis kompatibilis RDB fájl(ok). Ezzel a funkcióval áthelyezheti az adatokat az egyik Azure-gyorsítótárból a Redis-példányegy másik vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gép, amely az Azure Cache for Redis kiszolgálópéldány, és a fájl feltöltése a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

További információ: [Adatok importálása az Azure Cache for Redis-ből.](cache-how-to-import-export-data.md)

## <a name="reboot"></a>Újraindítás
A prémium szint lehetővé teszi, hogy újraindítegy vagy több csomópontot a gyorsítótár igény szerint. Ez lehetővé teszi, hogy tesztelje az alkalmazás rugalmasságát hiba esetén. A következő csomópontok újraindíthatók.

* A gyorsítótár fő csomópontja
* A gyorsítótár másodlagos csomópontja
* A gyorsítótár elsődleges és másodlagos csomópontjai egyaránt
* Ha egy prémium szintű gyorsítótár fürtözés, újraindíthatja az elsődleges, másodlagos vagy mindkét csomópont ot az egyes szegmensek a gyorsítótárban

További információ: [Újraindítás](cache-administration.md#reboot) és [Újraindítás – gyakori kérdések.](cache-administration.md#reboot-faq)

>[!NOTE]
>Újraindítás funkció most már engedélyezve van az összes Azure Cache redis rétegek.
>
>

## <a name="schedule-updates"></a>Frissítések ütemezése
Az ütemezett frissítések funkció lehetővé teszi, hogy kijelölje a karbantartási időszak a gyorsítótár. Ha a karbantartási időszak meg van adva, a Redis-kiszolgáló frissítései ebben az időszakban történnek. Karbantartási időszak kijelöléséhez válassza ki a kívánt napokat, és adja meg a karbantartási időszak kezdési óráját az egyes napokhoz. Vegye figyelembe, hogy a karbantartási időszak ideje UTC.Note that the maintenance window time is in UTC. 

További információt a [Frissítések ütemezése](cache-administration.md#schedule-updates) és [a Gyakori kérdések ütemezése című témakörben talál.](cache-administration.md#schedule-updates-faq)

> [!NOTE]
> Csak a Redis-kiszolgáló frissítései lesznek az ütemezett karbantartási időszakban. A karbantartási időszak nem vonatkozik az Azure-frissítésekre vagy a virtuális gép operációs rendszer frissítéseire.
> 
> 

## <a name="geo-replication"></a>Georeplikáció

**A georeplikáció** két prémium szintű Azure-gyorsítótár redis-példányok összekapcsolására szolgáló mechanizmust biztosít. Az egyik gyorsítótár elsődleges csatolt gyorsítótárként, a másik pedig másodlagos csatolt gyorsítótárként van megjelölve. A másodlagos csatolt gyorsítótár írásvédetté válik, és az elsődleges gyorsítótárba írt adatok replikálódnak a másodlagos csatolt gyorsítótárba. Ez a funkció a gyorsítótár Replikálására használható az Azure-régiók között.

További információ: [A georeplikáció konfigurálása az Azure Cache for Redis számára](cache-how-to-geo-replication.md)című témakörben talál.


## <a name="to-scale-to-the-premium-tier"></a>A prémium szintre való méretezéshez
A prémium szintre való skálázáshoz egyszerűen válassza ki a díjcsomag egyikét a **Díjszabási szint módosítása** panelen. A gyorsítótárat a prémium szintre is skálázhatja a PowerShell és a CLI használatával. A részletes útmutatásról az [Azure-gyorsítótár méretezése a Redis-hez](cache-how-to-scale.md) című témakörben és [a skálázási műveletek automatizálása című témakörben talál.](cache-how-to-scale.md#how-to-automate-a-scaling-operation)

## <a name="next-steps"></a>További lépések
Hozzon létre egy gyorsítótárat, és fedezze fel az új prémium szintű funkciókat.

* [Az adatmegőrzés konfigurálása a Redis prémium szintű Azure-gyorsítótárához](cache-how-to-premium-persistence.md)
* [A virtuális hálózat támogatásának konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis számára](cache-how-to-premium-vnet.md)
* [A fürtözés konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis számára](cache-how-to-premium-clustering.md)
* [Adatok importálása az Azure Cache for Redis számára az Azure Cache-ből](cache-how-to-import-export-data.md)
* [Az Azure Cache for Redis felügyelete](cache-administration.md)

