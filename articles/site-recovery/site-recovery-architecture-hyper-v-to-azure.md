---
title: "Hogyan működik a Hyper-V-replikáció az Azure-ba az Azure Site Recoveryben? | Microsoft Docs"
description: "Ez a cikk áttekintést nyújt a helyszíni Hyper-V virtuális gépeknek az Azure Site Recovery szolgáltatással az Azure-ba történő replikációjakor használt összetevőkről és architektúráról."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/23/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 28f775afaf72b11eec0c22f755e4dbd6a485c895
ms.contentlocale: hu-hu
ms.lasthandoff: 06/28/2017

---


# <a name="how-does-hyper-v-replication-to-azure-work-in-site-recovery"></a>Hogyan működik a Hyper-V-replikáció az Azure-ba a Site Recoveryben?


Ez a cikk leírja a helyszíni Hyper-V virtuális gépeknek az [Azure Site Recovery](site-recovery-overview.md) szolgáltatással az Azure-ba történő replikációjakor használt összetevőkről és folyamatokról.

A Site Recovery képes Hyper-V virtuális gépeket a System Center Virtual Machine Manager (VMM) szolgáltatással vagy anélkül felügyelt Hyper-V fürtökön vagy különálló gazdagépeken replikálni.

Megjegyzéseit a cikk alján, vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.



## <a name="architectural-components"></a>Az architektúra összetevői

Hyper-V virtuális gépek Azure-ba történő replikálásában számos összetevőnek része van.

**Összetevő** | **Hely** | **Részletek**
--- | --- | ---
**Azure** | Az Azure-ban szüksége van egy Microsoft Azure-fiókra, és Azure Storage-fiókra és egy Azure-hálózatra. | A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | A Hyper-V-gazdagépek VMM-felhőkben helyezkednek el | Ha a Hyper-V-gazdagépeket VMM felhőben felügyeli, a Recovery Services-tárolóban regisztrálja a VMM-kiszolgálót.<br/><br/> A VMM-kiszolgálón telepítse a Site Recovery Providert az Azure-ral való replikáció vezényléséhez.<br/><br/> A hálózatleképezés konfigurálásához logikai és virtuálisgép-hálózatokat is be kell állítania. Egy virtuálisgép-hálózatot össze kell kötnie egy felhőhöz társított logikai hálózattal.
**Hyper-V gazdagép** | A Hyper-V gazdagépek és fürtök VMM-kiszolgálóval vagy anélkül is üzembe helyezhetők. | Ha nincs VMM-kiszolgáló, a Site Recovery Providert a gazdagépen kell telepíteni, hogy vezényelni tudja az internetes replikációt a Site Recoveryval. Ha van VMM-kiszolgáló, a Provider azon van telepítve, és nem a gazdagépen.<br/><br/> A Recovery Services-ügynököt a gazdagépen kell telepíteni az adatreplikáció kezelése érdekében.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | Legalább egy virtuális gépnek futnia kell egy Hyper-V gazdakiszolgálón. | A virtuális gépekre semmit nem kell explicit módon telepíteni.

Az egyes összetevők telepítési előfeltételeiről és követelményeiről a [támogatási mátrix](site-recovery-support-matrix-to-azure.md) ad tájékoztatást.

**1. ábra: Replikálás Hyper-V-helyről az Azure-ba**

