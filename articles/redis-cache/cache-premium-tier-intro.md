---
title: "Az Azure Redis Cache prémium szintjének bemutatása |} Microsoft Docs"
description: "Útmutató: létrehozása és kezelése a Redis-adatmegőrzés, Redis a fürtszolgáltatás, és a Premium szint Azure Redis Cache példány hálózatok támogatása"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: c7a70e74f8b275ed9e10118b0ae9e81309f97ba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Az Azure Redis Cache Prémium szintjének bemutatása
Azure Redis Cache egy elosztott, felügyelt gyorsítótár, amely segít az adatok felső szinten gyors elérhetőségének biztosításával jól méretezhető és rugalmas alkalmazásait. 

Az új prémium szintű egy vállalati készen áll a réteget, amely tartalmazza a Standard szintű funkciók és további, például jobb teljesítmény, a nagyobb munkaterhelésekhez, a vész-helyreállítási, a importálási/exportálási és a fokozott biztonsági. További információt a további szolgáltatások a Premium gyorsítótár réteg olvasási továbbra is.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Standard vagy a Basic szint képest jobb teljesítmény
**Jobb teljesítmény Standard vagy alapszintű rétegben.** A prémium szinten lévő gyorsítótárak telepítve vannak a hardver, amely gyorsabb processzorok és képest jobb teljesítményt biztosít az alapszintű vagy Standard csomagra. Prémium szintű réteghez gyorsítótárainak rendelkezik nagyobb átviteli teljesítményt és kisebb késések fordulnak elő. 

**Az azonos méretű gyorsítótár átviteli értéke magasabb a prémium szintű képest Standard csomagra.** Például az átviteli sebessége a 53 GB P4 (prémium) gyorsítótár 250 KB-os kérelmek / másodperc, mint 150 K az C6 (normál).

