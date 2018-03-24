---
title: Hyper-V számára az Azure Site Recovery architektúrájáról Azure replikációs |} Microsoft Docs
description: Ez a cikk áttekintést nyújt a helyszíni Hyper-V (VMM nélküli) virtuális gépeknek az Azure Site Recovery szolgáltatással az Azure-ba történő replikációjakor használt összetevőkről és architektúráról.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/194/2018
ms.author: raynew
ms.openlocfilehash: 978d290287a4ff8875eea7e93f003c78e7177dae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="hyper-v-to-azure-replication-architecture"></a>Hyper-V replikáció Azure-architektúra


Ez a cikk ismerteti, architektúrájának és folyamatainak használható, ha a replikálása, feladatátvétele és helyreállítása a Hyper-V virtuális gépek (VM) között a helyszíni Hyper-V-gazdagépek és az Azure, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Hyper-V-gazdagépek a System Center Virtual Machine Manager (VMM) magánfelhőkben opcionálisan kezelhető.



## <a name="architectural-components---hyper-v-without-vmm"></a>Architektúra összetevői - a Hyper-V VMM nélkül

A következő tábla és a kép adja meg a Hyper-V-gazdagépek a VMM nem felügyeli a Hyper-V replikáció az Azure-ban használt összetevők áttekintése látható.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, Azure storage-fiók és Azure-hálózatot. | A tárfiók a helyszíni virtuális gép munkaterhelések replikált adatait tárolja. Azure virtuális gépek jönnek létre a replikált munkaterhelések adatokkal a helyszíni helyről feladatátvétel esetén.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Hyper-V** | Site Recovery üzembe helyezése során gyűjtse össze a Hyper-V gazdagépek és fürtök a Hyper-V helyekre. Az Azure Site Recovery Provider és Recovery Services agent telepítése minden önálló Hyper-V gazdagépen, vagy a Hyper-V fürt minden csomópontján. | A Provider a Site Recoveryvel az interneten keresztül vezényli a replikációt. Az adatreplikációt a Recovery Services-ügynök kezeli.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | Egy vagy több rendszert futtató virtuális gépek Hyper-v. | Nem kell explicit módon telepíthető virtuális gépek.


**Hyper-V Azure architektúra (VMM nélkül)**

![Architektúra](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)



## <a name="architectural-components---hyper-v-with-vmm"></a>Architektúra összetevői - a Hyper-V a VMM-mel

A következő tábla és a kép adja meg a VMM-felhőkben felügyelt Hyper-V-gazdagépeken a Hyper-V replikáció az Azure-ban használt összetevők áttekintése látható.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetéssel, Azure storage-fiók és Azure-hálózatot. | A tárfiók a helyszíni virtuális gép munkaterhelések replikált adatait tárolja. Azure virtuális gépek jönnek létre a replikált adatokat a helyszíni helyről feladatátvétel esetén.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | A VMM-kiszolgáló egy vagy több, Hyper-V-gazdagépeket tartalmazó felhőt tartalmaz. | A Site Recovery Provider telepítése a VMM-kiszolgálón, a Site Recovery replikációra, és regisztrálja a kiszolgálót a Recovery Services-tároló.
**Hyper-V gazdagép** | A VMM által felügyelt egy vagy több Hyper-V-gazdagép/-fürt. |  A Recovery Services Agent ügynök telepítése minden egyes Hyper-V gazdagép vagy fürt csomóponton.
**Hyper-V virtuális gépek** | Hyper-V-gazdakiszolgálón futó egy vagy több virtuális gép. | A virtuális gépekre semmit nem kell explicit módon telepíteni.
**Hálózat** | A VMM-kiszolgálón beállított logikai- és virtuálisgép-hálózatok. A Virtuálisgép-hálózatot kösse össze a felhőhöz társított logikai hálózatot. | A Virtuálisgép-hálózatok az Azure virtuális hálózatok vannak leképezve. Ha a feladatátvételt követően létrehozott Azure virtuális gépeken, az Azure-hálózatot a Virtuálisgép-hálózathoz csatlakoztatott történő hozzáadásuk.

**Hyper-V Azure architektúra (a VMM-mel)**