![Összetevők](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**2. ábra: Replikálás a VMM-felhőkben futó Hyper-V-ről az Azure-ba**

![Összetevők](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>Replikációs folyamat

**3. ábra: Replikáció és a helyreállítási folyamat Hyper-V Azure-ba történő replikációja esetén**

![munkafolyamat](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>Védelem engedélyezése

1. Miután engedélyezte a védelmet egy Hyper-V-alapú virtuális gép esetében az Azure Portalon vagy a helyszíni környezetben, elindul a **Védelem engedélyezése** feladat.
2. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, mielőtt meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően beállítja a replikációt.
3. A feladat elindítja a kezdeti replikációt a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódus meghívásával egy teljes körű virtuálisgép-replikáció elindítása céljából, majd a virtuális gépek virtuális lemezeit továbbítja az Azure-ba.
4. A feladatot a **Feladatok** lapon követheti nyomon.
        ![Feladatok listája](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Védelem engedélyezésének részletei](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>A kezdeti adatok replikálása

1. A kezdeti replikálás indításakor egy [pillanatfelvétel készül a Hyper-V-alapú virtuális gépről](https://technet.microsoft.com/library/dd560637.aspx).
2. A rendszer egyenként replikálja a virtuális merevlemezeket, amíg az összes át nem lesz másolva az Azure-ba. Ez a virtuális gép méretétől és a hálózat sávszélességétől függően eltarthat egy ideig. A hálózathasználat optimalizálásával kapcsolatban lásd [a helyszíni környezetből Azure-ba irányuló védelem hálózati sávszélesség-használatának kezelését](https://support.microsoft.com/kb/3056159) ismertető cikket.
3. Ha módosítják a lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V-replikációkövető nyomon követi a módosításokat replikálási naplók (.hrl) formájában. Ezek a fájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy .hrl-fájl, amelyet a rendszer továbbít a másodlagos tárterületre.
4. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
5. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


### <a name="finalize-protection"></a>Védelem véglegesítése

1. A kezdeti replikálás befejezése után a **Védelem véglegesítése a virtuális gépen** feladat konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez.
    ![Védelem véglegesítése feladat](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Azure-ba történő replikálás esetén előfordulhat, hogy módosítania kell a virtuális gép beállításait, hogy az készen álljon a feladatátvételre. Ezen a ponton érdemes lehet feladatátvételi tesztet futtatni, amellyel ellenőrizheti, hogy minden megfelelően működik-e.

### <a name="replicate-the-delta"></a>A változások replikálása

1. A kezdeti replikálást követően a replikációs beállításoknak megfelelően elindul a változások szinkronizálása.
2. A Hyper-V-replikációkövető a virtuális merevlemezek változásait .hrl fájlokban követi nyomon. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl. A rendszer a naplót a kezdeti replikáció befejezését követően küldi át az ügyfél tárfiókjába. A napló az Azure-ba való továbbítása alatt a rendszer az elsődleges lemez változásait egy másik naplófájlban követi nyomon ugyanabban a könyvtárban.
3. A kezdeti és a változásreplikáció során a virtuális gép figyelése a virtuálisgép-nézetben lehetséges. [További információk](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="synchronize-replication"></a>Replikáció szinkronizálása

1. Ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer a virtuális gépet megjelöli újraszinkronizálásra. Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra.
2.  Az újraszinkronizálás kiszámítja a forrás és a cél virtuális gépek ellenőrzőösszegeit, és ezek alapján csak a változtatott adatokat továbbítja, így segít csökkenti az adatmennyiséget. Az újraszinkronizálás egy rögzített blokkméretű csonkoló algoritmust alkalmaz, amelyben a forrás- és a célfájlok rögzített méretű adattömbökre vannak osztva. A rendszer kiszámítja az egyes adattömbök ellenőrző összegét, majd ezeket összevetve megállapítja, hogy mely blokkokat kell a forrásból átmásolni a célba.
3. Az újraszinkronizálás befejezését követően folytatódik a normál változásreplikálás. Alapértelmezés szerint a rendszer automatikusan munkaidőn kívüli időpontra ütemezi az újraszinkronizálást, de manuálisan is elvégezhető a virtuális gép újraszinkronizálása. Például manuálisan folytathatja az újraszinkronizálást, ha hálózatkimaradás vagy egyéb kimaradás következik be. Ehhez jelölje ki a virtuális gépet a portálon, majd válassza az **Újraszinkronizálás** elemet.

    ![Manuális újraszinkronizálás](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retry-logic"></a>Újrapróbálkozási logika

Ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. A vonatkozó logika két kategóriába sorolható:

**Kategória** | **Részletek**
--- | ---
**Helyreállíthatatlan hibák** | A rendszer nem kísérli meg a helyreállításukat. A virtuális gép állapota **Kritikusra** vált, és rendszergazdai beavatkozás szükséges. Ilyen hibák például a következők: sérült VHD-lánc; a virtuálisgép-replika érvénytelen állapota; hálózati hitelesítési hibák: engedélyezési hibák; „a virtuális gép nem található” hibák (önálló Hyper-V-kiszolgálók esetén).
**Helyreállítható hibák** | A rendszer minden replikálási időközben újrapróbálkozik exponenciális visszalépéssel, amely az újrapróbálkozás időközét az első kísérlet kezdetétől számított 1, 2, 4, 8 és 10 percre növeli. Ha a hiba nem szűnik meg, a rendszer 30 percenként újrapróbálkozik. Példák: hálózati hibák; nem elegendő lemezterületből fakadó hibák; alacsony memóriaállapot. |



## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett [feladatátvételt](site-recovery-failover.md) a helyszíni Hyper-V virtuális gépekről az Azure-ra. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
4. A feladatátvétel futtatása után a létrehozott replika virtuális gépeknek meg kell jelenniük az Azure-ban. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
5. Ezután véglegesíti a feladatátvételt, hogy hozzáférhessen a replika Azure virtuális gép számítási feladataihoz.
6. Amint az elsődleges helyszíni hely megint elérhetővé válik, [visszaadhatja](site-recovery-failback-from-azure-to-hyper-v.md) a feladatokat. Elindít egy tervezett feladatátvételt az Azure-ból az elsődleges helyre. Tervezett feladatátvétel esetében beállíthatja, hogy a feladat-visszavétel ugyanarra a virtuális gépre vagy egy másik helyre történjen, és szinkronizálhatja a módosításokat az Azure és a helyszíni hely között, így elkerülhető az adatvesztés. Ha a helyszínen létrejöttek a virtuális gépek, véglegesíti a feladatátvételt.




## <a name="next-steps"></a>Következő lépések

[Támogatási mátrix](site-recovery-support-matrix-to-azure.md) áttekintése