Méret, átviteli sebesség és a prémium szintű gyorsítótárak sávszélesség kapcsolatos további információkért lásd: [Azure Redis Cache – gyakori kérdések](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-adatmegőrzés
A prémium csomagban lehetővé teszi a gyorsítótárazott adatokat az Azure Storage-fiók megőrizni. A Basic vagy Standard-gyorsítótár összes adatot csak memóriában van tárolva. Alapul szolgáló infrastruktúra esetén nincs problémák lehetséges adatvesztést is lehet. Azt javasoljuk, hogy a prémium csomagban a Redis-adatok megőrzését szolgáltatás használatával növelje az adatvesztéssel szembeni hibatűrést. Azure Redis Cache Rekordadatbázis és AOF (hamarosan elérhető) lehetőséget kínál a [Redis-adatmegőrzés](http://redis.io/topics/persistence). 

Útmutató az adatmegőrzés konfigurálásához: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-persistence.md) (Adatmegőrzés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).

## <a name="redis-cluster"></a>Redis-fürt
Ha szeretné 53 GB-nál nagyobb méretű gyorsítótárak hozhatók létre, vagy szeretné részekre bonthatók az adatok több Redis-csomópont között, fürtözés, a támogatási réteg elérhető Redis is használhatja. Minden csomópont elsődleges vagy replika gyorsítótár párból áll a magas rendelkezésre állású Azure kezeli. 

**A redis-fürtszolgáltatás lehetővé teszi maximális méretezés és teljesítmény.** Átviteli sebesség lineárisan növeli, mivel a szilánkok (csomópontok) a fürt számának növeléséhez. EG. Ha a 10 szilánkok P4 fürt létrehozásakor, akkor a rendelkezésre álló átviteli 250 KB-os * 10 = 2,5 millió kérések száma másodpercenként. Tekintse meg a [Azure Redis Cache – gyakori kérdések](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) méretét, az átviteli sebesség és a sávszélesség a prémium szintű gyorsítótárak kapcsolatos további részletekért.

Első lépésként fürtözési, lásd: [konfigurálása prémium szintű Azure Redis Cache Fürtszolgáltatás](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Nagyobb biztonság és elszigeteltség
A Basic vagy Standard szint létrehozott gyorsítótárak érhetők el a nyilvános interneten. A gyorsítótárban való hozzáférés korlátozva alapján a hozzáférési kulcsot. A prémium tarifacsomagra további biztosítható, hogy csak a megadott hálózatban lévő ügyfelek hozzáférhet-e a gyorsítótárban. Telepítheti a Redis Cache-ben egy [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). Használhatja a VNet összes funkcióját, köztük az alhálózatokat, a hozzáférés-vezérlési házirendeket, és a Redishez való hozzáférést korlátozó többi egyéb funkciókat.

További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-vnet.md) (Virtuális hálózat támogatásának konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).

## <a name="importexport"></a>Import/Export
Importálási/exportálási egy Azure Redis Cache-adatok felügyeleti művelet, amely lehetővé teszi, hogy adatokat importáljon belőlük az Azure Redis Cache vagy exportál adatokat az Azure Redis Cache importálása és exportálása Redis gyorsítótár-adatbázis (Rekordadatbázis) pillanatkép prémium gyorsítótárában az Azure oldalakra vonatkozó blob Storage-fiók. Ez lehetővé teszi az Azure Redis Cache példány közötti áttelepítéséhez, vagy a gyorsítótár adatokkal használat előtt.

Importálás segítségével bármely Redis-kiszolgáló futtatja a felhő vagy a környezet, beleértve a futó Linux, Windows vagy bármely felhőalapú szolgáltató, például az Amazon Web Services, míg mások Redis vinnie a Redis kompatibilis Rekordadatbázis (oka) t. Adatok importálása egyszerű módja a gyorsítótár létrehozásához előre megadott adatokkal. Az importálási folyamat során az Azure Redis Cache Rekordadatbázis fájlokat az Azure storage betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi az Azure Redis Cache Redis kompatibilis Rekordadatbázis (oka) t a tárolt adatok exportálását. Ez a szolgáltatás segítségével tárolt adatok mozgatása egy Azure Redis Cache példányt, vagy egy másik Redis-kiszolgálóhoz. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális Gépen, amelyen az Azure Redis Cache server-példányt, és a fájl feltöltése a kijelölt tárfiókkal. Az exportálási művelet befejezése után a vagy állapota sikeres végrehajtásával vagy hibajelzéssel az ideiglenes fájl törlődik.

További információkért lásd: [az adatimportálás és exportál adatokat az Azure Redis Cache](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Újraindítás
A prémium csomagban lehetővé teszi, hogy a gyorsítótár igény egy vagy több csomópontja újraindítását. Ez lehetővé teszi, hogy a meghibásodása rugalmasságot az alkalmazás teszteléséhez. Az alábbi csomópontok újraindíthatja.

* A gyorsítótár fő csomópont
* Az alárendelt csomópont a gyorsítótár
* A gyorsítótár fő és az alárendelt csomópont
* A prémium szintű gyorsítótár fürtözési használatakor újraindíthatja a master, az alárendelt vagy mindkét csomópont az egyes szilánkok a gyorsítótárban

További információkért lásd: [újraindítás](cache-administration.md#reboot) és [újraindítás GYIK](cache-administration.md#reboot-faq).

>[!NOTE]
>Újraindítás funkció engedélyezve van minden Azure Redis Cache-csomagban.
>
>

## <a name="schedule-updates"></a>Frissítések ütemezése
Az ütemezett frissítések szolgáltatás a gyorsítótárhoz karbantartási időszak megadását teszi lehetővé. A karbantartási időszak megadása esetén a Redis-kiszolgáló változásainak végrehajtott ezen időszak alatt történjen. Jelölje ki a karbantartási időszak jelölje ki a kívánt napok, majd adja meg a karbantartási ablak indítási óra minden nap. Vegye figyelembe, hogy a karbantartási ablak időpontja UTC szerint. 

További információkért lásd: [frissítések ütemezése](cache-administration.md#schedule-updates) és [frissítések ütemezése gyakran ismételt kérdések](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Csak Redis server frissítések válnak, az ütemezett karbantartási időszak alatt. A karbantartási időszak nem érvényes Azure-hírt vagy a virtuális gép operációs rendszert.
> 
> 

## <a name="geo-replication"></a>Georeplikáció

**A georeplikáció** lehetővé teszi a csatolás két Premium szint Azure Redis Cache példányt. Egy gyorsítótár az elsődleges társított gyorsítótár, míg a másik a másodlagos csatolt gyorsítótár van kijelölve. A másodlagos csatolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatokat a rendszer replikálja a másodlagos csatolt gyorsítótár. Ez a funkció a gyorsítótár Azure-régiók közötti replikáció használható.

További információkért lásd: [georeplikáció konfigurálása az Azure Redis Cache](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Prémium tarifacsomagra méretezése
Méretezési prémium tarifacsomagra, egyszerűen válasszon egyet a a prémium csomag szükséges az a **tarifacsomag módosítása** panelen. A gyorsítótár PowerShell és a parancssori felület használatával prémium tarifacsomagra is méretezheti. Részletes útmutatásért lásd: [Scale Azure Redis Cache hogyan](cache-how-to-scale.md) és [automatizálása a méretezési művelet](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Következő lépések
Gyorsítótár létrehozásához, és vizsgálja meg az új premium szint szolgáltatásokat.

* [Adatmegőrzés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-persistence.md)
* [Virtuális hálózat támogatásának konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-vnet.md)
* [Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-clustering.md)
* [Hogyan kell az adatok importálása és az adatok exportálása az Azure Redis Cache](cache-how-to-import-export-data.md)
* [Azure Redis Cache felügyelete](cache-administration.md)

