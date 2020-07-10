---
title: A prémium szintű Redis készült Azure cache bemutatása
description: Ismerje meg, hogyan hozhatja létre és kezelheti a Redis-megőrzést, a Redis-fürtözést és a VNET támogatását a prémium szintű Azure cache-hez Redis-példányok
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 15b4764d32c536698246bddfcca50ffa6ce9b3b5
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184684"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>A prémium szintű Redis készült Azure cache bemutatása
Az Azure cache for Redis egy elosztott, felügyelt gyorsítótár, amellyel gyorsan méretezhető és rugalmas alkalmazások hozhatók létre az adataihoz való villámgyors hozzáférés biztosításával. 

Az új prémium szint egy nagyvállalati készültségi szint, amely magában foglalja a standard szintű funkciókat és egyebeket, például a jobb teljesítményt, nagyobb méretű számítási feladatokat, vész-helyreállítási, importálási/exportálási és fokozott biztonságot. Folytassa az olvasást, ha többet szeretne megtudni a prémium szintű gyorsítótári szint további funkcióiról.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Jobb teljesítmény a standard vagy az alapszinthez képest
**Jobb teljesítmény a standard vagy az alapszintnél.** A prémium szintű gyorsítótárak üzembe helyezése olyan hardvereken történik, amelyek gyorsabb processzorokkal rendelkeznek, és jobb teljesítményt biztosítanak az alapszintű vagy a standard szinthez képest. A prémium szintű gyorsítótárak nagyobb átviteli sebességgel és kisebb késéssel rendelkeznek. 

**Az azonos méretű gyorsítótárhoz tartozó átviteli sebesség a standard szinthez képest magasabb a prémium szinten.** A 53 GB-os (prémium szintű) gyorsítótár átviteli sebessége például 250K-kérelmek másodpercenkénti száma a C6-os (standard) 150K képest.

