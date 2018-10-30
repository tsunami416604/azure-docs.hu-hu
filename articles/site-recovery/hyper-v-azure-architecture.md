---
title: Hyper-V – Azure vészhelyreállítási helyreállítási architektúra az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk összetevőkről és architektúráról vész-helyreállítási a helyszíni Hyper-V virtuális gépek (VMM nélkül) az Azure-bA az Azure Site Recovery szolgáltatásban való üzembe helyezése során használt áttekintést nyújt.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: 23b12f5b0423f717e96ec1f59480f0175648c75f
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210652"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V – Azure vészhelyreállítási recovery architektúrájáról


Ez a cikk azt ismerteti, architektúrájának és folyamatainak replikálni, a feladatátvétel és helyreállítás a Hyper-V virtuális gépek (VM) a helyszíni Hyper-V-gazdagépek és az Azure között használt használatával a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Hyper-V-gazdagépek a System Center Virtual Machine Manager (VMM) magánfelhőkben igény szerint kezelhetők.



## <a name="architectural-components---hyper-v-without-vmm"></a>Az architektúra összetevői – a Hyper-V VMM nélkül

A következő táblázat és grafikus adja meg a Hyper-V-gazdagépek a VMM által nem felügyelt Hyper-V-replikáció az Azure-bA használt összetevők magas szintű nézetét.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, az Azure storage-fiók és Azure-hálózatra. | A storage-fiókban tárolt replikált adatokat a helyszíni virtuális gépek számítási feladataihoz. Azure virtuális gépek rendszer hoz létre a replikált munkaterhelés-adatok a helyszíni helyről feladatátvétel esetén.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Hyper-V** | Site Recovery üzembe helyezése során gyűjtse össze a Hyper-V-gazdagépek és fürtök Hyper-V helyek be. Az Azure Site Recovery Provider és Recovery Services-ügynök telepítése minden önálló Hyper-V gazdagépen, vagy a Hyper-V fürt minden csomópontján. | A Provider a Site Recoveryvel az interneten keresztül vezényli a replikációt. Az adatreplikációt a Recovery Services-ügynök kezeli.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | Egy vagy több futó virtuális gépek Hyper-V. | Semmit nem kell explicit módon kell telepíteni a virtuális gépeken.


**Hyper-V – Azure-architektúra (VMM nélkül)**

