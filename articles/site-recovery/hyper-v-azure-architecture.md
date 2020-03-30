---
title: Hyper-V vész-helyreállítási architektúra az Azure Site Recovery-ben
description: Ez a cikk áttekintést nyújt a helyszíni hyper-v virtuális gépek (VMM nélküli) az Azure-ba az Azure Site Recovery szolgáltatással üzembe helyezések és architektúra üzembe helyezésekor használt összetevőkről és architektúráról.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 022d6edad1e907173dfde3481e60d2523be087a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082667"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Hyper-V-ről Azure-ba történő vészhelyreállítás architektúrája


Ez a cikk ismerteti az architektúra és a használt folyamatok, amikor replikálja, feladatátvételi és helyreállítása Hyper-V virtuális gépek (VM-ek) között a helyszíni Hyper-V gazdagépek és az Azure az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával.

A Hyper-V-állomások opcionálisan kezelhetők a System Center Virtual Machine Manager (VMM) saját felhőiben.



## <a name="architectural-components---hyper-v-without-vmm"></a>Építészeti alkatrészek - Hyper-V VMM nélkül

Az alábbi táblázat és a grafika magas szintű nézetet biztosít az Azure-ba történő Hyper-V replikációhoz használt összetevőkről, ha a Hyper-V gazdagépeket nem a VMM kezeli.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetés, az Azure storage-fiók és az Azure-hálózat. | A helyszíni virtuálisgép-számítási feladatok replikált adatait a tárfiók tárolja. Az Azure virtuális gépek jönnek létre a replikált számítási feladatok adatai, ha a helyszíni helyről feladatátvétel történik.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Hyper-V** | A Site Recovery telepítése során Hyper-V állomásokat és fürtöket gyűjthet hyper-V helyekre. Az Azure Site Recovery Provider and Recovery Services ügynököt minden önálló Hyper-V gazdagépen vagy minden Hyper-V fürtcsomóponton telepíti. | A Provider a Site Recoveryvel az interneten keresztül vezényli a replikációt. Az adatreplikációt a Recovery Services-ügynök kezeli.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | Egy vagy több, Hyper-V-n futó virtuális gép. | Semmit sem kell explicit módon telepíteni a virtuális gépekre.


**Hyper-V és Azure architektúra (VMM nélkül)**

