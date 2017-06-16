---
title: "Hogyan működik a Hyper-V-replikáció az Azure-ba a Site Recoveryben? | Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan működik a Hyper-V-replikáció az Azure Site Recoveryben"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-architecture-hyper-v-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.contentlocale: hu-hu
ms.lasthandoff: 04/18/2017

---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Hogyan működik a Hyper-V-replikáció az Azure-ba?

Ebből a cikkből megismerheti az architektúrát és az Azure-ba történő Hyper-V-replikáció munkafolyamatát az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával.

Megjegyzéseit a cikk alján, vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.

A következőket replikálhatja az Azure-ba:
- **Hyper-V VMM-mel**: System Center Virtual Machine Manager-felhőkben (VMM-felhőkben) felügyelt helyszíni Hyper-V-gazdagépeken található virtuális gépek. A gazdagépek bármilyen [támogatott operációs rendszert](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) futtathatnak. A [Hyper-V és az Azure által támogatott](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bármilyen vendég operációs rendszert futtató Hyper-V-alapú virtuális gépet replikálhat.
- **Hyper-V VMM nélkül**: VMM-felhőkben nem felügyelt Hyper-V-gazdagépeken található helyszíni virtuális gépek. A gazdagépek a [támogatott operációs rendszerek](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) bármelyikét futtathatják. A [Hyper-V és az Azure által támogatott](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bármilyen vendég operációs rendszert futtató Hyper-V-alapú virtuális gépet replikálhat.


## <a name="architectural-components"></a>Az architektúra összetevői

**Terület** | **Összetevő** | **Részletek**
--- | --- | ---
**Azure** | Az Azure-ban szüksége van egy Microsoft Azure-fiókra, és Azure Storage-fiókra és egy Azure-hálózatra. | A Storage és a hálózat lehet Resource Manager-alapú vagy klasszikus fiók.<br/><br/> A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | VMM-felhőkben lévő Hyper-V-gazdagépek | Ha a Hyper-V-gazdagépeket VMM felhőben felügyeli, a Recovery Services-tárolóban regisztrálja a VMM-kiszolgálót.<br/><br/> A VMM-kiszolgálón telepítse a Site Recovery Providert az Azure-ral való replikáció vezényléséhez.<br/><br/> A hálózatleképezés konfigurálásához logikai és virtuálisgép-hálózatokat is be kell állítania. Egy virtuálisgép-hálózatot össze kell kötnie egy felhőhöz társított logikai hálózattal.
**Hyper-V gazdagép** | A Hyper-V-kiszolgálók VMM-kiszolgálóval vagy anélkül is üzembe helyezhetők. | Ha nincs VMM-kiszolgáló, a Site Recovery Providert a gazdagépen kell telepíteni, hogy vezényelni tudja az internetes replikációt a Site Recoveryval. Ha van VMM-kiszolgáló, a Provider azon van telepítve, és nem a gazdagépen.<br/><br/> A Recovery Services-ügynököt a gazdagépen kell telepíteni az adatreplikáció kezelése érdekében.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón legalább egy virtuális gépnek kell lennie. | A virtuális gépekre semmit nem kell explicit módon telepíteni

## <a name="deployment-steps"></a>A központi telepítés lépései

1. **Azure**: Állítsa be az Azure-összetevőket. Javasoljuk, hogy a Site Recovery üzembe helyezésének megkezdése előtt hozza létre a Storage- és hálózati fiókokat.
2. **Tároló**: Hozzon létre egy Recovery Services-tárolót a Site Recoveryhez, és konfigurálja a tároló beállításait, például konfigurálja a forrás- és célbeállításokat, állítson be egy replikációs szabályzatot és engedélyezze a replikációt.
3. **Forrás és cél**:
    - **Hyper-V gazdagépek-VMM-felhőkben**: A forrásbeállítások részeként töltse le és telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra, az Azure Recovery Services-ügynököt minden Hyper-V-gazdagépre. A forrás a VMM-kiszolgáló lesz. A cél az Azure.
    - Hyper-V-gazdagépek VMM nélkül: Amikor megadja a forrásbeállításokat, letölti és telepíti a Providert és az ügynököt minden egyes Hyper-V-gazdagépre. Az üzembe helyezés során egy Hyper-V-helyre gyűjti az összes gazdagépet, és ezt a helyet adja meg forrásként. A cél az Azure.

    ![Hyper-V/VMM replikálása az Azure-ba](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Hyper-V-hely replikálása az Azure-ba](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Replikációs házirend**: Létrehoz egy replikációs házirendet a Hyper-V-helyhez vagy a VMM-felhőhöz. A házirendet ezután a rendszer minden, a helyen vagy a felhőben lévő gazdagépen található virtuális gépre alkalmazza.
5. **Replikáció engedélyezése**: Engedélyezi a replikációt a Hyper-V-alapú virtuális gépek számára. A kezdeti replikálás a replikációs házirend beállításainak megfelelően történik. Az adatváltozásokat a rendszer nyomon követi, és az Azure változáskülönbözeteinek replikálása a kezdeti replikálás befejezése után kezdődik meg. Az elemek nyomon követett módosításait a rendszer .hrl fájlokban tárolja.
6. **Feladatátvétel tesztelése**: Egy tesztcélú feladatátvétel futtatásával ellenőrzi, hogy minden a vártnak megfelelően működik-e.

További információk az üzembe helyezésről:
- [Bevezetés a Hyper-V-alapú virtuális gépek az Azure-ba történő replikálásába – VMM-mel](site-recovery-vmm-to-azure.md)
- [Bevezetés a Hyper-V-alapú virtuális gépek az Azure-ba történő replikálásába – VMM nélkül](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>A Hyper-V replikálás munkafolyamata

### <a name="enable-protection"></a>Védelem engedélyezése

1. Miután engedélyezte a védelmet egy Hyper-V-alapú virtuális gép esetében az Azure Portalon vagy a helyszíni környezetben, elindul a **Védelem engedélyezése** feladat.
2. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, mielőtt meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően beállítja a replikációt.
3. A feladat elindítja a kezdeti replikációt a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódus meghívásával egy teljes körű virtuálisgép-replikáció elindítása céljából, majd a virtuális gépek virtuális lemezeit továbbítja az Azure-ba.
4. A feladatot a **Feladatok** lapon követheti nyomon.
        ![Feladatok listája](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Védelem engedélyezésének részletei](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Kezdeti replikálás

1. A kezdeti replikálás indításakor egy [pillanatfelvétel készül a Hyper-V-alapú virtuális gépről](https://technet.microsoft.com/library/dd560637.aspx).
2. A rendszer egyenként replikálja a virtuális merevlemezeket, amíg az összes át nem lesz másolva az Azure-ba. Ez a virtuális gép méretétől és a hálózat sávszélességétől függően eltarthat egy ideig. A hálózathasználat optimalizálásával kapcsolatban lásd [a helyszíni környezetből Azure-ba irányuló védelem hálózati sávszélesség-használatának kezelését](https://support.microsoft.com/kb/3056159) ismertető cikket.
3. Ha módosítják a lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V-replikációkövető nyomon követi a módosításokat replikálási naplók (.hrl) formájában. Ezek a fájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy .hrl-fájl, amelyet a rendszer továbbít a másodlagos tárterületre.
4. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
5. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


### <a name="finalize-protection"></a>Védelem véglegesítése

1. A kezdeti replikálás befejezése után a **Védelem véglegesítése a virtuális gépen** feladat konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez.
    ![Védelem véglegesítése feladat](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Azure-ba történő replikálás esetén előfordulhat, hogy módosítania kell a virtuális gép beállításait, hogy az készen álljon a feladatátvételre. Ezen a ponton érdemes lehet feladatátvételi tesztet futtatni, amellyel ellenőrizheti, hogy minden megfelelően működik-e.

### <a name="delta-replication"></a>Változásreplikáció

1. A kezdeti replikálást követően a replikációs beállításoknak megfelelően elindul a változások szinkronizálása.
2. A Hyper-V-replikációkövető a virtuális merevlemezek változásait .hrl fájlokban követi nyomon. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl. A rendszer a naplót a kezdeti replikáció befejezését követően küldi át az ügyfél tárfiókjába. A napló az Azure-ba való továbbítása alatt a rendszer az elsődleges lemez változásait egy másik naplófájlban követi nyomon ugyanabban a könyvtárban.
3. A kezdeti és a változásreplikáció során a virtuális gép figyelése a virtuálisgép-nézetben lehetséges. [További információk](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Replikációszinkronizálás

1. Ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer a virtuális gépet megjelöli újraszinkronizálásra. Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra.
2.  Az újraszinkronizálás kiszámítja a forrás és a cél virtuális gépek ellenőrzőösszegeit, és ezek alapján csak a változtatott adatokat továbbítja, így segít csökkenti az adatmennyiséget. Az újraszinkronizálás egy rögzített blokkméretű csonkoló algoritmust alkalmaz, amelyben a forrás- és a célfájlok rögzített méretű adattömbökre vannak osztva. A rendszer kiszámítja az egyes adattömbök ellenőrző összegét, majd ezeket összevetve megállapítja, hogy mely blokkokat kell a forrásból átmásolni a célba.
3. Az újraszinkronizálás befejezését követően folytatódik a normál változásreplikálás. Alapértelmezés szerint a rendszer automatikusan munkaidőn kívüli időpontra ütemezi az újraszinkronizálást, de manuálisan is elvégezhető a virtuális gép újraszinkronizálása. Például manuálisan folytathatja az újraszinkronizálást, ha hálózatkimaradás vagy egyéb kimaradás következik be. Ehhez jelölje ki a virtuális gépet a portálon, majd válassza az **Újraszinkronizálás** elemet.

    ![Manuális újraszinkronizálás](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Újrapróbálkozások

Ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. A vonatkozó logika két kategóriába sorolható:

**Kategória** | **Részletek**
--- | ---
**Helyreállíthatatlan hibák** | A rendszer nem kísérli meg a helyreállításukat. A virtuális gép állapota **Kritikusra** vált, és rendszergazdai beavatkozás szükséges. Ilyen hibák például a következők: sérült VHD-lánc; a virtuálisgép-replika érvénytelen állapota; hálózati hitelesítési hibák: engedélyezési hibák; „a virtuális gép nem található” hibák (önálló Hyper-V-kiszolgálók esetén).
**Helyreállítható hibák** | A rendszer minden replikálási időközben újrapróbálkozik exponenciális visszalépéssel, amely az újrapróbálkozás időközét az első kísérlet kezdetétől számított 1, 2, 4, 8 és 10 percre növeli. Ha a hiba nem szűnik meg, a rendszer 30 percenként újrapróbálkozik. Példák: hálózati hibák; nem elegendő lemezterületből fakadó hibák; alacsony memóriaállapot. |

## <a name="protection-and-recovery-lifecycle"></a>A védelem és helyreállítás életciklusa

![munkafolyamat](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezési előfeltételek ellenőrzése](site-recovery-prereq.md)
- Hibaelhárítás a következőkkel:
    - [Védelem figyelése és hibaelhárítása](site-recovery-monitoring-and-troubleshooting.md)
    - [A Microsoft ügyfélszolgálatának segítsége](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Gyakori hibák és megoldások](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