![Architektúra](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Az architektúra összetevői – a Hyper-V VMM-mel

A következő táblázat és grafikus adja meg a Hyper-V-gazdagépek a VMM-felhőkben felügyelt Hyper-V-replikáció az Azure-bA használt összetevők magas szintű nézetét.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, az Azure storage-fiók és Azure-hálózatra. | A storage-fiókban tárolt replikált adatokat a helyszíni virtuális gépek számítási feladataihoz. Az Azure virtuális gépek a replikált adatokkal jönnek létre a helyszíni helyről feladatátvétel esetén.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | A VMM-kiszolgáló egy vagy több, Hyper-V-gazdagépeket tartalmazó felhőt tartalmaz. | Telepítse a Site Recovery Providert a VMM-kiszolgálón, Site Recovery-vel való replikáció vezényléséhez, és regisztrálja a kiszolgálót a Recovery Services-tárolóba.
**Hyper-V gazdagép** | A VMM által felügyelt egy vagy több Hyper-V-gazdagép/-fürt. |  A Recovery Services-ügynököt minden Hyper-V gazdagép vagy fürt csomópontján telepítenie.
**Hyper-V virtuális gépek** | Hyper-V-gazdakiszolgálón futó egy vagy több virtuális gép. | A virtuális gépekre semmit nem kell explicit módon telepíteni.
**Hálózat** | A VMM-kiszolgálón beállított logikai- és virtuálisgép-hálózatok. A Virtuálisgép-hálózatot kösse össze egy logikai hálózatot, amely a felhőben van társítva. | Virtuálisgép-hálózatok Azure virtuális hálózatokra vannak leképezve. Azure virtuális gépek a feladatátvételt követően létrejönnek, amikor azok kerülnek az Azure-hálózatot, amely a Virtuálisgép-hálózat van leképezve.

**Hyper-V – Azure-architektúra (a VMM-mel)**

![Összetevők](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replikációs folyamat

![Hyper-V – Azure-bA](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikáció és a helyreállítási folyamat**


### <a name="enable-protection"></a>Védelem engedélyezése

1. Miután engedélyezte a védelmet egy Hyper-V-alapú virtuális gép esetében az Azure Portalon vagy a helyszíni környezetben, elindul a **Védelem engedélyezése** feladat.
2. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, mielőtt meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően beállítja a replikációt.
3. A feladat elindítja a kezdeti replikációt a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódus meghívásával egy teljes körű virtuálisgép-replikáció elindítása céljából, majd a virtuális gépek virtuális lemezeit továbbítja az Azure-ba.
4. A feladatot a **Feladatok** lapon követheti nyomon.      ![Feladatok listája](media/hyper-v-azure-architecture/image1.png) ![Védelem engedélyezésének részletei](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Kezdeti replikálás

1. Kezdeti replikáció akkor aktiválódik, amikor egy [a Hyper-V virtuális gép pillanatképét](https://technet.microsoft.com/library/dd560637.aspx) pillanatkép készítésének időpontjában.
2. Virtuális merevlemezek a virtuális gép replikált egy olyan, amíg átmásolja ezeket az Azure-bA. Előfordulhat, hogy ez eltarthat egy ideig a virtuális gép méretétől függően és a hálózati sávszélesség. [Ismerje meg, hogyan](https://support.microsoft.com/kb/3056159) növelheti a hálózati sávszélesség.
3. Ha lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V Replica Replication Tracker eszköz Hyper-V replikálási naplók (.hrl) formájában nyomon követi a módosításokat. Ezek a naplófájlok a lemezek ugyanabban a mappában találhatók. Minden lemezhez tartozik egy .hrl fájl, amelyet elküld a másodlagos tárhelyen. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


### <a name="finalize-protection-process"></a>Védelmi folyamata véglegesítése

1. A kezdeti replikáció befejezését követően a **védelem véglegesítése a virtuális gépen** feladat futtatása. Ez konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez.
2. Ebben a szakaszban ellenőrizheti a virtuális gép beállításait, győződjön meg arról, hogy készen áll a feladatátvételre. A vészhelyreállítási próba végrehajtása (teszt feladatátvétel) a virtuális géphez, ellenőrizze a sikertelen lesz, mint a várt módon futtathatja. 


## <a name="delta-replication"></a>Változásreplikáció

1. A kezdeti replikációt követően változásreplikálás kezdődik, a replikáció szabályzatának megfelelően.
2. A Hyper-V-Replikációkövető nyomon követi a .hrl fájlokban módosításokat az egyik virtuális merevlemez. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
3. A napló az ügyfél tárfiókja küld. Ha a napló az átvitel során, az Azure-ba, az elsődleges lemez változásait ugyanabban a mappában egy másik naplófájlban követi nyomon.
4. Kezdeti és a változásreplikáció során a virtuális gép az Azure Portalon követheti nyomon.

### <a name="resynchronization-process"></a>Az újraszinkronizálás folyamatban

1. Ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer a virtuális gépet megjelöli újraszinkronizálásra.
    - Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra.
    -  Alapértelmezés szerint az újraszinkronizálás munkaidőn kívüli automatikus futásra van ütemezve.
1.  Az újraszinkronizálás csak a módosított adatokat küld.
    - Minimalizálja a forrás és cél virtuális gépek ellenőrzőösszegeit által küldött adatok mennyisége.
    - Egy rögzített blokkméretű csonkoló algoritmust szalagokhoz, ahol forrás és a célfájlok rögzített méretű adattömbökre vannak osztva.
    - Az egyes adattömbök ellenőrzőösszegek akkor jönnek létre. Ezek összehasonlítja határozza meg, amivel blokkolja a forrásból a cél a alkalmazni kell.
2. Az újraszinkronizálás befejezését követően folytatódik a normál változásreplikálás.
3. Ha nem szeretne alapértelmezett újraszinkronizálás kívül várja, manuálisan is újraszinkronizálhatja egy virtuális Gépet. Ha például kimaradás esetén. Ehhez az Azure Portalon, válassza ki a virtuális gép > **újraszinkronizálása**.

    ![Manuális újraszinkronizálás](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Ismételje meg a folyamat

Ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Ismételje meg a táblában leírtak szerint van besorolva.

**Kategória** | **Részletek**
--- | ---
**Helyreállíthatatlan hibák** | A rendszer nem kísérli meg a helyreállításukat. A virtuális gép állapota **Kritikusra** vált, és rendszergazdai beavatkozás szükséges.<br/><br/> Ezek a hibák közé tartoznak a sérült VHD-lánc, neplatném stavu Pro a replika virtuális gép, hálózati hitelesítési hibák, a hitelesítési hibák, és a virtuális gép nem található hibák (önálló Hyper-V-kiszolgálók esetén.
**Helyreállítható hibák** | A rendszer minden replikálási időközben újrapróbálkozik exponenciális visszalépéssel, amely az újrapróbálkozás időközét az első kísérlet kezdetétől számított 1, 2, 4, 8 és 10 percre növeli. Ha a hiba nem szűnik meg, a rendszer 30 percenként újrapróbálkozik. Ilyen például hálózati hibák, elegendő lemezterületből fakadó hibák és alacsony memóriaállapot.



## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. A helyszíni Hyper-V virtuális gépek tervezett vagy nem tervezett feladatátvétel futtathatja az Azure-bA. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. Nem tervezett feladatátvétel, ha az elsődleges hely nem érhető el.
2. Egyetlen gép feladatátvételét, vagy több gép összehangolt feladatátadását helyreállítási terveket hozhat létre.
3. Feladatátvétel futtatása. Az első szakasz a feladatátvétel befejezése után megtekintheti a létrehozott replika virtuális gépek az Azure-ban kell lennie. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
4. Ekkor véglegesíti a feladatátvételt, a munkaterhelés elérése a replika Azure virtuális gép elindításához.

Miután a helyszíni infrastruktúra újra működik, visszaadhatja a. Feladat-visszavétel három lépésben történik:

1. Elindít egy tervezett feladatátvételt az Azure-ból a helyszíni helyre:
    - **Minimalizálják az állásidőt**: Ha ezt a beállítást használja a Site Recovery szinkronizálja az adatokat a feladatátvétel előtt. Azt ellenőrzi, hogy megváltozott az adatblokkokat, és letölti azokat a helyszíni helyre, miközben az Azure virtuális gép megőrzi fut, minimálisra csökkentik az állásidőt. Manuálisan adja meg, hogy a feladatátvételt kell elvégezni, amikor az Azure virtuális gép leállt, bármely végső változásokat másolja, és a feladatátvétel indítása.
    - **Teljes letöltés**: ezt a lehetőséget az adatok szinkronizálása a feladatátvétel során. Ez a beállítás a teljes lemez tölti le. Ez azért gyorsabban nincs ellenőrzőösszegek számítása, de több állásidőt. Használja ezt a beállítást, ha futtatja, a replika Azure virtuális gépek egy kis ideig, vagy ha a helyszíni virtuális gép törölve lett.
    - **Virtuális gép létrehozása**: hajtja végre, vissza ugyanazon a virtuális Gépen vagy egy másik virtuális géphez ki. Megadhatja, hogy a Site Recovery a virtuális Gépet kell létrehoznia, ha még nem létezik.

2. Miután a kezdeti szinkronizálás befejezését követően válassza ki a feladatátvétel végrehajtásához. Miután ez befejeződik, bejelentkezhetnek a helyszíni virtuális Gépre, és ellenőrizze, hogy minden a várt módon működik. Az Azure Portalon látható, hogy az Azure virtuális gépek lett leállítva.
3.  Ekkor véglegesíti a feladatátvételt, hogy hozzáférhessen a helyszíni virtuális gép újra számítási és a Befejezés.
4. Követően számítási feladatok biztonsági sikertelenek voltak, engedélyezheti a visszirányú replikálás, hogy a helyszíni virtuális gépek replikálása az Azure-bA újra.



## <a name="next-steps"></a>További lépések


Hajtsa végre a [ebben az oktatóanyagban](tutorial-prepare-azure.md) a Hyper-V használatának első lépései az Azure-bA.