![Architektúra](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Építészeti alkatrészek - Hyper-V VMM-mel

Az alábbi táblázat és a grafika magas szintű nézetet biztosít az Azure-ba történő Hyper-V replikációhoz használt összetevőkről, amikor a Hyper-V gazdagépek vmm-felhőkben vannak kezelve.

**Összetevő** | **Követelmény** | **Részletek**
--- | --- | ---
**Azure** | Egy Azure-előfizetés, az Azure storage-fiók és az Azure-hálózat. | A helyszíni virtuálisgép-számítási feladatok replikált adatait a tárfiók tárolja. Az Azure virtuális gépek jönnek létre a replikált adatokat, ha a helyszíni helyről feladatátvétel történik.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | A VMM-kiszolgáló egy vagy több, Hyper-V-gazdagépeket tartalmazó felhőt tartalmaz. | A Site Recovery Provider telepítése a VMM-kiszolgálón, a replikáció site recovery-vel való koordinálásához, és a kiszolgáló regisztrálásához a Helyreállítási szolgáltatások tárolójában.
**Hyper-V gazdagép** | A VMM által felügyelt egy vagy több Hyper-V-gazdagép/-fürt. |  A Helyreállítási szolgáltatások ügynökét minden Hyper-V állomásra vagy fürtcsomópontra telepíti.
**Hyper-V virtuális gépek** | Hyper-V-gazdakiszolgálón futó egy vagy több virtuális gép. | A virtuális gépekre semmit nem kell explicit módon telepíteni.
**Hálózat** | A VMM-kiszolgálón beállított logikai- és virtuálisgép-hálózatok. A virtuális gép hálózatát a felhőhöz társított logikai hálózathoz kell kapcsolni. | A virtuális gép-hálózatok az Azure virtuális hálózataihoz vannak rendelve. Ha az Azure virtuális gépek feladatátvétel után jönnek létre, hozzáadódnak a virtuális gép hálózatához leképezett Azure-hálózathoz.

**Hyper-V –Azure architektúra (VMM-mel)**

![Összetevők](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)



## <a name="replication-process"></a>Replikációs folyamat

![Hyper-V az Azure replikációjához](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikációs és helyreállítási folyamat**


### <a name="enable-protection"></a>Védelem engedélyezése

1. Miután engedélyezte a védelmet egy Hyper-V-alapú virtuális gép esetében az Azure Portalon vagy a helyszíni környezetben, elindul a **Védelem engedélyezése** feladat.
2. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, mielőtt meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően beállítja a replikációt.
3. A feladat elindítja a kezdeti replikációt a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódus meghívásával egy teljes körű virtuálisgép-replikáció elindítása céljából, majd a virtuális gépek virtuális lemezeit továbbítja az Azure-ba.
4. A feladat figyelheti a **Feladatok** lapon.      ![Feladatok](media/hyper-v-azure-architecture/image1.png) ![listája: Védelmi részletezés engedélyezése](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Kezdeti adatreplikáció

1. A kezdeti replikáció aktiválásakor egy [Hyper-V VM pillanatkép pillanatkép](https://technet.microsoft.com/library/dd560637.aspx) készül.
2. Virtuális merevlemezek a virtuális gépen replikálódik egyenként, amíg ők minden másolt az Azure-ba. Ez eltarthat egy ideig, a virtuális gép méretétől és a hálózati sávszélességtől függően. [További információ](https://support.microsoft.com/kb/3056159) a hálózati sávszélesség növeléséről.
3. Ha a lemez változások a kezdeti replikáció közben történnek, a Hyper-V replikációs követő hyper-V replikációs naplóként (.hrl) követi a módosításokat. Ezek a naplófájlok ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy társított .hrl fájl, amelyet a rendszer a másodlagos tárolóba küld. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét.
5. A rendszer a naplózott lemezmódosításokat szinkronizálja, és egyesíti a szülőlemezzel.


### <a name="finalize-protection-process"></a>Védelmi folyamat véglegesítése

1. A kezdeti replikáció befejezése után a **Véglegesítés védelem a virtuális gép** feladat fut. Konfigurálja a hálózati és egyéb replikáció utáni beállításokat, így a virtuális gép védett.
2. Ebben a szakaszban ellenőrizheti a virtuális gép beállításait, hogy megbizonyosodjon arról, hogy készen áll a feladatátvételre. Futtathat egy vész-helyreállítási gyakorlatot (teszt feladatátvétel) a virtuális gép, annak ellenőrzésére, hogy a feladatátvétel várt módon. 


## <a name="delta-replication"></a>Változásreplikáció

1. A kezdeti replikáció után megkezdődik a különbözeti replikáció a replikációs házirendnek megfelelően.
2. A Hyper-V replikációs követő .hrl fájlként követi nyomon a virtuális merevlemezre történő módosításokat. Minden replikációra konfigurált lemezhez tartozik egy .hrl fájl.
3. A napló az ügyfél tárfiókjába kerül. Amikor egy napló az Azure-ba kerül átvitelalatt, az elsődleges lemez módosításait egy másik naplófájlban, ugyanabban a mappában követi nyomon a rendszer.
4. A kezdeti és a különbözeti replikáció során figyelheti a virtuális gép az Azure Portalon.

### <a name="resynchronization-process"></a>Reszinkronizálási folyamat

1. Ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer a virtuális gépet megjelöli újraszinkronizálásra.
    - Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra.
    -  Alapértelmezés szerint az újraszinkronizálás a munkaidőn kívül automatikusan fog futni.
1.  Az újraszinkronizálás csak különbözeti adatokat küld.
    - Minimálisra csökkenti a forrás- és a célvirtuális gépek ellenőrzőösszegei által küldött adatok mennyiségét.
    - Ez használ egy rögzített blokk darabolási algoritmus, ahol a forrás és a cél fájlok vannak osztva rögzített darabokat.
    - Az egyes adattömbök ellenőrzőösszegei jönnek létre. Ezek összehasonlítása annak meghatározásához, hogy a forrás mely blokkokat kell alkalmazni a cél.
2. Az újraszinkronizálás befejezését követően folytatódik a normál változásreplikálás.
3. Ha nem szeretné megvárni az alapértelmezett újraszinkronizálást a munkaidőn kívül, manuálisan újraszinkronizálhatja a virtuális gépeket. Például, ha egy kimaradás történik. Ehhez az Azure Portalon válassza ki a virtuális gép > **újraszinkronizálása.**

    ![Manuális újraszinkronizálás](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Újrapróbálkozási folyamat

Ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Az újrapróbálkozás a táblázatban leírtak szerint van besorolva.

**Kategória** | **Részletek**
--- | ---
**Helyreállíthatatlan hibák** | A rendszer nem kísérli meg a helyreállításukat. A virtuális gép állapota **Kritikusra** vált, és rendszergazdai beavatkozás szükséges.<br/><br/> Ilyen hibák például egy hibás Virtuális merevlemez-lánc, a replika virtuális gép érvénytelen állapota, hálózati hitelesítési hibák, engedélyezési hibák és virtuális gép nem talált hibákat (önálló Hyper-V kiszolgálók esetén).
**Helyreállítható hibák** | A rendszer minden replikálási időközben újrapróbálkozik exponenciális visszalépéssel, amely az újrapróbálkozás időközét az első kísérlet kezdetétől számított 1, 2, 4, 8 és 10 percre növeli. Ha a hiba nem szűnik meg, a rendszer 30 percenként újrapróbálkozik. Ilyenek például a hálózati hibák, a kevés lemezhiba és a kevés memória.



## <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett feladatátvételt a helyszíni Hyper-V virtuális gépekről az Azure-ra. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. Futtasson nem tervezett feladatátvételt, ha az elsődleges hely nem érhető el.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó helyreállítási terveket is.
3. Feladatátvételt futtat. Miután a feladatátvétel első szakasza befejeződött, látnia kell a létrehozott replika virtuális gépek az Azure-ban. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
4. Ezután véglegesíti a feladatátvételt, hogy megkezdje a számítási feladatok elérését az Azure virtuális gép replikájáról.

Miután a helyszíni infrastruktúra újra működik, visszaléphet. A feladat-visszavétel három szakaszban történik:

1. Indítsa el a tervezett feladatátvételt az Azure-ból a helyszíni helyre:
    - **Állásidő minimalizálása**: Ha ezt a beállítást használja, a Site Recovery a feladatátvétel előtt szinkronizálja az adatokat. Ellenőrzi a módosított adatblokkokat, és letölti őket a helyszíni webhelyre, miközben az Azure virtuális gép folyamatosan fut, minimalizálva az állásidőt. Ha manuálisan adja meg, hogy a feladatátvétel befejeződnie kell, az Azure virtuális gép leáll, a végső különbözeti módosítások másolása, és a feladatátvétel elindul.
    - **Teljes letöltés**: Ezzel a beállítással az adatok szinkronizálása a feladatátvétel során történik. Ez a beállítás letölti a teljes lemezt. Ez gyorsabb, mert nincs ellenőrző összeg számítva, de van több állásidő. Akkor használja ezt a beállítást, ha már egy ideje futtatja az Azure-beli virtuális gépek replikáját, vagy ha a helyszíni virtuális gépet törölték.
    - **Virtuális gép létrehozása:** Kiválaszthatja, hogy adja vissza ugyanazt a virtuális gép, vagy egy másik virtuális gép. Megadhatja, hogy a Site Recovery létre kell hoznia a virtuális gép, ha még nem létezik.

2. A kezdeti szinkronizálás befejezése után a feladatátvétel befejezését választja. Miután befejeződött, bejelentkezhet a helyszíni virtuális gépre, hogy ellenőrizze, hogy minden a várt módon működik-e. Az Azure Portalon láthatja, hogy az Azure-beli virtuális gépek leálltak.
3.  Ezután véglegesíti a feladatátvétel befejezéséhez, és indítsa el a számítási feladat elérését a helyszíni virtuális gép újra.
4. Miután a számítási feladatok visszavétele után engedélyezi a fordított replikációt, hogy a helyszíni virtuális gépek ismét replikáljanak az Azure-ba.



## <a name="next-steps"></a>További lépések


Az [oktatóanyag](tutorial-prepare-azure.md) gal a Hyper-V Azure-replikációhoz való befejezéséhez kövesse.


