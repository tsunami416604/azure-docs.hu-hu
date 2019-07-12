---
title: Hogyan működik a Hyper-V áttelepítése az Azure Migrate-kiszolgáló áttelepítése? | Microsoft Docs
description: Az Azure Migrate-kiszolgáló áttelepítése a Hyper-V áttelepítés áttekintése
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811712"
---
# <a name="how-does-hyper-v-replication-work"></a>Hogyan működik a Hyper-V-replikáció?

Ez a cikk az architektúra és az Azure Migrate Server áttelepítési eszköz a Hyper-V virtuális gépek áttelepítésekor használt folyamatok áttekintést nyújt.

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és a számítási feladatok és a privát és nyilvános felhőbeli VM-eken, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.

## <a name="agentless-migration"></a>Ügynök nélküli migrálást

Az Azure Migrate Server áttelepítési eszköz a Hyper-V-hoz optimalizált migrálási munkafolyamat használatával a helyszíni Hyper-V virtuális gépek, ügynök nélküli kivételfigyelés replikációt biztosít. Csak a Hyper-V-gazdagépek vagy a fürt csomópontjai telepítenie kell egy szoftverfrissítési ügynököt. Semmit nem kell a Hyper-V virtuális gépeken telepíthető.

## <a name="server-migration-and-azure-site-recovery"></a>Az Azure Site Recovery és a kiszolgáló áttelepítése

Az Azure Migrate kiszolgáló áttelepítése egy olyan eszköz áttelepítése a helyszíni számítási feladatokat, és a felhő alapú virtuális gépek, az Azure-bA. Site Recovery szolgáltatás a vész-helyreállítási eszköze. Az eszközök néhány gyakori technológia összetevők adatreplikációt megosztani, de különböző célokat szolgálnak. 


## <a name="architectural-components"></a>Az architektúra összetevői

![Architektúra](./media/hyper-v-replication-architecture/architecture.png)



**Összetevő** | **Üzembe helyezés** | 
--- | --- 
**Replikációs szolgáltató** | A Microsoft Azure Site Recovery provider telepítve a Hyper-V-gazdagépek, és regisztrálja az Azure Migrálási kiszolgáló áttelepítése.<br/> A szolgáltató Hyper-V virtuális gépek replikálását koordinálja.
**Recovery Services-ügynök** | A Microsoft Azure Recovery Services ügynök kezeli az adatreplikációt. A szolgáltató Hyper-V virtuális gépekről az adatok replikálása az Azure-ba is működik.<br/> A replikált adatokat töltenek fel egy storage-fiókot az Azure-előfizetésében. A kiszolgáló-áttelepítési eszköz a folyamatok a replikált adatok, és alkalmazza azt az előfizetésben található replika lemezek. A replika lemezek segítségével hozzon létre az Azure virtuális gépek áttelepítésekor.

- Ezeket az összetevőket, a telepítő egyetlen fájl által letöltött Azure Migrate-kiszolgáló áttelepítése a portálon.
- A szolgáltató és a készülék kimenő HTTPS port 443-as kapcsolatok használatával kommunikálni az Azure Migrate-kiszolgáló áttelepítése.
- A provider és az ügynök kommunikációját olyan biztonságos, titkosított csatornákon történik.


## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a Hyper-V virtuális gép replikációját, megkezdődik a kezdeti replikálás.
2. A Hyper-V virtuális gép pillanatkép készítésének időpontjában.
3. A virtuális gép virtuális merevlemezei replikált-– egy olyan, amíg átmásolja ezeket az Azure-bA. Kezdeti replikáció ideje a virtuális gép méretét, és a hálózati sávszélesség függ.
4. Hyper-V replika segítségével, és a naplófájlok (hrl fájlokat) tárolja a kezdeti replikáció során felmerülő lemezen változások nyomon követése.
    - A naplófájlok ugyanabban a mappában található, mint a lemezek.
    - Minden lemezhez tartozik egy társított hrl fájlt a másodlagos tárterületre küldött.
    - A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. Kezdeti replikálás befejezése után a rendszer törli a virtuális gép pillanatképét, és változásreplikációhoz kezdődik.
5. Növekményes lemezmódosításokat hrl fájlok követi nyomon. Replikációs naplók rendszeres időközönként a Recovery Services-ügynök által feltöltött Azure storage-fiókba.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

A Hyper-V replikációs teljesítményt befolyásolja tényezők befolyásolják, beleértve az Azure-beli Virtuálisgép-méret, az adatok adatváltozási sebesség (forgalom), a virtuális gépek, a log file storage, a replikációs adatok feltöltési sávszélesség és a céloldali tárolóra a Hyper-V gazdagépen rendelkezésre álló területet.

- Ha egyszerre több gépet replikál, használja a [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) Hyper-v replikáció optimalizálásához.
- A Hyper-V replikációjának megtervezéséhez, és a replikációs elosztása az Azure storage-fiókok esetében kapacitás megfelelően.

### <a name="control-upload-throughput"></a>Vezérlő feltöltési sebessége

Adatok feltöltése az Azure-ban minden egyes Hyper-V gazdagépen használt sávszélesség is korlátozza. légy óvatos. Ha túl alacsony értékek negatívan befolyásolja replikáció és a késleltetés migrálása.


1. Jelentkezzen be a Hyper-V gazdagép vagy -fürt csomópontja.
2. Futtatás **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, a Windows Azure Backup szolgáltatás MMC beépülő modul megnyitásához.
3. A beépülő modulban, válassza ki a **tulajdonságainak módosítása**.
4. A **sávszélesség-szabályozási**válassza **szabályozása a biztonsági mentési műveletek internetes sávszélességének**. Állítsa be a munkaórákra és a munkaórákon kívüli. 512 KB/s a 1,023 Mbps érvényes tartományok származnak.
I

### <a name="influence-upload-efficiency"></a>Befolyásoló feltöltési hatékonyságát

Ha rendelkezik a replikáció tartalékolt sávszélesség, és szeretné növelni a feltöltések, növelheti a következőképpen a feltöltési feladathoz lefoglalt szálak száma:

1. Nyissa meg a beállításjegyzékben a Regedit.
2. Keresse meg a kulcs HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Növelje az adatfeltöltés replikáló virtuális gépek használt szálak számát. Az alapértelmezett értéke 4, maximális értéke pedig 32. 




## <a name="next-steps"></a>További lépések

Próbálja ki [Hyper-V áttelepítés](tutorial-migrate-hyper-v.md) Azure Migrate-áttelepítés használatával.
