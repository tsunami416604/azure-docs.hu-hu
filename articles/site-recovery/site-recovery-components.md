---
title: "Hogy működik a Site Recovery? | Microsoft Docs"
description: "Ebben a cikkben a Site Recovery architektúráját mutatjuk be."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 22b50dd6242e8c10241b0626b48f8ef842b6b0fd
ms.openlocfilehash: c33ca9e5292096a0fd96d98da3e89d721463e903
ms.lasthandoff: 03/02/2017


---
# <a name="how-does-azure-site-recovery-work"></a>Hogy működik az Azure Site Recovery?

Ebből a cikkből megismerheti az [Azure Site Recovery](site-recovery-overview.md) mögöttes architektúráját, illetve az azt működtető összetevőket.

Megjegyzéseit a cikk alján, vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.


## <a name="replication-to-azure"></a>Replikálás az Azure-ba

A következőket replikálhatja az Azure-ba:
- **VMware**: [Támogatott gazdagépen](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) futó helyszíni VMware virtuális gépek. A [támogatott operációs rendszereket](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) futtató VMware virtuális gépeket replikálhatja.
- **Hyper-V**: [Támogatott gazdagépeken](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) futó helyszíni Hyper-V virtuális gépek.
- **Fizikai gépek**: [Támogatott operációs rendszereken](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) Windowst vagy Linuxot futtató helyszíni fizikai kiszolgálók. A [Hyper-V és az Azure által támogatott](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bármilyen vendég operációs rendszert futtató Hyper-V-alapú virtuális gépet replikálhat.

## <a name="vmware-replication-to-azure"></a>VMware–Azure replikálás

Terület | Összetevő | Részletek
--- | --- | ---
**Azure** | Az Azure-ban szüksége van egy Azure-fiókra, egy Azure Storage-fiókra és egy Azure-hálózatra. | A Storage és a hálózat lehet Resource Manager- vagy klasszikus fiók.<br/><br/>  A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | Egyetlen felügyeleti kiszolgáló (VMware virtuális gép) futtatja az összes helyszíni összetevőt – a konfigurációs kiszolgálót, a folyamatkiszolgálót és a fő célkiszolgálót | A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
 **Folyamatkiszolgáló**:  | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. | Replikációs átjáróként üzemel. Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja őket, majd továbbítja az Azure Storage-nak.<br/><br/> A folyamatkiszolgáló ezenfelül kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését.<br/><br/> Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.
 **Fő célkiszolgáló** | Alapértelmezés szerint telepítve van a helyszíni konfigurációs kiszolgálón. | Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.<br/><br/> Ha a feladat-visszavételi adatforgalom kötetei nagyok, a feladat-visszavételhez üzembe helyezhet egy másik fő célkiszolgálót.
**VMware-kiszolgálók** | A VMware virtuális gépek vSphere ESXi-kiszolgálókon futnak, és a gazdagépek felügyeletéhez egy vCenter-kiszolgáló üzembe helyezését javasoljuk. | Vegyen fel VMware-kiszolgálókat a Recovery Services-tárolóba.<br/><br/> I
**Replikált gépek** | A mobilitási szolgáltatás az összes replikálni kívánt VMware virtuális gépen telepítve lesz. A szolgáltatás manuálisan is telepíthető az egyes gépekre, de leküldéses telepítés is végrehajtható a folyamatkiszolgálóról.

**1. ábra: Összetevők: VMware-ről Azure-ra**

![Összetevők](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Replikációs folyamat

1. Az üzemelő példány beállításának részeként az Azure-összetevőket és egy Recovery Services-tárolót is be kell állítani. A tárolóban meg kell adni a replikáció forrás- és célhelyét, be kell állítani a konfigurációs kiszolgálót, fel kell venni VMware-kiszolgálókat, replikációs szabályzatot kell létrehozni, üzembe kell helyezni a mobilitási szolgáltatást, engedélyezni kell a replikálást, illetve futtatni kell egy feladatátvételi tesztet.
2.  A gépek a replikációs szabályzat szerint kezdik meg a replikálást, és az adatok kezdeti másolata az Azure-tárolóba lesz replikálva.
4. Az Azure változáskülönbözeteinek replikációja a kezdeti replikálás befejezése után kezdődik el. A gépek nyomon követett módosításait a rendszer egy .hrl fájlban tárolja.
    - A replikációs folyamat kezelése érdekében a replikálást végző gépek a 443-as bejövő HTTPS-porton kommunikálnak a konfigurációs kiszolgálóval.
    - A replikálást végző gépek a 9443-as bejövő HTTPS-porton küldik el a replikációs adatokat a folyamatkiszolgálónak (ez a beállítás konfigurálható).
    - Az Azure-replikációs folyamat kezelését a konfigurációs kiszolgáló a 443-as kimenő HTTPS-porton keresztül végzi el.
    - A folyamatkiszolgáló adatokat fogad a forrásgépekről, amelyeket optimalizál és titkosít, majd a 443-as kimenő porton küldi elküld az Azure-tárolóba.
    - Ha engedélyezve van a több virtuális gépre kiterjedő konzisztencia, a replikációs csoportban található gépek a 20004-es porton kommunikálnak egymással. Több virtuális gépes környezetről akkor beszélünk, ha a gépek feladatátvételkor azonos összeomlásbiztos és alkalmazáskonzisztens helyreállítási pontokat használó replikációs csoportokba vannak rendezve. Ez akkor lehet hasznos, ha a gépek ugyanazt a számítási feladatot futtatják, így konzisztensnek kell maradniuk.
5. Az adatforgalmat a rendszer az interneten keresztül az Azure Storage nyilvános végpontjaira replikálja. Erre a célra az Azure ExpressRoute [nyilvános társviszony-létesítési](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering) szolgáltatását is használhatja. Az adatforgalmat helyszíni helyek és az Azure között helyek közötti VPN-en keresztül nem lehet replikálni.

**2. ábra: Replikáció VMware-ről Azure-ra**

![Továbbfejlesztett](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

1. Miután ellenőrizte, hogy a feladatátvételi teszt a várt módon működik-e, igény szerint nem tervezett feladatátvételeket futtathat az Azure-hoz. A tervezett feladatátvétel nem támogatott.
2. Elvégezheti egyetlen gép feladatátvételét, vagy létrehozhat több virtuális gép feladatátvételét tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
3. Feladatátvétel futtatásakor az Azure-ban replikaként létrehozott virtuális gépek jönnek létre. Akkor kell feladatátvételt végezni, ha hozzá szeretne férni a replikaként létrehozott Azure virtuális gép számítási feladataihoz.
4. Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat. Ehhez be kell állítani a feladat-visszavételi infrastruktúrát, el kell kezdeni a gép replikálását a másodlagos helyről az elsődlegesre, valamint nem tervezett feladatátvételt kell futtatni a másodlagos helyről. Ezen feladatátvétel végrehajtását követően az adatok visszakerülnek a helyszíni helyre, és az Azure-ba történő replikációt újra engedélyezni kell. [További információ](site-recovery-failback-azure-to-vmware.md)

A feladat-visszavételre vonatkozó követelmények:


- **Ideiglenes folyamatkiszolgáló az Azure-ban**: ha feladatátvételt követően szeretné visszaadni a feladatokat az Azure-ból, be kell állítania egy folyamatkiszolgálóként üzemelő Azure virtuális gépet, amely kezeli az Azure-ból való replikációt. Ez a virtuális gép a feladatok visszaadását követően törölhető.
- **VPN-kapcsolat**: a feladat-visszavételhez szüksége lesz egy VPN-kapcsolatra (vagy Azure ExpressRoute-ra), amelyet a helyszíni hely Azure-hálózatában kell beállítania.
- **Önálló helyszíni fő célkiszolgáló**: a helyszíni fő célkiszolgáló kezeli a feladat-visszavételt. A fő célkiszolgálót alapértelmezés szerint a felügyeleti kiszolgálóra kell telepíteni, de nagyobb mértékű forgalom feladat-visszavétele esetén érdemes ebből a célból egy önálló helyszíni fő célkiszolgálót is létrehozni.
- **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A replikációs szabályzat létrehozásakor a rendszer ezt automatikusan létrehozza.

**3. ábra: VMware-/fizikai gépek közötti feladat-visszavétel**

![Feladat-visszavétel](./media/site-recovery-components/enhanced-failback.png)

## <a name="physical-server-replication-to-azure"></a>Fizikai kiszolgáló replikálása az Azure-ba

Ez a replikációs forgatókönyv ugyanazokat az összetevőket és folyamatot használja, mint a [VMware–Azure replikálás](#vmware-replication-to-azure), a következő eltérésekkel:

- VMware virtuális gép helyett fizikai kiszolgálót használhat konfigurációs kiszolgálóként
- A feladat-visszavételhez helyszíni VMware-infrastruktúrára van szükség. Fizikai gép nem használható a feladat-visszavételhez.

## <a name="hyper-v-replication-to-azure"></a>Hyper-V–Azure replikálás

**Terület** | **Összetevő** | **Részletek**
--- | --- | ---
**Azure** | Az Azure-ban szüksége van egy Microsoft Azure-fiókra, és Azure Storage-fiókra és egy Azure-hálózatra. | A Storage és a hálózat lehet Resource Manager-alapú vagy klasszikus fiók.<br/><br/> A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | VMM-felhőkben lévő Hyper-V-gazdagépek | Ha a Hyper-V-gazdagépeket VMM felhőben felügyeli, a Recovery Services-tárolóban regisztrálja a VMM-kiszolgálót.<br/><br/> A VMM-kiszolgálón telepítse a Site Recovery Providert az Azure-ral való replikáció vezényléséhez.<br/><br/> A hálózatleképezés konfigurálásához logikai és virtuálisgép-hálózatokat is be kell állítania. Egy virtuálisgép-hálózatot össze kell kötnie egy felhőhöz társított logikai hálózattal.
**Hyper-V gazdagép** | A Hyper-V-kiszolgálók VMM-kiszolgálóval vagy anélkül is üzembe helyezhetők. | Ha nincs VMM-kiszolgáló, a Site Recovery Providert a gazdagépen kell telepíteni, hogy vezényelni tudja az internetes replikációt a Site Recoveryval. Ha van VMM-kiszolgáló, a Provider azon van telepítve, és nem a gazdagépen.<br/><br/> A Recovery Services-ügynököt a gazdagépen kell telepíteni az adatreplikáció kezelése érdekében.<br/><br/> A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón legalább egy virtuális gépnek kell lennie. | A virtuális gépekre semmit nem kell explicit módon telepíteni


### <a name="replication-process"></a>Replikációs folyamat

1. Az Azure-összetevők beállítása. Javasoljuk, hogy a Site Recovery üzembe helyezésének megkezdése előtt hozza létre a Storage- és hálózati fiókokat.
2. Létrehoz egy replikációsszolgáltatás-tárolót a Site Recoveryhez, és konfigurálja a tároló beállításait, például:
    - Forrás- és célbeállítások. Ha a Hyper-V-gazdagépeket nem VMM-felhőben felügyeli, akkor létrehoz egy Hyper-V helytárolót a cél számára, és hozzáad Hyper-V-gazdagépeket. Ha a Hyper-V-gazdagépeket VMM-ben felügyeli, a forrás a VMM-felhő. A cél az Azure.
    - Az Azure Site Recovery Provider és a Microsoft Azure Recovery Services ügynök telepítése. Ha rendelkezik VMM-mel, a Provider arra lesz telepítve, az ügynök pedig az egyes Hyper-V gazdagépekre. Ha nem rendelkezik VMM-mel, a Provider és az ügynök is az egyes gazdagépekre lesz telepítve.
    - Létrehoz egy replikációs házirendet a Hyper-V helyhez vagy a VMM-felhőhöz. A házirendet ezután a rendszer minden, a helyen vagy a felhőben lévő gazdagépen található virtuális gépre alkalmazza.
    - Engedélyezi a replikációt a Hyper-V virtuális gépek számára. A kezdeti replikálás a replikációs házirend beállításainak megfelelően történik.
4. Az adatváltozásokat a rendszer nyomon követi, és az Azure változáskülönbözeteinek replikálása a kezdeti replikálás befejezése után kezdődik meg. Az elemek nyomon követett módosításait a rendszer .hrl fájlokban tárolja.
5. Egy teszt feladatátvétel futtatásával ellenőrzi, hogy minden jól működik-e.

### <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett [feladatátvételt](site-recovery-failover.md) a helyszíni Hyper-V virtuális gépekről az Azure-ra. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
4. A feladatátvétel futtatása után a létrehozott replika virtuális gépeknek meg kell jelenniük az Azure-ban. Hozzárendelhet egy nyilvános IP-címet a virtuális géphez, ha szükséges.
5. Ezután véglegesíti a feladatátvételt, hogy hozzáférhessen a replika Azure virtuális gép számítási feladataihoz.
6. Amint az elsődleges helyszíni hely megint elérhetővé válik, [visszaadhatja](site-recovery-failback-from-azure-to-hyper-v.md) a feladatokat. Elindít egy tervezett feladatátvételt az Azure-ból az elsődleges helyre. Tervezett feladatátvétel esetében beállíthatja, hogy a feladat-visszavétel ugyanarra a virtuális gépre vagy egy másik helyre történjen, és szinkronizálhatja a módosításokat az Azure és a helyszíni hely között, így elkerülhető az adatvesztés. Ha a helyszínen létrejöttek a virtuális gépek, véglegesíti a feladatátvételt.

**4. ábra: Replikálás Hyper-V-helyről az Azure-ba**

![Összetevők](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**5. ábra: Replikálás a VMM-felhőkben futó Hyper-V-ről az Azure-ba**

![Összetevők](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-to-a-secondary-site"></a>Replikálás másodlagos helyre

A következőket replikálhatja másodlagos helyre:

- **VMware**: [Támogatott gazdagépen](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) futó helyszíni VMware virtuális gépek. A [támogatott operációs rendszereket](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions) futtató VMware virtuális gépeket replikálhatja.
- **Fizikai gépek**: [Támogatott operációs rendszereken](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions) Windowst vagy Linuxot futtató helyszíni fizikai kiszolgálók.
- **Hyper-V**: VMM-felhőkben felügyelt [támogatott Hyper-V-gazdagépeken](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) futó helyszíni Hyper-V virtuális gépek. [támogatott gazdagépek](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). A [Hyper-V és az Azure által támogatott](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bármilyen vendég operációs rendszert futtató Hyper-V-alapú virtuális gépet replikálhat.


## <a name="vmware-vmphysical-server-replication-to-a-secondary-site"></a>VMware virtuális gép/fizikai kiszolgáló replikálása másodlagos helyre

### <a name="components"></a>Összetevők

**Terület** | **Összetevő** | **Részletek**
--- | --- | ---
**Azure** | A forgatókönyv üzembe helyezéséhez használja az InMage Scout segédprogramot. | Az InMage Scout beszerzéséhez Azure-előfizetésre van szüksége.<br/><br/> Miután létrehozott egy Site Recovery-tárolót, töltse le az InMage Scoutot, és telepítse a legújabb frissítéseket az üzembe helyezés előkészítéséhez.
**Folyamatkiszolgáló** | Az elsődleges helyen található | Helyezze üzembe a folyamatkiszolgálót, amely kezeli az adatok gyorsítótárazását, tömörítését és optimalizálását.<br/><br/> Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**Konfigurációs kiszolgáló** | A másodlagos helyen található | A konfigurációs kiszolgáló végzi az üzemelő példány felügyeleti webhelyen vagy a vContinuum-konzolban végzett felügyeletét, konfigurálását és megfigyelését.
**vContinuum-kiszolgáló** | Választható. Ugyanoda kell telepíteni, mint a konfigurációs kiszolgálót. | Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet.
**Fő célkiszolgáló** | A másodlagos helyen található | A fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat.<br/><br/> Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani.<br/><br/> Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra. A Unified Agent ügynök nincs telepítve ezen a kiszolgálón.
**VMware ESX/ESXi- és vCenter-kiszolgáló** |  A virtuális gépek ESX-/ESXi-gazdagépeken futnak. A gazdagépeket egy vCenter-kiszolgáló felügyeli | A VMware virtuális gépek replikálásához VMware-infrastruktúrára van szükség.
**Virtuális gépek/fizikai kiszolgálók** |  A replikálni kívánt VMware virtuális gépeken és fizikai kiszolgálókon telepített Unified Agent. | Ez az ügynök valósítja meg az összetevők közötti kommunikációt.


### <a name="replication-process"></a>Replikációs folyamat

1. Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt.
2. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak.
3. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

**6. ábra: VMware és VMware közötti replikáció**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="hyper-v-vm-replication-to-a-secondary-site"></a>Hyper-V virtuális gép replikációja másodlagos helyre


**Terület** | **Összetevő** | **Részletek**
--- | --- | ---
**Azure** | Szüksége van egy Microsoft Azure-fiókra. |
**VMM-kiszolgáló** | Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen | Mindegyik VMM-kiszolgálónak csatlakoznia kell az internethez.<br/><br/> Minden kiszolgálón legyen legalább egy VMM-magánfelhő beállított Hyper-V-kapacitásprofillal.<br/><br/> Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A Provider és az Azure közötti kommunikáció biztonságos, titkosított csatornákon történik.
**Hyper-V kiszolgáló** |  Legalább egy Hyper-V gazdakiszolgáló az elsődleges és a másodlagos VMM-felhőkben.<br/><br/> A kiszolgálóknak csatlakozniuk kell az internethez.<br/><br/> A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Hyper-V virtuális gépek** | A forrás Hyper-V gazdakiszolgálón található. | A forrás gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

### <a name="replication-process"></a>Replikációs folyamat

1. Beállítja az Azure-fiókot.
2. Létrehoz egy replikációsszolgáltatás-tárolót a Site Recoveryhez, és konfigurálja a tároló beállításait, például:

    - A replikációs forrást és célt (elsődleges és másodlagos helyek).
    - Az Azure Site Recovery Provider és a Microsoft Azure Recovery Services ügynök telepítése. A Provider VMM-kiszolgálókon, az ügynök pedig az egyes Hyper-V gazdagépeken van telepítve.
    - Létrehoz egy replikációs házirendet a forrás VMM-felhőhöz. A házirendet ezután a rendszer minden, a felhőben lévő gazdagépen található virtuális gépre alkalmazza.
    - Engedélyezi a replikációt a Hyper-V virtuális gépek számára. A kezdeti replikálás a replikációs házirend beállításainak megfelelően történik.
4. Az adatváltozásokat a rendszer nyomon követi, és a változáskülönbözetek replikálása a kezdeti replikálás befejezése után kezdődik meg. Az elemek nyomon követett módosításait a rendszer .hrl fájlokban tárolja.
5. Egy teszt feladatátvétel futtatásával ellenőrzi, hogy minden jól működik-e.

**7. ábra: VMM és VVM közötti replikáció**

![Két helyszíni hely közötti replikálás](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback"></a>Feladatátvétel és feladat-visszavétel

1. Futtathat tervezett vagy nem tervezett [feladatátvételt](site-recovery-failover.md) a helyszíni helyek között. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
4. Ha elvégez egy nem tervezett feladatátvételt egy másodlagos helyre, a feladatátvétel után a másodlagos hely gépei nem engedélyezettek védelemhez vagy replikáláshoz. Ha tervezett feladatátvételt futtatott, a feladatátvétel után a másodlagos hely gépei védettek lesznek.
5. Ekkor véglegesíti a feladatátvételt, hogy hozzáférhessen a replika virtuális gép számítási feladataihoz.
6. Amikor az elsődleges hely újra elérhetővé válik, fordított replikálást hajt végre a másodlagos helyről az elsődleges helyre való replikáláshoz. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
7. Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.


## <a name="hyper-v-replication-workflow"></a>A Hyper-V replikálás munkafolyamata

**Munkafolyamat fázisa** | **Művelet**
--- | ---
1. **Védelem engedélyezése** | Miután engedélyezte a védelmet egy Hyper-V virtuális gép esetében, elindul a **Védelem engedélyezése** feladat, amely ellenőrzi, hogy a gép megfelel-e az előfeltételeknek. A feladat két metódust hív meg:<br/><br/> A [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) beállítja a replikációt a konfigurált beállításokkal.<br/><br/> A [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) inicializál egy teljes virtuálisgép-replikációt.
2. **Kezdeti replikálás** |  Egy pillanatkép készül a virtuális gépről, majd a rendszer egyenként replikálja a virtuális merevlemezeket, amíg az összes át nem lesz másolva a másodlagos helyre.<br/><br/> Az ehhez a művelethez szükséges időtartam a virtuális gép méretétől, a hálózati sávszélességtől, illetve a kezdeti replikáció módjától függ.<br/><br/> Ha módosítják a lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V-replikációkövető nyomon követi a módosításokat replikálási naplók (.hrl) formájában, amelyek ugyanabban a mappában találhatók, mint a lemezek.<br/><br/> Minden lemezhez tartozik egy .hrl-fájl, amelyet a rendszer továbbít a másodlagos tárterületre.<br/><br/> A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét, valamint szinkronizálja és egyesíti a naplózott lemezmódosításokat.
3. **Védelem véglegesítése** | A kezdeti replikálás befejezése után a **Védelem véglegesítése** feladat konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez.<br/><br/> Azure-ba történő replikálás esetén előfordulhat, hogy módosítania kell a virtuális gép beállításait, hogy az készen álljon a feladatátvételre.<br/><br/> Ezen a ponton érdemes lehet feladatátvételi tesztet futtatni, amellyel ellenőrizheti, hogy minden megfelelően működik-e.
4. **Replikáció** | A kezdeti replikálást követően a replikációs beállításoknak megfelelően elindul a változások szinkronizálása.<br/><br/> **Sikertelen replikáció**: ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer újraszinkronizálást végez. Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra. Az újraszinkronizálás kiszámítja a forrás és a cél virtuális gépek ellenőrzőösszegeit, és ezek alapján csak a változásokat továbbítja, így segít csökkenti az adatmennyiséget. Az újraszinkronizálás befejezését követően folytatódik a változásreplikálás. Alapértelmezés szerint a rendszer automatikusan munkaidőn kívüli időpontra ütemezi az újraszinkronizálást, de manuálisan is elvégezhető a virtuális gép újraszinkronizálása.<br/><br/> **Replikációs hiba**: ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Ha nem helyrehozható hiba, például hitelesítési vagy engedélyezési hiba történik, vagy egy replikagép érvénytelen állapotban van, a rendszer nem próbálkozik újra. Ha helyrehozható hiba, például hálózati hiba vagy alacsony lemezterülettel/memóriával kapcsolatos hiba lép fel, a rendszer újrapróbálkozik, és folyamatosan növeli a próbálkozások közötti időintervallumot (1, 2, 4, 8, 10, majd 30 perc).
5. **Tervezett/nem tervezett feladatátvétel** | Igény szerint futtathat tervezett/nem tervezett feladatátvételeket.<br/><br/> Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.<br/><br/> A replikaként létrehozott virtuális gépeket a rendszer függőben lévő állapotba helyezi. A feladatátvétel befejezéséhez véglegesíteni kell ezeket.<br/><br/> Ha az elsődleges hely újból működik, visszaadhatja a feladatokat az elsődleges helynek, amikor elérhetővé válik.


**8. ábra: Hyper-V-munkafolyamat**

![munkafolyamat](./media/site-recovery-components/arch-hyperv-azure-workflow.png)




## <a name="next-steps"></a>Következő lépések

[Előfeltételek ellenőrzése](site-recovery-prereq.md)

