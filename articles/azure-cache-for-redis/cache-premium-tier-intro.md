---
title: Bevezetés az Azure Cache redis Cache prémium szint |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és kezelheti a Redis-adatmegőrzés, Redis, fürtözés, és a prémium szintű Azure Cache a Redis-példány a VNET-támogatás
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 906ab6c433df7cf452d1f5ab4660abce8d4674fc
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107514"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Bevezetés az Azure Cache redis Cache prémium szint
A Redis Azure Cache olyan elosztott, felügyelt gyorsítótár, amely segítséget nyújt az adatok a villámgyors adathozzáférésnek köszönhetően gyors és hatékonyan méretezhető alkalmazások készítését. 

Az új prémium szintű egy nagyvállalati igényekre szabott szint, amely tartalmazza a Standard csomag összes szolgáltatás és más, például jobb teljesítményt, nagyobb méretű számítási feladatokat, vészhelyreállítás, importálási/exportálási és fokozott biztonságot. További információ a prémium szintű gyorsítótárszint további funkcióit olvassa tovább.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Standard vagy alapszintű csomagra képest jobb teljesítményt
**Jobb teljesítmény alapszintű vagy Standard szintű.** Gyorsítótárak a prémium szintű vannak üzembe helyezve, amelyet gyorsabb processzorokkal rendelkeznek, és jobb teljesítményt biztosít az alapszintű vagy Standard szintű hardveren. Prémium szintű gyorsítótárak magasabb átviteli sebesség és a kisebb a késésük rendelkezik. 

**Az azonos méretű gyorsítótár átviteli sebesség nagyobb a prémium szintű szemben a standard szintű csomag.** Például az átviteli sebességet egy 53 GB-os P4 (prémium szintű) gyorsítótár 250e kérelmek / másodperc, mint a korábban megszokott 150 K az C6 csomag (Standard).

