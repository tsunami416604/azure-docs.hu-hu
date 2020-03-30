---
title: Hogyan működik a Hyper-V áttelepítése az Azure Áttelepítése?
description: További információ a Hyper-V áttelepítéséről az Azure Migrate szolgáltatással
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185881"
---
# <a name="how-does-hyper-v-replication-work"></a>Hogyan működik a Hyper-V replikáció?

Ez a cikk áttekintést nyújt az azure-áttelepítési kiszolgálóáttelepítési eszközzel a Hyper-V virtuális gépek áttelepítésekénél használt architektúráról és folyamatokról.

[Az Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és számítási feladatok, valamint a magán-/nyilvános felhőbeli virtuális gépek Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint külső független szoftverszállítói (ISV) ajánlatokat.

## <a name="agentless-migration"></a>Ügynök nélküli áttelepítés

Az Azure Áttelepítési kiszolgáló áttelepítése eszköz ügynök nélküli replikációt biztosít a helyszíni Hyper-V virtuális gépekhez, a Hyper-V-re optimalizált áttelepítési munkafolyamat használatával. Szoftverügynököt csak Hyper-V állomásokra vagy fürtcsomópontokra telepít. Semmit sem kell telepíteni a Hyper-V virtuális gépekre.

## <a name="server-migration-and-azure-site-recovery"></a>Kiszolgáló áttelepítése és az Azure site recovery

Az Azure Migrate Server Migration egy olyan eszköz, amely a helyszíni számítási feladatok és a felhőalapú virtuális gépek Azure-ba történő áttelepítéséhez. A Site Recovery egy vész-helyreállítási eszköz. Az eszközök közösen használnak néhány közös technológiai összetevőt az adatok replikációjához, de különböző célokat szolgálnak. 


## <a name="architectural-components"></a>Az architektúra összetevői

![Architektúra](./media/hyper-v-replication-architecture/architecture.png)



**Összetevő** | **Környezet** | 
--- | --- 
**Replikációs szolgáltató** | A Microsoft Azure Site Recovery szolgáltató telepítve van a Hyper-V gazdagépeken, és regisztrálva van az Azure Migration Server Migration.<br/> A szolgáltató vezényli a hyper-V virtuális gépek replikációját.
**Helyreállítási szolgáltatások ügynöke** | A Microsoft Azure Recovery Service ügynök kezeli az adatreplikációt. Együttműködik a szolgáltatóval a Hyper-V virtuális gépekről az Azure-ba történő adatok replikálása.<br/> A replikált adatok feltöltése egy tárfiókba az Azure-előfizetésben. A Kiszolgálóáttelepítés eszköz feldolgozza a replikált adatokat, és alkalmazza azokat az előfizetésben lévő replikalemezekre. A replikalemezek az Azure virtuális gépek áttelepítése kor létrehozásához használt.

- Az összetevőket egyetlen telepítőfájl telepíti, amely az Azure Áttelepítési kiszolgáló áttelepítése a portálon.
- A szolgáltató és a készülék kimenő HTTPS-port 443-as kapcsolatokat használ az Azure Migrate Server Migration szolgáltatással való kommunikációhoz.
- A szolgáltató tól és az ügynöktől érkező kommunikáció biztonságos és titkosított.


## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a replikációt egy Hyper-V virtuális gép, a kezdeti replikáció kezdődik.
2. A Hyper-V vm pillanatkép készül.
3. A virtuális gépeken lévő virtuális gépek egyenként replikálódnak, amíg az összes az Azure-ba nem kerül. A kezdeti replikációs idő a virtuális gép méretétől és a hálózati sávszélességtől függ.
4. A kezdeti replikáció során előforduló lemezmódosításokat a Hyper-V replika nyomon követi, és naplófájlokban (hrl fájlokban) tárolja.
    - A naplófájlok ugyanabban a mappában vannak, mint a lemezek.
    - Minden lemezhez tartozik egy társított hrl fájl, amely a másodlagos tárolóba kerül.
    - A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejezése után a virtuális gép pillanatképe törlődik, és a különbözeti replikáció megkezdődik.
5. A növekményes lemezmódosításokat a hrl fájlok követik nyomon. A replikációs naplókat a Recovery Services-ügynök rendszeresen feltölti egy Azure-tárfiókba.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

A Hyper-V replikációs teljesítményét olyan tényezők befolyásolják, mint a virtuális gépek mérete, a virtuális gépek adatváltozási sebessége (lemorzsolódása), a Hyper-V gazdagépen a naplófájlok tárolására rendelkezésre álló hely, a replikációs adatok feltöltési sávszélessége és az Azure-beli céltárolás.

- Ha egyszerre több gépet replikál, használja az [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) for Hyper-V-t, hogy optimalizálja a replikációt.
- Tervezze meg a Hyper-V replikációt, és ossza el a replikációt az Azure storage-fiókokon keresztül, a kapacitásnak megfelelően.

### <a name="control-upload-throughput"></a>A feltöltési átviteli rendszer szabályozása

Korlátozhatja az adatok azure-ba való feltöltéséhez használt sávszélességet az egyes Hyper-V-gazdagépeken. légy óvatos. Ha túl alacsonyra állítja az értékeket, az hátrányosan befolyásolja a replikációt, és késlelteti az áttelepítést.


1. Jelentkezzen be a Hyper-V állomásvagy fürtcsomópontba.
2. A Windows Azure Biztonsági másolat mmc beépülő moduljának megnyitásához futtassa a **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**parancsot.
3. A beépülő modulban válassza a **Tulajdonságok módosítása**lehetőséget.
4. A **Szabályozás területen**válassza az Internet **sávszélesség-használat szabályozásának engedélyezése a biztonsági mentési műveletekhez**lehetőséget. Állítsa be a munkaidő és a nem munkaidő korlátait. Az érvényes tartományok 512 Kb/s és 1023 Mb/s között mozognak.
I

### <a name="influence-upload-efficiency"></a>A feltöltés hatékonyságának befolyásolása

Ha van tartalék sávszélessége a replikációhoz, és növelni szeretné a feltöltéseket, az alábbiak szerint növelheti a feltöltési feladathoz lefoglalt szálak számát:

1. Nyissa meg a regedit beállításjegyzéket.
2. Navigálás a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM kulcsra
3. Növelje az egyes replikáló virtuális gépek adatfeltöltéshez használt szálak számát. Az alapértelmezett érték 4, a maximális érték pedig 32. 




## <a name="next-steps"></a>További lépések

Próbálja ki a [Hyper-V áttelepítést](tutorial-migrate-hyper-v.md) az Azure Áttelepítési kiszolgáló áttelepítésével.
