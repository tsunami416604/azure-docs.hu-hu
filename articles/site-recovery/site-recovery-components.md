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
ms.date: 01/02/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: bd8082c46ee36c70e372208d1bd15337acc558a1
ms.openlocfilehash: eb97f66901efa336942dee56d9a8a62ade1f6842


---
# <a name="how-does-azure-site-recovery-work"></a>Hogy működik az Azure Site Recovery?

Ebből a cikkből megismerheti az Azure Site Recovery mögöttes architektúráját, illetve az azt működtető összetevőket.

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. Fontos, hogy a BCDR stratégia gondoskodjon a vállalati adatok biztonságáról és helyreállíthatóságáról, és lehetővé tegye, hogy a számítási feladatok még vészhelyzet esetén se álljanak le.

Az Azure Site Recovery szolgáltatása a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával segít a stratégia kidolgozásában. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információ: [Mi a Site Recovery?](site-recovery-overview.md)

A jelen cikk az [Azure Portalon](https://portal.azure.com) végzett üzembe helyezést ismerteti. A [klasszikus Azure portál](https://manage.windowsazure.com/) használható a meglévő Site Recovery-tárolók fenntartására, de új tárolókat nem hozhat létre.

Felmerülő megjegyzéseit a cikk alján teheti közzé. Műszaki jellegű kérdéseit az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) tegye fel.


## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Site Recovery számos forgatókönyv esetében használható replikációra:

- **VMware virtuális gépek replikálása**: A helyszíni VMware virtuális gépeket az Azure-ba és másodlagos adatközpontokba is lehet replikálni.
- **Fizikai gépek replikálása**: A (Windowst vagy Linuxot futtató) fizikai gépeket az Azure-ba és másodlagos adatközpontokba is lehet replikálni. A fizikai gépek és a VMware virtuális gépek replikálási folyamata csaknem megegyezik.
- **Hyper-V virtuális gépek replikálása**: A Hyper-V virtuális gépeket Azure-tárolóba vagy másodlagos VMM-helyre is lehet replikálni. Másodlagos helyre csak System Center Virtual Machine Manager-felhőkben (VMM-felhőkben) felügyelt virtuális gépeket lehet replikálni.
- **Virtuális gépek áttelepítése**: A helyszíni virtuális gépek és fizikai kiszolgálók Azure-ba történő replikálásán (replikálásán, feladatátvételén és feladat-visszavételén) kívül arra is lehetősége van, hogy ezeket Azure virtuális gépekre telepítse át (ilyenkor csak replikálás és feladatátvétel végezhető el, feladat-visszavétel nem). A következőket telepítheti át:
    - Áttelepíthet helyszíni Hyper-V virtuális gépeken, VMWare virtuális gépeken és fizikai kiszolgálókon futó számítási feladatokat Azure virtuális gépekre.
    - Áttelepíthet [Azure IaaS virtuális gépeket](site-recovery-migrate-azure-to-azure.md) Azure-régiók között. Ebben az esetben jelenleg csak az áttelepítés támogatott, a feladat-visszavétel nem.
    - [AWS Windows-példányokat](site-recovery-migrate-aws-to-azure.md) Azure IaaS virtuális gépekre. Ebben az esetben jelenleg csak az áttelepítés támogatott, a feladat-visszavétel nem.

A Site Recovery a támogatott virtuális gépeken és fizikai kiszolgálókon futó alkalmazásokat replikálja. A támogatott alkalmazások teljes körű összefoglalóját megtalálja a [Milyen számítási feladatokat tud védeni az Azure Site Recovery?](site-recovery-workload.md) című cikkben.

## <a name="replicate-vmware-vmsphysical-servers-to-azure"></a>VMware virtuális gépek vagy fizikai kiszolgálók replikálása az Azure-ba

### <a name="components"></a>Összetevők