![Összetevők](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replikációs folyamat

![Hyper-V Azure replikáció](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikáció és a helyreállítási folyamat**


### <a name="enable-protection"></a>Védelem engedélyezése

1. Miután engedélyezte a védelmet egy Hyper-V-alapú virtuális gép esetében az Azure Portalon vagy a helyszíni környezetben, elindul a **Védelem engedélyezése** feladat.
2. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, mielőtt meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően beállítja a replikációt.
3. A feladat elindítja a kezdeti replikációt a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódus meghívásával egy teljes körű virtuálisgép-replikáció elindítása céljából, majd a virtuális gépek virtuális lemezeit továbbítja az Azure-ba.
4. A feladatot a **Feladatok** lapon követheti nyomon.      ![Feladatok listája](media/hyper-v-azure-architecture/image1.png)![Védelem engedélyezésének részletei](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Kezdeti adatreplikálás

1. Kezdeti replikálás kiváltásakor a [Hyper-V virtuális gép pillanatkép](https://technet.microsoft.com/library/dd560637.aspx) pillanatkép készítésének időpontjában.
2. A virtuális Gépen lévő virtuális merevlemezekhez replikált egy csak most az összes másolja őket az Azure-bA. Előfordulhat, hogy ez eltarthat egy ideig, attól függően, hogy a Virtuálisgép-méretet, és a hálózati sávszélesség. [Megtudhatja, hogyan](https://support.microsoft.com/kb/3056159) növelésére sávszélességet.
3. Ha a lemezen változások történnek, amíg a kezdeti replikáció folyamatban van, a Hyper-V Replica Replication Tracker módosításokat Hyper-V replikálási naplók (.hrl) követi nyomon. Ezekben a naplófájlokban a lemezek ugyanabban a mappában találhatók. Minden lemezhez tartozik egy .hrl-fájl, a másodlagos tárterületre küldött. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


### <a name="finalize-protection-process"></a>Védelmi folyamata véglegesítése

1. A kezdeti replikáció befejezését követően, a **védelem véglegesítése a virtuális gépen** feladat futtatása. Ez beállítja a hálózati és replikációt követő egyéb beállításokat, hogy a virtuális gép védett.
2. Ebben a szakaszban ellenőrizheti a virtuális gép beállításait, győződjön meg arról, hogy készen áll a feladatátvételre. A vész helyreállítási részletezéshez (feladatátvételi teszt) a virtuális géphez, ellenőrizze, hogy az nem várt módon több mint is futtathatja. 


## <a name="delta-replication"></a>Változásreplikáció

1. A kezdeti replikálás után az különbözeti replikációt a replikációs házirend megfelelően elindul.
2. A Hyper-V Replica Replication Tracker állapotváltozásait virtuális merevlemez .hrl-fájlok formájában. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
3. A napló nem jut hozzá a felhasználói tárfiók. Amikor a napló az átvitel során, az Azure-ba, az elsődleges lemez változásait követi újabb naplófájlt, ugyanabban a mappában.
4. Kiindulási és különbözeti replikálás során a virtuális Gépet az Azure-portálon a figyelheti.

### <a name="resynchronization-process"></a>Az újraszinkronizálás folyamatban

1. Ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer a virtuális gépet megjelöli újraszinkronizálásra.
    - Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra.
    -  Alapértelmezés szerint az újraszinkronizálás ütemezett automatikusan munkaidőn kívül.
1.  Az újraszinkronizálás csak különbözeti adatokat küld.
    - Minimalizálja a forrás és cél virtuális gépek ellenőrzőösszegeit által küldött adatok mennyisége.
    - Ahol a forrás-és cél rögzített méretű adattömböket oszthatók rögzített blokk tömbösítési algoritmust azt használja.
    - Az egyes adattömbök ellenőrzőösszegeket jönnek létre. Ezek összehasonlítja a cél alkalmazandó meghatározni, mely blokkok a forráskiszolgálóról.
2. Az újraszinkronizálás befejezését követően folytatódik a normál változásreplikálás.
3. Ha nem szeretné megvárni az újraszinkronizálás alapértelmezett a munkaidőn kívül, akkor manuálisan is újraszinkronizálhatja egy virtuális Gépet. Például nem tervezett kimaradás esetén. Ehhez az Azure portálon, válassza ki a virtuális gép > **újraszinkronizálása**.

    ![Manuális újraszinkronizálás](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Ismételje meg a folyamat

Ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Újrapróbálkozási besorolásának a táblázatban ismertetett módon.

**Kategória** | **Részletek**
--- | ---
**Helyreállíthatatlan hibák** | A rendszer nem kísérli meg a helyreállításukat. A virtuális gép állapota **Kritikusra** vált, és rendszergazdai beavatkozás szükséges.<br/><br/> Ezek a hibák például egy hibás VHD lánc, a replika virtuális Gépre, a hálózati hitelesítési hibák, a hitelesítési hibák állapota érvénytelen, és a virtuális gép nem található hibák (az önálló Hyper-V kiszolgálók.
**Helyreállítható hibák** | A rendszer minden replikálási időközben újrapróbálkozik exponenciális visszalépéssel, amely az újrapróbálkozás időközét az első kísérlet kezdetétől számított 1, 2, 4, 8 és 10 percre növeli. Ha a hiba nem szűnik meg, a rendszer 30 percenként újrapróbálkozik. Ezeket például hálózati hibák, kevés a szabad hibákat, és kevés a memória.



## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathatja a tervezett vagy nem tervezett feladatátvétel a helyszíni Hyper-V virtuális gépek Azure-bA. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. Futtassa a nem tervezett feladatátvételt, ha az elsődleges hely nem érhető el.
2. Egyetlen gép feladatátvételt, vagy hozzon létre helyreállítási tervek, több gép feladatainak átvétele számít.
3. Feladatátvételt. Az első fázist, a feladatátvétel után megtekintheti a létrehozott replika virtuális gépek Azure-ban kell lennie. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
4. Majd véglegesítse a feladatátvételt, az alkalmazások és szolgáltatások eléréséhez a replika Azure virtuális gép elindításához.

Miután a helyszíni infrastruktúra újra működik, akkor is feladat-visszavételt. Feladat-visszavétel három lépésben történik:

1. A tervezett feladatátvételt az Azure-ból a helyszíni hely indítsa:
    - **Állásidő minimalizálása érdekében**: Ha ezt a beállítást használja a Site Recovery szinkronizálja az adatokat a feladatátvétel előtt. Megváltozott az adatblokkokat keres, és letölti azokat a helyszíni hely, miközben az Azure virtuális gép tartja fut, minimalizálja az állásidőt. Ha manuálisan ad meg, hogy a feladatátvétel elvégzését, az Azure virtuális gép le van állítva, a végső változáskülönbözeteit kerülnek és elindul a feladatátvétel.
    - **Teljes letöltésére**: Ezzel a beállítással adatok szinkronizálása során. Ezt a lehetőséget a teljes lemez tölti le. Akkor gyorsabb, mert nincs ellenőrzőösszeg számítása, de nincs további állásidő. Használja ezt a beállítást, ha Ön már futott a replika Azure virtuális gépek egy kis ideig, vagy ha a helyszíni virtuális gép törölve lett.
    - **Hozzon létre virtuális Gépet**: sikertelen vissza az azonos virtuális gép vagy egy másik virtuális gépre is kiválaszthatja. Megadhatja, hogy a Site Recovery készítsen a virtuális gép, ha még nem létezik.

2. Kezdeti szinkronizálás befejezése után végezze el a feladatátvételt válassza ki. Miután befejeződött, ellenőrizze, hogy minden a várt módon működik a helyszíni virtuális gép bejelentkezhet. Az Azure-portálon tekintheti meg, hogy az Azure virtuális gépek lett leállítva.
3.  Ezt követően véglegesítést befejezéséhez, és a munkaterhelés újból megnyitni a helyszíni virtuális gép elindítása a feladatátvételt.
4. Miután munkaterhelések ismét sikertelen, a fordított replikáció engedélyeznie, hogy a helyszíni virtuális gépek replikálása az Azure-bA újra.



## <a name="next-steps"></a>További lépések


Hajtsa végre a [ebben az oktatóanyagban](tutorial-prepare-azure.md) Hyper-V Azure replikáció megkezdéséhez.


