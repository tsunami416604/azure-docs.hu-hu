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
ms.date: 11/14/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 7455d6f99ed8ceb401224f98105f7b651f55c724
ms.openlocfilehash: 6c509700e85eed71de3555c04ea290a5611f75bd


---
# <a name="how-does-azure-site-recovery-work"></a>Hogy működik az Azure Site Recovery?

Ebből a cikkből megismerheti az Azure Site Recovery mögöttes architektúráját, illetve az azt működtető összetevőket.

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. Fontos, hogy a BCDR stratégia gondoskodjon a vállalati adatok biztonságáról és helyreállíthatóságáról, és lehetővé tegye, hogy a számítási feladatok még vészhelyzet esetén se álljanak le.

Az Azure Site Recovery szolgáltatása a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával segít a stratégia kidolgozásában. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információ: [Mi a Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery az Azure Portalon

Az Azure két különböző [üzemi modellel](../azure-resource-manager/resource-manager-deployment-model.md) rendelkezik az erőforrások létrehozásához és használatához: az Azure Resource Manager-modellel és a klasszikus szolgáltatáskezelési modellel. Az Azure-ban két különböző portál érhető el: a [klasszikus Azure portál](https://manage.windowsazure.com/), amely a klasszikus üzembe helyezési modellt, és az [Azure Portal](https://portal.azure.com), amely mindkét modellt támogatja.

A Site Recovery a klasszikus portálon és az Azure Portalon egyaránt elérhető. A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatásfelügyeleti modellel támogatható. Az Azure Portalon a klasszikus modell vagy az erőforrásmodell üzembe helyezései támogathatók. [Itt olvashat bővebben](site-recovery-overview.md#site-recovery-in-the-azure-portal) az Azure Portal használatával végzett üzembe helyezésről.

A cikk információi a klasszikus és az Azure Portalon üzemelő példányokra egyaránt vonatkoznak. Az esetleges eltéréseket jelezzük.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

A Site Recovery számos forgatókönyv esetében használható replikációra:

- **VMware virtuális gépek replikálása**: A helyszíni VMware virtuális gépeket az Azure-ba és másodlagos adatközpontokba is lehet replikálni.
- **Fizikai gépek replikálása**: A Windowst vagy Linuxot futtató fizikai gépeket az Azure-ba és másodlagos adatközpontokba is lehet replikálni. A fizikai gépek és a VMware virtuális gépek replikálási folyamata csaknem megegyezik.
- **Hyper-V virtuális gépek replikálása (VMM nélkül)**: a nem a VMM által felügyelt Hyper-V virtuális gépeket az Azure-ba lehet replikálni.
- **A System Center VMM-felhőben kezelt Hyper-V virtuális gépek replikálása**: a VMM-felhőkben található Hyper-V gazdakiszolgálókon futó helyszíni Hyper-V virtuális gépeket az Azure-ba vagy másodlagos adatközpontba is lehet replikálni. A replikáció standard Hyper-V-replikával vagy SAN-replikációval is elvégezhető.
- **Virtuális gépek**: a Site Recovery az [Azure-beli IaaS típusú virtuális gépek](site-recovery-migrate-azure-to-azure.md) régiók közötti áttelepítésére, illetve [AWS Windows-példányok](site-recovery-migrate-aws-to-azure.md) Azure-beli IaaS típusú virtuális gépekre való áttelepítésére is használható. Jelenleg csak az áttelepítés támogatott, tehát átadhatja a virtuális gépeket, de nem veheti őket vissza.

A Site Recovery a fent megnevezett típusú virtuális gépeken és fizikai kiszolgálókon futó legtöbb alkalmazást is képes replikálni. A támogatott alkalmazások teljes körű összefoglalóját megtalálja a [Milyen számítási feladatokat tud védeni az Azure Site Recovery?](site-recovery-workload.md) című cikkben.

## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Replikálás az Azure-ba: VMware virtuális gépek és fizikai Windows-/Linux-kiszolgálók
A VMware virtuális gépek többféleképpen is replikálhatók a Site Recovery használatával.

* **Az Azure Portal használata**: Amikor üzembe helyezi a Site Recoveryt az Azure Portalon, a virtuális gépek a klasszikus szolgáltatáskezelői tárolóba vagy a Resource Managernek adhatók át. A VMware virtuális gépeknek az Azure Portalon történő replikálása számos előnnyel jár, ilyen például a klasszikus tárba vagy a Resource Manager-tárba végzett replikálás lehetősége az Azure-ban. [További információk](site-recovery-vmware-to-azure.md).
* **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. Ennél az üzembe helyezésnél a virtuális gépek feladatai csak az Azure-beli klasszikus tárnak adhatók át, a Resource Manager-tárnak nem. [További információk](site-recovery-vmware-to-azure-classic.md).

A Site Recovery üzembe helyezési architekturális követelményei a VMware virtuális gépeknek/fizikai kiszolgálóknak az Azure Portal webhelyen vagy a (továbbfejlesztett) klasszikus Azure portálon végzett replikálására vonatkozóan hasonlóak, van azonban néhány eltérés:

* Ha az Azure Portalon végzi az üzembe helyezést, a replikáció Resource Manager-alapú tárolóba végezhető el, az Azure virtuális gépek pedig Resource Manager-hálózatokkal csatlakoztathatók a feladatátvétel után.
* Amikor az Azure Portalon végzi az üzembe helyezést, LRS- vagy GRS-tárolót is használhat. A klasszikus portálon a GRS-t kell használni.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.

A következőkre lesz szüksége:

- **Azure-fiók**: Szüksége van egy Microsoft Azure-fiókra.
- **Azure-tároló**: A replikált adatok tárolásához Azure-tárfiókra van szükség. Klasszikus fiókot vagy Resource Manager-tárfiókot is használhat. A fiók lehet LRS vagy GRS, amikor az Azure Portal webhelyen végzi az üzembe helyezést. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvételre kerül sor, a rendszer Azure virtuális gépeket hoz létre.
- **Azure-hálózat**: Szüksége van egy olyan Azure-beli virtuális hálózatra, amelyhez az Azure-beli virtuális gépek csatlakozni fognak, ha feladatátvétel során jönnek létre. Az Azure Portalon ezeket a hálózatokat a klasszikus szolgáltatáskezelői vagy a Resource Manager-modellben lehet létrehozni.
- **Helyszíni konfigurációs kiszolgáló**: Szüksége van egy helyszíni Windows Server 2012 R2 rendszert futtató gépre, amelyen elérhető a konfigurációs kiszolgáló, valamint más Site Recovery-összetevők. VMware virtuális gépek replikálásakor a gépnek magas rendelkezésre állású VMware virtuális gépnek kell lennie. Fizikai kiszolgálók replikálásakor a gép fizikai gép is lehet. A Site Recovery következő összetevői lesznek telepítve a gépre:
 
 - **Konfigurációs kiszolgáló**: a helyszíni környezet és az Azure közötti kommunikáció koordinálását, valamint az adatreplikáció és -helyreállítás kezelését végzi.
 - **Folyamatkiszolgáló**: replikációs átjáróként üzemel. Ez az összetevő fogadja a védett forrásgépek adatait, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, majd továbbítja az adatokat az Azure Storage-nak. Ezenfelül kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését. Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.
 - **Fő célkiszolgáló**: az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.
 
- **Replikálni kívánt VMware virtuális gépek vagy fizikai kiszolgálók**: Minden Azure-ba replikálni kívánt gépen telepítve kell lennie a mobilitási szolgáltatási összetevőnek. A szolgáltatás észleli a gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. Az összetevő manuálisan is telepíthető, de azt is beállíthatja, hogy a folyamatkiszolgáló automatikusan, leküldéses módszerrel telepítse, amikor engedélyezi az adott gép replikálását.
- **vSphere-gazdagépek/vCenter-kiszolgáló**: Szükség van legalább egy, VMware virtuális gépeket futtató vSphere-gazdakiszolgálóra. Javasoljuk, hogy a gazdagépek kezelésére helyezzen üzembe egy vCenter-kiszolgálót is.
- **Feladat-visszavétel**:  
 - **A fizikai gépek közötti feladat-visszavétel nem támogatott**: ez azt jelenti, hogy ha a fizikai kiszolgálók feladatait átadja az Azure-nak, akkor a feladat-visszavétel célja VMware virtuális gép lehet. Fizikai kiszolgáló nem használható a feladat-visszavételhez. A feladat-visszavételhez egy Azure virtuális gépre lesz szüksége, és ha a konfigurációs kiszolgálót nem VMware virtuális gépként helyezte üzembe, akkor egy különálló fő célkiszolgálót kell beállítania VMware virtuális gépként. Ez azért szükséges, mert a fő célkiszolgáló együttműködik és kapcsolatba lép a VMware-tárral a lemezeknek a VMware virtuális gépre történő visszaállításához.
 - **Ideiglenes folyamatkiszolgáló az Azure-ban**: ha feladatátvételt követően szeretné visszaadni a feladatokat az Azure-ból, be kell állítania egy folyamatkiszolgálóként üzemelő Azure virtuális gépet, amely kezeli az Azure-ból való replikációt. Ez a virtuális gép a feladatok visszaadását követően törölhető.
 - **VPN-kapcsolat**: a feladat-visszavételhez szüksége lesz egy VPN-kapcsolatra (vagy Azure ExpressRoute-ra), amelyet a helyszíni hely Azure-hálózatában kell beállítania.
 - **Önálló helyszíni fő célkiszolgáló**: a helyszíni fő célkiszolgáló kezeli a feladat-visszavételt. A fő célkiszolgálót alapértelmezés szerint a felügyeleti kiszolgálóra kell telepíteni, de nagyobb mértékű forgalom feladat-visszavétele esetén érdemes ebből a célból egy önálló helyszíni fő célkiszolgálót is létrehozni.

**Általános architektúra**

![Továbbfejlesztett](./media/site-recovery-components/arch-enhanced.png)

**Üzembe helyezési összetevők**

![Továbbfejlesztett](./media/site-recovery-components/arch-enhanced2.png)

**Feladat-visszavétel**

![Továbbfejlesztett feladat-visszavétel](./media/site-recovery-components/enhanced-failback.png)

* [További információk](site-recovery-vmware-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-failback-azure-to-vmware.md) a feladat-visszavételről az Azure Portal webhelyen.
* [További információk](site-recovery-failback-azure-to-vmware-classic.md) a feladat-visszavételről a klasszikus portálon.

## <a name="replicate-to-azure-hyper-v-vms-not-managed-by-vmm"></a>Replikálás az Azure-ba: a VMM által nem felügyelt Hyper-V virtuális gépek

A nem a System Center VMM-felhőkben kezelt Hyper-V virtuális gépeket a következőképpen replikálhatja az Azure-ba a Site Recoveryvel:

- **Az Azure Portal használatával**: Amikor üzembe helyezi a Site Recoveryt az Azure Portalon, a virtuális gépek a klasszikus tárolóba vagy a Resource Managernek adhatók át. [További információk](site-recovery-hyper-v-site-to-azure.md).
- **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. Ennél az üzembe helyezésnél a virtuális gépek feladatai csak az Azure-beli klasszikus tárolónak adhatók át, a Resource Manager-tárolónak nem. [További információk](site-recovery-hyper-v-site-to-azure-classic.md).

A két üzembe helyezés architektúrája hasonló, van azonban néhány eltérés:

* Ha az Azure Portalon végzi az üzembe helyezést, a replikáció Resource Manager-tárolóba végezhető el, az Azure virtuális gépek pedig Resource Manager-hálózatokkal csatlakoztathatók a feladatátvétel után.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.

A következőkre lesz szüksége:

* **Azure-fiók**: Szüksége van egy Microsoft Azure-fiókra.
* **Azure-tároló**: A replikált adatok tárolásához Azure-tárfiókra van szükség. Az Azure Portalon klasszikus fiókot vagy Resource Manager-tárfiókot is használhat. A klasszikus portálon csak klasszikus fiókok használhatók. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.
* **Azure-hálózat**: Szüksége van egy olyan Azure-hálózatra, amelyhez az Azure-beli virtuális gépek csatlakozni fognak, amikor létrejönnek a feladatátvétel után.
* **Hyper-V gazdagép**: Szükség van legalább egy Windows Server 2012 R2 Hyper-V gazdagépre. A Site Recovery üzembe helyezése során telepíteni fogja az Azure Site Recovery Provider és az Azure Recovery Services ügynököt a gazdagépen.
* **Hyper-V virtuális gépek**: A Hyper-V gazdakiszolgálón legalább egy virtuális gépnek kell lennie. A Site Recovery üzembe helyezése során a rendszer telepíti az Azure Site Recovery Provider és az Azure Recovery Services ügynököt a Hyper-V gazdagépen. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. Az ügynök a HTTPS-protokollal (a 443-as porton) végzi az adatok replikálását. A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.

**Általános architektúra**

![Replikálás Hyper-V-helyről az Azure-ba](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

* [További információk](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-to-azure-hyper-v-vms-managed-by-vmm"></a>Replikálás az Azure-ba: a VMM által felügyelt Hyper-V virtuális gépek
A VMM-felhőkben lévő Hyper-V virtuális gépeket a következőképpen replikálhatja az Azure-ba a Site Recovery használatával:

* **Az Azure Portal használatával**: Amikor üzembe helyezi a Site Recoveryt az Azure Portalon, a virtuális gépek a klasszikus tárolóba vagy a Resource Managernek adhatók át. [További információk](site-recovery-vmm-to-azure.md).
* **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. Ennél az üzembe helyezésnél a virtuális gépek feladatai csak az Azure-beli klasszikus tárolónak adhatók át, a Resource Manager-tárolónak nem. [További információk](site-recovery-vmm-to-azure-classic.md).

A két üzembe helyezés architektúrája hasonló, van azonban néhány eltérés:

* Ha az Azure Portalon végzi az üzembe helyezést, a replikáció Resource Manager-alapú tárolóba végezhető el, az Azure virtuális gépek pedig Resource Manager-hálózatokkal csatlakoztathatók a feladatátvétel után.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.

A következőkre lesz szüksége:

* **Azure-fiók**: Szüksége van egy Microsoft Azure-fiókra.
* **Azure-tároló**: A replikált adatok tárolásához Azure-tárfiókra van szükség. Az Azure Portalon klasszikus fiókot vagy Resource Manager-tárfiókot is használhat. A klasszikus portálon csak klasszikus fiókok használhatók. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.
* **Azure-hálózat**: Be kell állítania egy hálózatleképezést, hogy az Azure virtuális gépek a megfelelő hálózatokhoz csatlakozzanak a feladatátvétel utáni létrehozáskor.
* **VMM-kiszolgáló**: Szüksége van legalább egy, System Center 2012 R2 rendszeren futó helyszíni VMM-kiszolgálóra, amely egy vagy több magánfelhővel rendelkezik. Ha az Azure Portalon végzi az üzembe helyezést, a hálózatleképezés konfigurálásához logikai és virtuálisgép-hálózatokat is be kell állítani. Ez a klasszikus portálon nem kötelező.  Egy virtuálisgép-hálózatot össze kell kötnie egy felhőhöz társított logikai hálózattal.
* **Hyper-V gazdagép**: A VMM-felhőben legalább egy Windows Server 2012 R2 rendszerű Hyper-V gazdakiszolgálónak kell üzemelnie.
* **Hyper-V virtuális gépek**: A Hyper-V gazdakiszolgálón legalább egy virtuális gépnek kell lennie.

**Általános architektúra**

![VMM-replikáció Azure-ba](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

* [További információk](site-recovery-vmm-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-vmm-to-azure-classic.md) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replikálás másodlagos helyre: VMware virtuális gépek vagy fizikai kiszolgálók

A VMware-alapú virtuális gépek vagy fizikai kiszolgálók másodlagos helyre történő replikálásához töltse le az Azure Site Recovery-előfizetéshez járó InMage Scoutot. Ezt az Azure Portalról vagy a klasszikus Azure portálról lehet letölteni.

Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

A következőkre lesz szüksége:

**Azure-fiók**: ennél az üzembe helyezési forgatókönyvnél az InMage Scout segédprogramot kell használnia. Ennek beszerzéséhez Azure-előfizetésre lesz szüksége. A Site Recovery-tároló létrehozását követően az üzemelő példány beállításához töltse le az InMage Scout segédprogramot, és telepítse hozzá a legújabb frissítéseket.
**Folyamatkiszolgáló (elsődleges hely)**: állítsa be a folyamatkiszolgáló összetevőt az elsődleges helyen. Ez végzi az adatok gyorsítótárazását, tömörítését és optimalizálását. Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**VMware ESX/ESXi- és vCenter-kiszolgáló (elsődleges hely)**: ha VMware virtuális gépeknek szeretne védelmet nyújtani, szüksége lesz egy EXS/ESXi-hipervizorra, illetve a hipervizorokat felügyelő VMware vCenter-kiszolgálóra (utóbbi válaszható).

- **Virtuális gépek/fizikai kiszolgálók (elsődleges hely)**: a védelemmel ellátni kívánt VMware virtuális gépeken, illetve a windowsos/linuxos fizikai kiszolgálókon telepítenie kell a Unified Agent ügynököt. Ezenfelül telepítse a Unified Agent ügynököt a fő célkiszolgálóként üzemelő gépekre is. Ez az ügynök valósítja meg az összetevők közötti kommunikációt.
- **Konfigurációs kiszolgáló (másodlagos hely)**: a konfigurációs kiszolgáló az az összetevő, amelyet elsőként telepíteni kell a másodlagos helyre az üzemelő példánynak a felügyeleti weblapon vagy a vContinuum-konzolban való felügyelete, konfigurálása és figyelése céljából. Az üzemelő példány csupán egyetlen konfigurációs kiszolgálót tartalmaz. Ezt a Windows Server 2012 R2-es verziójával futó gépre kell telepíteni.
- **vContinuum-kiszolgáló (másodlagos hely)**: telepítse ugyanarra a helyre (a másodlagos helyre), mint a konfigurációs kiszolgálót. Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet. Alapértelmezett telepítés esetén a vContinuum-kiszolgáló az első fő célkiszolgáló, és telepítve van rá a Unified Agent ügynök.
- **Fő célkiszolgáló (másodlagos hely)**: a fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat. Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani. Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra.

**Általános architektúra**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)

## <a name="replicate-to-a-secondary-site-hyper-v-vms-managed-by-vmm"></a>Replikálás másodlagos helyre: a VMM által felügyelt Hyper-V virtuális gépek
A System Center VMM által felügyelt Hyper-V virtuális gépeket a következőképpen replikálhatja egy másodlagos adatközpontba a Site Recovery használatával:

* **Az Azure Portal használatával**: a Site Recoveryt az Azure Portalon helyezheti üzembe. [További információk](site-recovery-hyper-v-site-to-azure.md).
* **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. [További információk](site-recovery-hyper-v-site-to-azure-classic.md).

A két üzembe helyezés architektúrája hasonló, van azonban néhány eltérés:

- Ha az Azure Portalon végzi az üzembe helyezést, be kell állítania a hálózatleképezést. Ez a klasszikus portálon elhagyható.
- Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.
- Ha a klasszikus Azure Portalon végzi az üzembe helyezést, lehetősége van [tárolóleképezés](site-recovery-storage-mapping.md) beállítására.

A következőkre lesz szüksége:

* **Azure-fiók**: Szüksége van egy Microsoft Azure-fiókra.
* **VMM-kiszolgáló**: Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, egy a másodlagoson, és mindkettő tartalmazzon legalább egy VMM-magánfelhőt. A kiszolgálón a System Center 2012 SP1-es vagy újabb verziója fusson, és telepítse rá a legújabb frissítéseket is. A kiszolgálón álljon rendelkezésre internetkapcsolat. A felhőkön állítsa be a Hyper-V-kapacitásprofilt. Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A Provider és az Azure közötti kommunikáció biztonságos, titkosított csatornákon történik.
* **Hyper-V-kiszolgáló**: a Hyper-V-kiszolgálóknak az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük. A gazdakiszolgálókon a Windows Server 2012-es vagy újabb verziója fusson, és telepítse rájuk a legújabb frissítéseket is. A kiszolgálókon álljon rendelkezésre internetkapcsolat. A rendszer LAN vagy VPN hálózaton keresztül replikálja az adatokat az elsődleges és másodlagos Hyper-V gazdakiszolgálók között Kerberos vagy tanúsítványalapú hitelesítés használatával.  
* **Védett gépek**: a forrás Hyper-V gazdakiszolgálókon legalább egy védeni kívánt virtuális gépnek kell futnia.

**Általános architektúra**

![Két helyszíni hely közötti replikálás](./media/site-recovery-components/arch-onprem-onprem.png)

- [További információk](site-recovery-vmm-to-vmm.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
- [További információk](site-recovery-vmm-to-vmm-classic.md#before-you-start) a klasszikus Azure portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-to-a-secondary-site-with-san-replication-hyper-v-vms-managed-by-vmm"></a>Replikálás másodlagos helyre SAN-replikációval: a VMM által felügyelt Hyper-V virtuális gépek
A VMM-felhőkben felügyelt Hyper-V virtuális gépeket az Azure Portalon lehet egy másodlagos helyre replikálni SAN-replikálással. Ez a forgatókönyv jelenleg nem támogatott az új Azure Portalon.

Ha ezt a forgatókönyvet követi, a Site Recovery üzembe helyezése során telepítse az Azure Site Recovery Providert a VMM-kiszolgálókra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A rendszer SAN-szinkronreplikálás használatával replikálja az adatokat az elsődleges és másodlagos tárolótömbök között.

A következőkre lesz szüksége:

**Azure-fiók**: Rendelkeznie kell Azure-előfizetéssel.

* **SAN-tömb**: Egy [támogatott SAN-tömb](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), amelyet az elsődleges VMM-kiszolgáló felügyel. Ez a SAN közös hálózati infrastruktúrán osztozik egy másik, a másodlagos helyen található SAN-tömbbel.
* **VMM-kiszolgáló**: Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, egy a másodlagoson, és mindkettő tartalmazzon legalább egy VMM-magánfelhőt. A kiszolgálón a System Center 2012 SP1-es vagy újabb verziója fusson, és telepítse rá a legújabb frissítéseket is. A kiszolgálón álljon rendelkezésre internetkapcsolat. A felhőkön állítsa be a Hyper-V-kapacitásprofilt.
* **Hyper-V-kiszolgáló**: a Hyper-V-kiszolgálóknak az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük. A gazdakiszolgálókon a Windows Server 2012-es vagy újabb verziója fusson, és telepítse rájuk a legújabb frissítéseket is. A kiszolgálókon álljon rendelkezésre internetkapcsolat.
* **Védett gépek**: a forrás Hyper-V gazdakiszolgálókon legalább egy védeni kívánt virtuális gépnek kell futnia.

**SAN-replikáció architektúrája**

![SAN-replikáció](./media/site-recovery-components/arch-onprem-onprem-san.png)

- [Itt megismerheti](site-recovery-vmm-san.md#before-you-start) az üzembe helyezés feltételeit.

### <a name="on-premises"></a>Helyszíni követelmények
## <a name="hyper-v-protection-lifecycle"></a>A Hyper-V védelem életciklusa
Ez a munkafolyamat bemutatja a Hyper-V virtuális gépek védelmének, replikálásának és feladatátadásának lépéseit.

1. **Védelem engedélyezése**: állítsa be a Site Recovery-tárolót, adja meg a VMM-felhő vagy a Hyper-V-hely replikációs beállításait, majd engedélyezze a virtuális gépek védelmét. A szolgáltatás elindít egy **Védelem engedélyezése** nevű feladatot, amely a **Feladatok** lapon követhető nyomon. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, majd meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően létrehozza az Azure-ba végzett replikációt. A **Védelem engedélyezése** nevű feladat ezenfelül meghívja a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódust is, amely teljes körű virtuálisgép-replikációt indít el.
2. **Kezdeti replikáció:** Egy pillanatkép készül a virtuális gépről, majd a rendszer egyenként replikálja a virtuális merevlemezeket, amíg az összes át nem lesz másolva az Azure-ba vagy a másodlagos adatközpontba. Az ehhez a művelethez szükséges időtartam a virtuális gép méretétől, a hálózati sávszélességtől, illetve a kezdeti replikáció módjától függ. Ha módosítják a lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V-replikációkövető nyomon követi a módosításokat replikálási naplók (.hrl) formájában, amelyek ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy .hrl-fájl, amelyet a rendszer továbbít a másodlagos tárterületre. A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét, valamint szinkronizálja és egyesíti a naplózott lemezmódosításokat.
3. **Védelem véglegesítése**: A kezdeti replikáció befejezése után a **Védelem véglegesítése** feladat konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez. Azure-ba történő replikálás esetén előfordulhat, hogy módosítania kell a virtuális gép beállításait, hogy az készen álljon a feladatátvételre. Ezen a ponton érdemes lehet feladatátvételi tesztet futtatni, amellyel ellenőrizheti, hogy minden megfelelően működik-e.
4. **Replikáció**: a kezdeti replikációt követően a replikációs beállításoknak megfelelően elindul a változások szinkronizálása.
   - **Sikertelen replikáció**: ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer újraszinkronizálást végez. Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra. Az újraszinkronizálás kiszámítja a forrás és a cél virtuális gépek ellenőrzőösszegeit, és ezek alapján csak a változásokat továbbítja, így segít csökkenti az adatmennyiséget. Az újraszinkronizálás befejezését követően folytatódik a változásreplikálás. Alapértelmezés szerint a rendszer automatikusan munkaidőn kívüli időpontra ütemezi az újraszinkronizálást, de manuálisan is elvégezhető a virtuális gép újraszinkronizálása.
   - **Replikációs hiba**: ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Ha nem helyrehozható hiba, például hitelesítési vagy engedélyezési hiba történik, vagy egy replikagép érvénytelen állapotban van, a rendszer nem próbálkozik újra. Ha helyrehozható hiba, például hálózati hiba vagy alacsony lemezterülettel/memóriával kapcsolatos hiba lép fel, a rendszer újrapróbálkozik, és folyamatosan növeli a próbálkozások közötti időintervallumot (1, 2, 4, 8, 10, majd 30 perc).
5. **Tervezett/nem tervezett feladatátvételek**: igény szerint futtathat tervezett/nem tervezett feladatátvételeket. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. A replikaként létrehozott virtuális gépeket a rendszer függőben lévő állapotba helyezi. A feladatátvétel befejezéséhez véglegesíteni kell ezeket, kivéve, ha SAN-replikálást végez, amely automatikusan elvégzi a véglegesítést. Ha felállt az elsődleges hely, a rendszer elvégzi a feladatátvételt. Ha az Azure-ba végzett replikálást, a fordított replikációra automatikusan sor kerül. Ellenkező esetben indítsa el manuálisan a fordított replikációt.

![munkafolyamat](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Következő lépések

[Felkészülés az üzembe helyezésre](site-recovery-best-practices.md)



<!--HONumber=Nov16_HO4-->