**Összetevő** | **Részletek**
--- | ---
**Azure** | Az Azure-ban szüksége van egy Microsoft Azure-fiókra, és Azure Storage-fiókra és egy Azure-hálózatra.<br/><br/> A Storage és a hálózat lehet Resource Manager- vagy klasszikus fiók.<br/><br/>  A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal. Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**Konfigurációs kiszolgáló** | A helyszíni hely és az Azure közötti kommunikáció koordinálásához és az adatreplikáció kezeléséhez beállíthat egy helyszíni konfigurációs kiszolgálót.
**Folyamatkiszolgáló** | Alapértelmezés szerint telepítve van a helyszíni konfigurációs kiszolgálón.<br/><br/> Replikációs átjáróként üzemel. Ez az összetevő fogadja a védett forrásgépek adatait, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, majd továbbítja az adatokat az Azure Storage-nak.<br/><br/> Kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését.<br/><br/> Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.
**Fő célkiszolgáló** | Alapértelmezés szerint telepítve van a helyszíni konfigurációs kiszolgálón.<br/><br/> Az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat. Ha a feladat-visszavételi adatforgalom kötetei nagyok, a feladat-visszavételhez üzembe helyezhet egy másik fő célkiszolgálót.
**VMware-kiszolgálók** | A VMware virtuális gépek replikálásához vegyen fel vCenter- és vSphere-kiszolgálókat a Recovery Services-tárolóba.<br/><br/> Fizikai kiszolgálók replikálása esetén a feladat-visszavételhez helyszíni VMware-infrastruktúrára van szükség. Fizikai gép nem használható a feladat-visszavételhez.
**Replikált gépek** | A mobilitási szolgáltatásnak az összes replikálni kívánt gépen telepítve kell lennie. A szolgáltatás manuálisan is telepíthető az egyes gépekre, de leküldéses telepítés is végrehajtható a folyamatkiszolgálóról.

**1. ábra: Összetevők: VMware-ről Azure-ra**