További információ a méretről, az átviteli sebességről és a sávszélességről a prémium szintű gyorsítótárak esetében: [Azure cache for REDIS GYIK](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-adatmegőrzés
A prémium szint lehetővé teszi az Azure Storage-fiókban tárolt gyorsítótár-adatmegőrzést. Az alapszintű vagy a standard szintű gyorsítótár csak a memóriában tárolja az összes adatát. A mögöttes infrastruktúra-problémák esetén lehetséges az adatvesztés. Javasoljuk, hogy a prémium szint Redis adatmegőrzési funkciójának használatával növelje a rugalmasságot az adatvesztéssel szemben. Az Azure cache for Redis RDB és AOF (hamarosan elérhető) lehetőségeket kínál a [Redis megőrzésében](https://redis.io/topics/persistence). 

Az adatmegőrzés konfigurálásával kapcsolatos útmutatásért lásd: az [adatmegőrzés konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-persistence.md)-hez.

## <a name="redis-cluster"></a>Redis-fürt
Ha 53 GB-nál nagyobb gyorsítótárat szeretne létrehozni, vagy több Redis-csomóponton szeretne adatszegmenst használni, használhatja a prémium szinten elérhető Redis-fürtözést. Az egyes csomópontok az Azure által kezelt elsődleges/replika gyorsítótár-párokat tartalmazzák a magas rendelkezésre állás érdekében. 

**A Redis fürtözés maximális skálázást és átviteli sebességet biztosít.** Az átviteli sebesség a fürtben lévő szegmensek (csomópontok) számának növelésével lineárisan növekszik. Ha például 10 szegmensből álló P4-fürtöt hoz létre, akkor a rendelkezésre álló átviteli sebesség 250K * 10 = 2 500 000 kérés/másodperc. A prémium szintű gyorsítótárral rendelkező mérettel, átviteli sebességgel és sávszélességgel kapcsolatos további részletekért tekintse meg az [Azure cache for Redis gyakori kérdések](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) című témakört.

A fürtözés megkezdéséhez tekintse meg a [fürtszolgáltatás konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-clustering.md)-hoz című témakört.

## <a name="enhanced-security-and-isolation"></a>Fokozott biztonság és elkülönítés
Az alapszintű vagy standard szinten létrehozott gyorsítótárak elérhetők a nyilvános interneten. A gyorsítótárhoz való hozzáférés korlátozott a hozzáférési kulcs alapján. A prémium szintű csomaggal ellenőrizheti, hogy csak a megadott hálózatban lévő ügyfelek férhetnek hozzá a gyorsítótárhoz. Az Azure cache-t üzembe helyezheti az [azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/)Redis. Használhatja a VNet összes funkcióját, köztük az alhálózatokat, a hozzáférés-vezérlési házirendeket, és a Redishez való hozzáférést korlátozó többi egyéb funkciókat.

További információkért lásd: [Virtual Network támogatásának konfigurálása prémium szintű Azure cache-hez a Redis](cache-how-to-premium-vnet.md)-hez.

## <a name="importexport"></a>Import/Export
Az import/export egy Azure cache for Redis adatkezelési művelet, amely lehetővé teszi az adatok importálását az Azure cache-be a Redis, vagy exportálja az Azure cache-ből a Redis-be adatok exportálását egy Azure cache for Redis Database (RDB) pillanatképnek a prémium szintű gyorsítótárból egy Azure Storage-fiókba való importálásával és exportálásával. Ez lehetővé teszi, hogy áttelepítse a különböző Azure cache-t a Redis-példányok között, vagy a használat előtt feltöltse a gyorsítótárat az adatokkal.

Az importálással bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról lehet Redis kompatibilis RDB-fájlt használni, beleértve a Linuxon, a Windowson vagy bármely más felhőalapú szolgáltatón, például a Amazon Web Serviceson vagy más felhőben futó Redis. Az adatok importálása egyszerű módszer a gyorsítótár előre feltöltött adatokkal való létrehozására. Az importálási folyamat során az Azure cache for Redis betölti a RDB-fájlokat az Azure Storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis-Redis kompatibilis RDB-fájl (ok) hoz. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

További információ: [adatok importálása és exportálása az Azure cache-ből a Redis-hez](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Újraindítás
A prémium szint lehetővé teszi, hogy igény szerint újraindítsa a gyorsítótár egy vagy több csomópontját. Ez lehetővé teszi, hogy meghibásodás esetén tesztelje a rugalmasságot az alkalmazásban. A következő csomópontok is újraindulnak.

* A gyorsítótár elsődleges csomópontja
* A gyorsítótár replika csomópontja
* A gyorsítótár elsődleges és replika csomópontjai
* Ha prémium szintű gyorsítótárat használ a fürtözéssel, akkor a gyorsítótárban lévő egyes szegmensek esetében újraindíthatja az elsődleges, a replikát vagy mindkét csomópontot.

További információ: [Újraindítás](cache-administration.md#reboot) és [újraindítással kapcsolatos gyakori kérdések](cache-administration.md#reboot-faq).

>[!NOTE]
>Az újraindítási funkció mostantól engedélyezve van az összes Azure cache-hez a Redis szintjein.
>
>

## <a name="schedule-updates"></a>Frissítések ütemezése
Az ütemezett frissítések funkció lehetővé teszi, hogy kijelölje a gyorsítótár karbantartási ablakát. Ha a karbantartási időszak meg van adva, a rendszer minden Redis-kiszolgáló frissítést végez ebben az ablakban. A karbantartási időszak kijelöléséhez válassza ki a kívánt napokat, és adja meg a karbantartási időszak kezdő óráját minden nap. A karbantartási időszak időpontja UTC. 

További információ: [frissítések ütemezett](cache-administration.md#schedule-updates) és [ütemezett frissítései – gyakori kérdések](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Az ütemezett karbantartási időszakban csak a Redis-kiszolgáló frissítései lesznek elérhetők. A karbantartási időszak nem vonatkozik az Azure frissítéseire vagy a virtuális gép operációs rendszerének frissítéseire.
> 
> 

## <a name="geo-replication"></a>Georeplikáció

A **geo-replikáció** lehetővé teszi két prémium szintű Azure cache összekapcsolását a Redis-példányokhoz. Az egyik gyorsítótár elsődleges csatolt gyorsítótárként van kijelölve, a másik pedig másodlagos csatolt gyorsítótárként. A másodlagos csatolt gyorsítótár írásvédett lesz, és az elsődleges gyorsítótárba írt információk replikálódnak a másodlagos csatolt gyorsítótárba. Ez a funkció a gyorsítótár Azure-régiók közötti replikálására használható.

További információ: a [geo-replikáció konfigurálása az Azure cache-hez a Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>A prémium szint méretezése
A prémium szintre való skálázáshoz válassza az egyik prémium szintet a **change árképzési szint** panelen. A gyorsítótárat a prémium szintre is méretezheti a PowerShell és a parancssori felület használatával. Részletes útmutatásért lásd: az [Azure cache skálázása Redis](cache-how-to-scale.md) és [a skálázási műveletek automatizálása](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>További lépések
Hozzon létre egy gyorsítótárat, és fedezze fel az új prémium szintű funkciókat.

* [Az adatmegőrzés konfigurálása prémium szintű Azure cache-Redis](cache-how-to-premium-persistence.md)
* [A prémium szintű Azure cache Virtual Network támogatásának konfigurálása a Redis-hez](cache-how-to-premium-vnet.md)
* [Fürtözés konfigurálása prémium szintű Azure cache-hez a Redis-hez](cache-how-to-premium-clustering.md)
* [Adatok importálása és exportálása az Azure cache-ből a Redis-be](cache-how-to-import-export-data.md)
* [Az Azure cache felügyelete a Redis-hez](cache-administration.md)