Méret, teljesítmény és a sávszélesség a prémium gyorsítótárak kapcsolatos további információkért lásd: [Azure Cache a redis Cache – gyakori kérdések](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis-adatmegőrzés
A prémium szintű csomag segítségével megőrizheti a gyorsítótárazza az adatokat egy Azure Storage-fiókot. Az alapszintű és Standard gyorsítótár csak a memóriában tárolt összes adatot. Alapul szolgáló infrastruktúra esetén nincs problémák lehetséges adatvesztéssel is lehet. Azt javasoljuk, hogy a Redis adatmegőrzési funkció használatát a prémium szintű az adatvesztéssel szembeni ellenálló-képesség növelésére. Az Azure Cache redis RDB-fájlba való és (hamarosan) AOF lehetőséget kínál a [Redis-adatmegőrzés](https://redis.io/topics/persistence). 

Útmutatás az adatmegőrzés konfigurálásához: [egy prémium szintű Azure Cache megőrzése a Redis konfigurálása](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Redis-fürt
Ha azt szeretné, 53 GB-nál nagyobb méretű gyorsítótárak hozhatók létre, vagy szeretné bonthatók az adatok több Redis-csomóponton, használhatja a Redis-fürtözés, azaz prémium tarifacsomagban érhető el. Minden egyes csomópont a magas rendelkezésre állás érdekében az Azure-ban felügyelt elsődleges/replika gyorsítótár pár áll. 

**A redis-fürtözés biztosít maximális méretezést és teljesítményt.** Átviteli sebesség költségráfordításokkal egyenes arányban növekszik, a fürtben (csomópontok) szegmensei számának növelésével. Eg. Ha 10 szegmens-P4 szintű fürtöt hoz létre, akkor az elérhető átviteli sebesség 250 ezer * 10 = 2,5 millió vonatkozó kérelmek másodpercenkénti száma. Tekintse át a [redis Cache – gyakori kérdések az Azure Cache](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) méret, teljesítmény és a sávszélesség a prémium gyorsítótárak kapcsolatos további részletekért.

Fürtszolgáltatás használatának megkezdéséhez lásd [egy prémium szintű Azure Cache redis fürtözés konfigurálása](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Nagyobb biztonság és elszigeteltség
A Basic vagy Standard szintű létrehozott gyorsítótárak elérhetők a nyilvános interneten. A gyorsítótárhoz való hozzáférést korlátozódik a hozzáférési kulcs alapján. A prémium szintű további biztosíthatja, hogy csak az ügyfelek egy adott hálózaton belül hozzáférhet-e a gyorsítótárban. A redis Azure Cache telepíthet egy [Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/). Használhatja a VNet összes funkcióját, köztük az alhálózatokat, a hozzáférés-vezérlési házirendeket, és a Redishez való hozzáférést korlátozó többi egyéb funkciókat.

További információkért lásd: [annak a virtuális hálózat támogatásának konfigurálása prémium szintű Azure Cache-gyorsítótárhoz Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Import/Export
Importálás/exportálás szolgáltatás egy Azure Cache, amely lehetővé teszi, hogy az adatok az Azure Cache által importálása és exportálása az Azure Cache Redis adatbázis (RDB) pillanatkép egy prémium szintű gyorsítótár, a redis Azure Cache redis adatok importálása és exportálása a Redis-felügyeleti művelethez egy lapblob egy Azure Storage-fiókban. Ez lehetővé teszi, hogy migrálása másik Azure Cache a Redis-példány között, vagy töltse fel a gyorsítótárat az adatokat, mielőtt használja.

Importálás ahhoz, hogy a Redis-kompatibilis RDB-fájl(ok) minden bármilyen felhőben vagy a környezetben, beleértve a Linux, Windows vagy bármely más szolgáltatónál, például az Amazon Web Services és a többi futó Redis futó Redis-kiszolgálóról is használható. A rendszer egyszerűen hozzon létre egy gyorsítótárat az adatokkal előre feltöltött adatok importálása. Az importálási folyamat során az Azure Cache redis RDB-fájlba való fájlokat az Azure storage-ból betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi, hogy a Redis a Redis-kompatibilis RDB-fájl(ok) for Azure Cache-ben tárolt adatok exportálása. Ez a funkció használatával adatok áthelyezése egy másik vagy egy másik Redis-kiszolgáló egy Azure Cache a Redis-példányt. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gépen, amelyen az Azure Cache Redis-server-példányhoz, és a fájlt a kijelölt tárfiók töltenek fel. Az exportálási művelet befejeződik, vagy sikeres állapotú vagy sikertelen, amikor az ideiglenes fájl törlődik.

További információkért lásd: [adatok importálása, és az adatok exportálása az Azure Cache redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Újraindítás
A prémium szint lehetővé teszi, hogy a gyorsítótár igény szerinti, egy vagy több csomópont újraindítását. Ez lehetővé teszi, hogy tesztelje az alkalmazást a rugalmassághoz meghibásodása. Az alábbi csomópontok indíthatja újra.

* Fő csomóponttal, a gyorsítótár
* Az alárendelt csomópont a gyorsítótár
* A gyorsítótár fő és az alárendelt csomópontok
* Egy prémium szintű gyorsítótár fürtözési használatakor akkor indíthatja újra a fő, az alárendelt vagy mindkét csomópontját az egyes szegmensek a gyorsítótárban

További információkért lásd: [újraindítás](cache-administration.md#reboot) és [újraindítás – gyakori kérdések](cache-administration.md#reboot-faq).

>[!NOTE]
>Újraindítás funkció engedélyezve van az összes Azure Cache Redis-szint esetében.
>
>

## <a name="schedule-updates"></a>Frissítések ütemezése
Az ütemezett frissítések szolgáltatás egy karbantartási időszakot a gyorsítótár megadását teszi lehetővé. Ha a karbantartási időszak van megadva, a Redis-kiszolgáló frissítéseit ezen időszak alatt készülnek el. Karbantartási időszak kijelölni, válassza ki a kívánt napok és adja meg a karbantartási ablak indítási órán keresztül minden nap. Vegye figyelembe, hogy a karbantartási ablak időpontja (UTC). 

További információkért lásd: [frissítések ütemezése](cache-administration.md#schedule-updates) és [frissítések ütemezése – Gyakori kérdések](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Csak a Redis-kiszolgáló frissítéseket az ütemezett karbantartási időszakban menjenek végbe. A karbantartási időszak nem vonatkozik az Azure vagy a virtuális gép operációsrendszer-frissítéseket.
> 
> 

## <a name="geo-replication"></a>Georeplikáció

**Georeplikáció** lehetővé teszi a két prémium szintű Azure Cache Redis-példány csatolása. Egy gyorsítótár az elsődleges társított gyorsítótár, a másik pedig a csatolt másodlagos gyorsítótáraként van kijelölve. A másodlagos összekapcsolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatok replikálódnak a másodlagos összekapcsolt gyorsítótár. Ez a funkció segítségével gyorsítótár replikálása az Azure-régiók között.

További információkért lásd: [georeplikáció konfigurálása az Azure Cache Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>A prémium szint méretezhető
Méretezhető, a prémium szintű, egyszerűen egyikén a prémium szinten a **tarifacsomag módosítása** panelen. A gyorsítótár, a prémium szintre, PowerShell és CLI használatával is méretezheti. Lépésenkénti útmutatásért lásd: [How to Scale Azure Cache redis](cache-how-to-scale.md) és [hogyan automatizálhatja a skálázási művelet](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>További lépések
Hozzon létre egy gyorsítótárat, és ismerje meg az új prémiumszintű funkciókkal.

* [Egy prémium szintű Azure Cache megőrzése a Redis konfigurálása](cache-how-to-premium-persistence.md)
* [Hogyan lehet virtuális hálózat támogatásának konfigurálása prémium szintű Azure Cache-gyorsítótárhoz Redis](cache-how-to-premium-vnet.md)
* [Egy prémium szintű Azure Cache redis fürtözés konfigurálása](cache-how-to-premium-clustering.md)
* [Hogyan lehet az adatok importálása és az adatok exportálása az Azure Cache redis](cache-how-to-import-export-data.md)
* [A Redis Azure Cache felügyelete](cache-administration.md)