![Összetevők](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Replikációs folyamat

1. Az üzembe helyezés beállításának részeként az Azure-összetevőket és egy Recovery Services-tárolót is be kell állítani. A tárolóban meg kell adni a replikáció forrás- és célhelyét, be kell állítani a konfigurációs kiszolgálót, fel kell venni VMware-kiszolgálókat, replikációs szabályzatot kell létrehozni, üzembe kell helyezni a mobilitási szolgáltatást, engedélyezni kell a replikálást, illetve futtatni kell egy feladatátvételi tesztet.
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

### <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. A helyszíni VMWare virtuális gépekről és fizikai kiszolgálókról az Azure-ra csak nem tervezett feladatátvételeket lehet futtatni. A tervezett feladatátvétel nem támogatott.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
3. Feladatátvétel futtatásakor az Azure-ban replikaként létrehozott virtuális gépek jönnek létre. Akkor kell feladatátvételt végezni, ha hozzá szeretne férni a replikaként létrehozott Azure virtuális gép számítási feladataihoz.
4. Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat. Ehhez be kell állítani a feladat-visszavételi infrastruktúrát, el kell kezdeni a gép replikálását a másodlagos helyről az elsődlegesre, valamint nem tervezett feladatátvételt kell futtatni a másodlagos helyről. Ezen feladatátvétel végrehajtását követően az adatok visszakerülnek a helyszíni helyre, és az Azure-ba történő replikációt újra engedélyezni kell. [További információ](site-recovery-failback-azure-to-vmware.md)

A feladat-visszavételre vonatkozó követelmények:

- **A fizikai gépek közötti feladat-visszavétel nem támogatott**: ez azt jelenti, hogy ha a fizikai kiszolgálók feladatait átadja az Azure-nak, akkor a feladat-visszavétel célja VMware virtuális gép lehet. Fizikai kiszolgáló nem használható a feladat-visszavételhez. A feladat-visszavételhez egy Azure virtuális gépre lesz szüksége, és ha a konfigurációs kiszolgálót nem VMware virtuális gépként helyezte üzembe, akkor egy különálló fő célkiszolgálót kell beállítania VMware virtuális gépként. Ez azért szükséges, mert a fő célkiszolgáló együttműködik és kapcsolatba lép a VMware-tárral a lemezeknek a VMware virtuális gépre történő visszaállításához.
- **Ideiglenes folyamatkiszolgáló az Azure-ban**: ha feladatátvételt követően szeretné visszaadni a feladatokat az Azure-ból, be kell állítania egy folyamatkiszolgálóként üzemelő Azure virtuális gépet, amely kezeli az Azure-ból való replikációt. Ez a virtuális gép a feladatok visszaadását követően törölhető.
- **VPN-kapcsolat**: a feladat-visszavételhez szüksége lesz egy VPN-kapcsolatra (vagy Azure ExpressRoute-ra), amelyet a helyszíni hely Azure-hálózatában kell beállítania.
- **Önálló helyszíni fő célkiszolgáló**: a helyszíni fő célkiszolgáló kezeli a feladat-visszavételt. A fő célkiszolgálót alapértelmezés szerint a felügyeleti kiszolgálóra kell telepíteni, de nagyobb mértékű forgalom feladat-visszavétele esetén érdemes ebből a célból egy önálló helyszíni fő célkiszolgálót is létrehozni.
- **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. A replikációs szabályzat létrehozásakor a rendszer ezt automatikusan létrehozza.

**3. ábra: VMware-/fizikai gépek közötti feladat-visszavétel**

![Feladat-visszavétel](./media/site-recovery-components/enhanced-failback.png)


## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>VMware virtuális gépek/fizikai kiszolgálók replikálása másodlagos helyre

### <a name="components"></a>Összetevők

**Összetevő** | **Részletek**
--- | ---
**Azure** | A forgatókönyv üzembe helyezéséhez használja az InMage Scout segédprogramot. Ennek beszerzéséhez Azure-előfizetésre lesz szüksége.<br/><br/> Miután létrehozott egy Site Recovery-tárolót, töltse le az InMage Scoutot, és telepítse a legújabb frissítéseket az üzembe helyezés előkészítéséhez.
**Folyamatkiszolgáló** | Helyezze üzembe a folyamatkiszolgáló összetevőt az elsődleges helyen. Ez végzi az adatok gyorsítótárazását, tömörítését és optimalizálását.<br/><br/> Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**VMware ESX/ESXi- és vCenter-kiszolgáló** |  A VMware virtuális gépek replikálásához VMware-infrastruktúrára van szükség.
**Virtuális gépek/fizikai kiszolgálók** |  A replikálni kívánt VMware virtuális gépeken, illetve a windowsos/linuxos fizikai kiszolgálókon telepítenie kell a Unified Agent ügynököt.<br/><br/> Ez az ügynök valósítja meg az összetevők közötti kommunikációt.
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgálót telepíteni kell a másodlagos helyre az üzemelő példány felügyeleti weblapon vagy a vContinuum-konzolban végzett felügyelete, konfigurálása és megfigyelése céljából.
**vContinuum-kiszolgáló** | Ugyanoda kell telepíteni, mint a konfigurációs kiszolgálót.<br/><br/> Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet.
**Fő célkiszolgáló (másodlagos hely)** | A fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat.<br/><br/> Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani.<br/><br/> Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra. A Unified Agent ügynök nincs telepítve ezen a kiszolgálón.

### <a name="replication-process"></a>Replikációs folyamat

1. Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt.
2. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak.
3. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

**4. ábra: VMware és VMware közötti replikáció**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="replicate-hyper-v-vms-to-azure"></a>Hyper-V virtuális gépek replikálása az Azure-ba


### <a name="components"></a>Összetevők

**Összetevő** | **Részletek**
--- | ---
**Azure** | Az Azure-ban szüksége van egy Microsoft Azure-fiókra, és Azure Storage-fiókra és egy Azure-hálózatra.<br/><br/> A Storage és a hálózat lehet Resource Manager-alapú vagy klasszikus fiók.<br/><br/> A replikált adatokat a tárfiók tárolja, ha pedig feladatátvétel következik be a helyszíni helyről, a rendszer Azure virtuális gépeket hoz létre a replikált adatokkal.<br/><br/> Az Azure virtuális gépek a létrejöttükkor csatlakoznak az Azure virtuális hálózathoz.
**VMM-kiszolgáló** | Ha a Hyper-V gazdagépek tárolása VMM-felhőkben történik, a [hálózatleképezés](site-recovery-network-mapping.md) konfigurálásához logikai és virtuálisgép-hálózatokat is be kell állítani. Egy virtuálisgép-hálózatot össze kell kötnie egy felhőhöz társított logikai hálózattal.
**Hyper-V gazdagép** | Szüksége van legalább egy Hyper-V gazdakiszolgálóra.
**Hyper-V virtuális gépek** | A Hyper-V gazdakiszolgálón legalább egy virtuális gépnek kell lennie. A Hyper-V gazdagépen futó Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. Az ügynök a HTTPS-protokollal (a 443-as porton) végzi az adatok replikálását. A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.


## <a name="replication-process"></a>Replikációs folyamat

1. Az Azure-összetevők beállítása. Javasoljuk, hogy a Site Recovery üzembe helyezésének megkezdése előtt hozza létre a Storage- és hálózati fiókokat.
2. Létrehoz egy replikációsszolgáltatás-tárolót a Site Recoveryhez, és konfigurálja a tároló beállításait, például:
    - Ha a Hyper-V gazdagépeket nem VMM-felhőben felügyeli, akkor létrehoz egy Hyper-V helytárolót, és hozzáad Hyper-V gazdagépeket.
    - A replikációs forrást és célt. Ha a Hyper-V gazdagépeket VMM-ben felügyeli, a forrás a VMM-felhő. Ha nem, a forrás a Hyper-V hely.
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
6. Amint az elsődleges helyszíni hely megint elérhetővé válik, visszaadhatja a feladatokat. Elindít egy tervezett feladatátvételt az Azure-ból az elsődleges helyre. Tervezett feladatátvétel esetében beállíthatja, hogy a feladat-visszavétel ugyanarra a virtuális gépre vagy egy másik helyre történjen, és szinkronizálhatja a módosításokat az Azure és a helyszíni hely között, így elkerülhető az adatvesztés. Ha a helyszínen létrejöttek a virtuális gépek, véglegesíti a feladatátvételt.

**5. ábra: Replikálás Hyper-V helyről az Azure-ba**

![Összetevők](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**6. ábra: Replikálás a VMM-felhőkben futó Hyper-V-ről az Azure-ba**

![Összetevők](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)



## <a name="replicate-hyper-v-vms-to-a-secondary-site"></a>Hyper-V virtuális gépek replikálása másodlagos helyre

### <a name="components"></a>Összetevők

**Összetevő** | **Részletek**
--- | ---
**Azure-fiók** | Szüksége van egy Microsoft Azure-fiókra.
**VMM-kiszolgáló** | Javasoljuk, hogy legyen egy internethez csatlakozó VMM-kiszolgáló az elsődleges helyen és egy a másodlagos helyen.<br/><br/> Minden kiszolgálón legyen legalább egy VMM-magánfelhő beállított Hyper-V-kapacitásprofillal.<br/><br/> Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A Provider és az Azure közötti kommunikáció biztonságos, titkosított csatornákon történik.
**Hyper-V kiszolgáló** |  Szükség van legalább egy Hyper-V gazdakiszolgálóra az elsődleges és a másodlagos VMM-felhőkben. A kiszolgálóknak csatlakozniuk kell az internethez.<br/><br/> A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
**Forrásgépek** | A forrás Hyper-V gazdakiszolgálókon legalább egy replikálni kívánt virtuális gépnek kell futnia.

## <a name="replication-process"></a>Replikációs folyamat

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

### <a name="failover-and-failback-process"></a>Feladatátvételi és feladat-visszavételi folyamat

1. Futtathat tervezett vagy nem tervezett [feladatátvételt](site-recovery-failover.md) a helyszíni helyek között. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés.
2. Elvégezheti egy gép feladatátadását, de létrehozhat több gép összehangolt feladatátadását tartalmazó [helyreállítási terveket](site-recovery-create-recovery-plans.md) is.
4. Ha elvégez egy nem tervezett feladatátvételt egy másodlagos helyre, a feladatátvétel után a másodlagos hely gépei nem engedélyezettek védelemhez vagy replikáláshoz. Ha tervezett feladatátvételt hajtott végre, a feladatátvétel után a másodlagos hely gépei védettek.
5. Ekkor véglegesíti a feladatátvételt, hogy hozzáférhessen a replika virtuális gép számítási feladataihoz.
6. Amikor az elsődleges hely újra elérhetővé válik, fordított replikálást hajt végre a másodlagos helyről az elsődleges helyre való replikáláshoz. A fordított replikáció során a virtuális gépek védett állapotba kerülnek, de a másodlagos adatközpont marad továbbra is az aktív hely.
7. Ha azt szeretné, hogy újra az elsődleges hely legyen az aktív hely, kezdeményezzen egy tervezett feladatátvételt a másodlagos helyről az elsődleges helyre, majd hajtson végre ismét fordított replikálást.


### <a name="hyper-v-replication-workflow"></a>A Hyper-V replikálás munkafolyamata

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

[Felkészülés az üzembe helyezésre](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO1-->


