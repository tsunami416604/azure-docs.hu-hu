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
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 94250becb238adad7983d4b45939eb988411fca3


---
# <a name="how-does-azure-site-recovery-work"></a>Hogy működik az Azure Site Recovery?
A cikkből megismerheti az Azure Site Recovery szolgáltatás mögöttes architektúráját, valamint a szolgáltatás működéséért felelős összetevőket.

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="overview"></a>Áttekintés
A legtöbb vállalatnál szükség van üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak az alkalmazások, a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. Fontos, hogy a BCDR stratégia gondoskodjon a vállalati adatok biztonságáról és helyreállíthatóságáról, és lehetővé tegye, hogy a számítási feladatok még vészhelyzet esetén se álljanak le.

Az Azure Site Recovery szolgáltatása a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával segít a stratégia kidolgozásában. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. További információ: [Mi a Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery az Azure Portalon
Az Azure két különböző [üzemi modellel](../resource-manager-deployment-model.md) rendelkezik az erőforrások létrehozásához és használatához: az Azure Resource Manager-modellel és a klasszikus szolgáltatáskezelési modellel. Az Azure-ban két különböző portál érhető el: a [klasszikus Azure portál](https://manage.windowsazure.com/), amely a klasszikus üzembe helyezési modellt, és az [Azure Portal](https://portal.azure.com), amely mindkét modellt támogatja.

A Site Recovery a klasszikus portálon és az Azure Portalon egyaránt elérhető. A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatásfelügyeleti modellel támogatható. Az Azure Portalon a klasszikus modell vagy az erőforrásmodell üzembe helyezései támogathatók. [Itt olvashat bővebben](site-recovery-overview.md#site-recovery-in-the-azure-portal) az Azure Portal használatával végzett üzembe helyezésről.

A cikk információi a klasszikus és az Azure Portalon üzemelő példányokra egyaránt vonatkoznak. Az esetleges eltéréseket jelezzük.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek
A Site Recovery számos forgatókönyv esetében használható replikációra:

* **VMware virtuális gépek replikálása**: a helyszíni VMware virtuális gépeket az Azure-ba vagy egy másodlagos adatközpontba replikálhatja.
* * **Fizikai gépek replikálása**: a windowsos vagy linuxos fizikai gépeket az Azure-ba vagy egy másodlagos adatközpontba replikálhatja. A fizikai gépek replikálásának folyamata szinte teljesen megegyezik a VMware virtuális gépek replikálásának folyamatával.
* **Hyper-V virtuális gépek replikálása (VMM nélkül)**: a nem a VMM által felügyelt Hyper-V virtuális gépeket az Azure-ba replikálhatja.
* **System Center VMM-felhőkben működő Hyper-V virtuális gépek replikálása**: a helyszíni Hyper-V-gazdakiszolgálókon működő Hyper-V virtuális gépeket az Azure-ba vagy egy másodlagos adatközpontba replikálhatja. A replikálást végrehajthatja standard Hyper-V-replika vagy SAN-replikáció használatával.
* **Virtuális gépek**: a Site Recovery az [Azure-beli IaaS típusú virtuális gépek](site-recovery-migrate-azure-to-azure.md) régiók közötti áttelepítésére, illetve [AWS Windows-példányok](site-recovery-migrate-aws-to-azure.md) Azure-beli IaaS típusú virtuális gépekre való áttelepítésére is használható. A szolgáltatás jelenleg csupán az áttelepítést támogatja, ami azt jelenti, hogy a virtuális gépeken elvégezhető a feladatátvétel, de a feladat-visszavétel nem.

A Site Recovery a fent megnevezett típusú virtuális gépeken és fizikai kiszolgálókon futó legtöbb alkalmazást is képes replikálni. A támogatott alkalmazások teljes körű összefoglalóját megtalálja a [Milyen számítási feladatokat tud védeni az Azure Site Recovery?](site-recovery-workload.md) című cikkben.

## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Replikálás az Azure-ba: VMware virtuális gépek és fizikai Windows-/Linux-kiszolgálók
A VMware virtuális gépek többféleképpen is replikálhatók a Site Recovery használatával.

* **Az Azure Portal használatával**: ha a Site Recovery üzembe helyezését az Azure Portal webhelyen hajtja végre, a virtuális gépek feladatait a klasszikus szolgáltatásfelügyeleti tárnak vagy a Resource Managernek adhatja át. A VMware virtuális gépeknek az Azure Portalon történő replikálása számos előnnyel jár, ilyen például a klasszikus tárba vagy a Resource Manager-tárba végzett replikálás lehetősége az Azure-ban. [További információk](site-recovery-vmware-to-azure.md).
* **A klasszikus portál használatával**: a Site Recovery a klasszikus portálon egy továbbfejlesztett architektúra alkalmazásával helyezhető üzembe. Ezt kell használni a klasszikus portálon működő összes új üzembe helyezésnél. Ennél az üzembe helyezésnél a virtuális gépek feladatai csak az Azure-beli klasszikus tárnak adhatók át, a Resource Manager-tárnak nem. [További információk](site-recovery-vmware-to-azure-classic.md). Ezenkívül rendelkezésre áll egy [korábbi architektúra](site-recovery-vmware-to-azure-classic-legacy.md) is a VMware-replikáció beállításához a klasszikus portálon. Ez nem alkalmazható az új üzembe helyezésekhez.  Ha már végrehajtotta az üzembe helyezést a korábbi architektúra használatával, [olvassa el, hogyan telepítheti át](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) a továbbfejlesztett architektúrára.

A Site Recovery üzembe helyezési architekturális követelményei a VMware virtuális gépeknek/fizikai kiszolgálóknak az Azure Portal webhelyen vagy a (továbbfejlesztett) klasszikus Azure portálon végzett replikálására vonatkozóan hasonlóak, van azonban néhány eltérés:

* Ha az Azure Portal webhelyen végzi az üzembe helyezést, a replikálást végrehajthatja a Resource Manager-alapú tárba, és használhatja a Resource Manager-hálózatokat az Azure virtuális gépek csatlakoztatásához a feladatátvételt követően.
* Ha az Azure Portalon végzi az üzembe helyezést, az LRS- és a GRS-tár egyaránt támogatott. A klasszikus portálon a GRS-t kell használni.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.

A következőkre lesz szüksége:

* **Azure-fiók**: szüksége lesz egy Microsoft Azure-fiókra.
* **Azure Storage**: a replikált adatok tárolásához Azure Storage-tárfiókra lesz szüksége. Használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A fiók lehet LRS vagy GRS, amikor az Azure Portal webhelyen végzi az üzembe helyezést. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.
* **Azure-hálózat**: szüksége lesz egy Azure virtuális hálózatra, amelyhez a létrehozott Azure virtuális gépek csatlakoznak, amikor a rendszer létrehozza azokat feladatátvétel esetén. Az Azure Portalon ezek lehetnek a klasszikus szolgáltatásfelügyeleti modellben vagy a Resource Manager-modellben létrehozott hálózatok.
* **Helyszíni konfigurációs kiszolgáló**: szüksége lesz egy helyszíni Windows Server 2012 R2 rendszerű gépre, amely a konfigurációs kiszolgálót és a Site Recovery egyéb összetevőit futtatja. Ha VMware virtuális gépeket replikál, ennek egy magas rendelkezésre állású VMware virtuális gépnek kell lennie. Ha fizikai kiszolgálókat kíván replikálni, a gép lehet fizikai. A Site Recovery következő összetevői lesznek telepítve a gépre:
  * **Konfigurációs kiszolgáló**: a helyszíni környezet és az Azure közötti kommunikáció koordinálását, valamint az adatreplikáció és -helyreállítás kezelését végzi.
  * **Folyamatkiszolgáló**: replikációs átjáróként üzemel. Ez az összetevő fogadja a védett forrásgépek adatait, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, majd továbbítja az adatokat az Azure Storage-nak. Ezenfelül kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését. Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.
  * **Fő célkiszolgáló**: az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat.
* **Replikálandó VMware virtuális gépek vagy fizikai kiszolgálók**: az Azure-ba replikálni kívánt gépeken telepíteni kell a mobilitási szolgáltatás összetevőt. A szolgáltatás észleli a gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. Az összetevő manuálisan is telepíthető, de azt is beállíthatja, hogy a folyamatkiszolgáló automatikusan, leküldéses módszerrel telepítse, amikor engedélyezi az adott gép replikálását.
* **vSPhere-gazdagépek/vCenter-kiszolgáló**: szüksége lesz egy vagy több vSPhere-gazdakiszolgálóra, amelyeken a VMware virtuális gépek futnak. Javasoljuk, hogy a gazdagépek kezelésére helyezzen üzembe egy vCenter-kiszolgálót is.
* **Feladat-visszavétel**: a következőkre lesz szüksége:
  * **A fizikai gépek közötti feladat-visszavétel nem támogatott**: ez azt jelenti, hogy ha a fizikai kiszolgálók feladatait átadja az Azure-nak, akkor a feladat-visszavétel célja VMware virtuális gép lehet. Fizikai kiszolgáló nem használható a feladat-visszavételhez. A feladat-visszavételhez egy Azure virtuális gépre lesz szüksége, és ha a konfigurációs kiszolgálót nem VMware virtuális gépként helyezte üzembe, akkor egy különálló fő célkiszolgálót kell beállítania VMware virtuális gépként. Ez azért szükséges, mert a fő célkiszolgáló együttműködik és kapcsolatba lép a VMware-tárral a lemezeknek a VMware virtuális gépre történő visszaállításához.
  * * **Ideiglenes folyamatkiszolgáló az Azure-ban**: ha feladatátvételt követően szeretné visszaadni a feladatokat az Azure-ból, be kell állítania egy folyamatkiszolgálóként üzemelő Azure virtuális gépet, amely kezeli az Azure-ból való replikációt. Ez a virtuális gép a feladatok visszaadását követően törölhető.
  * **VPN-kapcsolat**: a feladat-visszavételhez szüksége lesz egy VPN-kapcsolatra (vagy Azure ExpressRoute-ra), amelyet a helyszíni hely Azure-hálózatában kell beállítania.
  * **Önálló helyszíni fő célkiszolgáló**: a helyszíni fő célkiszolgáló kezeli a feladat-visszavételt. A fő célkiszolgálót alapértelmezés szerint a felügyeleti kiszolgálóra kell telepíteni, de nagyobb mértékű forgalom feladat-visszavétele esetén érdemes ebből a célból egy önálló helyszíni fő célkiszolgálót is létrehozni.

**Általános architektúra**

![Továbbfejlesztett](./media/site-recovery-components/arch-enhanced.png)

**Üzembe helyezési összetevők**

![Továbbfejlesztett](./media/site-recovery-components/arch-enhanced2.png)

**Feladat-visszavétel**

![Továbbfejlesztett feladat-visszavétel](./media/site-recovery-components/enhanced-failback.png)

* [További információk](site-recovery-vmware-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) a továbbfejlesztett üzembe helyezés követelményeiről a klasszikus portál esetében.
* [További információk](site-recovery-failback-azure-to-vmware.md) a feladat-visszavételről az Azure Portal webhelyen.
* [További információk](site-recovery-failback-azure-to-vmware-classic.md) a feladat-visszavételről a klasszikus portálon.

## <a name="replicate-to-azure-hyperv-vms-not-managed-by-vmm"></a>Replikálás az Azure-ba: a VMM által nem felügyelt Hyper-V virtuális gépek
A System Center VMM által nem felügyelt Hyper-V virtuális gépeket a következőképpen replikálhatja az Azure-ba a Site Recovery használatával:

* **Az Azure Portal használatával**: ha a Site Recovery üzembe helyezését az Azure Portalon hajtja végre, a virtuális gépek feladatait a klasszikus tárnak vagy a Resource Managernek adhatja át. [További információk](site-recovery-hyper-v-site-to-azure.md).
* **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. Ennél az üzembe helyezésnél a virtuális gépek feladatai csak az Azure-beli klasszikus tárnak adhatók át, a Resource Manager-tárnak nem. [További információk](site-recovery-hyper-v-site-to-azure-classic.md).

A két üzembe helyezés architektúrája hasonló, van azonban néhány eltérés:

* Ha az Azure Portalon végzi az üzembe helyezést, a replikálást végrehajthatja a Resource Manager-tárba, és használhatja a Resource Manager-hálózatokat az Azure virtuális gépek csatlakoztatásához a feladatátvételt követően.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.

A következőkre lesz szüksége:

* **Azure-fiók**: szüksége lesz egy Microsoft Azure-fiókra.
* **Azure Storage**: a replikált adatok tárolásához Azure Storage-tárfiókra lesz szüksége. Az Azure Portalon használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A klasszikus portálon csak klasszikus fiók használható. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.
* **Azure-hálózat**: szüksége lesz egy Azure-hálózatra, amelyhez a létrehozott Azure virtuális gépek csatlakoznak, amikor a rendszer létrehozza azokat a feladatátvételt követően.
* **Hyper-V-gazdagép**: szüksége lesz egy vagy több Windows Server 2012 R2 rendszerű Hyper-V-gazdakiszolgálóra. A Site Recovery üzembe helyezése során telepíteni fogja az Azure Site Recovery Provider és az Azure Recovery Services ügynököt a gazdagépen.
* **Hyper-V virtuális gépek**: egy vagy több virtuális gépre lesz szüksége a Hyper-V-gazdakiszolgálón. Az Azure Site Recovery Provider és az Azure Recovery Services ügynök a Hyper-V-kiszolgálón a Site Recovery üzembe helyezése során. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. Az ügynök a HTTPS-protokollal (a 443-as porton) végzi az adatok replikálását. A Provider és az Agent kommunikációja biztonságos, titkosított csatornákon történik. Ezenfelül az Azure-tárfiókba replikált adatok is titkosítást kapnak.

**Általános architektúra**

![Replikálás Hyper-V-helyről az Azure-ba](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

* [További információk](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-to-azure-hyperv-vms-managed-by-vmm"></a>Replikálás az Azure-ba: a VMM által felügyelt Hyper-V virtuális gépek
A VMM-felhőkben lévő Hyper-V virtuális gépeket a következőképpen replikálhatja az Azure-ba a Site Recovery használatával:

* **Az Azure Portal használatával**: ha a Site Recovery üzembe helyezését az Azure Portalon hajtja végre, a virtuális gépek feladatait a klasszikus tárnak vagy a Resource Managernek adhatja át. [További információk](site-recovery-vmm-to-azure.md).
* **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. Ennél az üzembe helyezésnél a virtuális gépek feladatai csak az Azure-beli klasszikus tárnak adhatók át, a Resource Manager-tárnak nem. [További információk](site-recovery-vmm-to-azure-classic.md).

A két üzembe helyezés architektúrája hasonló, van azonban néhány eltérés:

* Ha az Azure Portal webhelyen végzi az üzembe helyezést, a replikálást végrehajthatja a Resource Manager-alapú tárba, és használhatja a Resource Manager-hálózatokat az Azure virtuális gépek csatlakoztatásához a feladatátvételt követően.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.

A következőkre lesz szüksége:

* **Azure-fiók**: szüksége lesz egy Microsoft Azure-fiókra.
* **Azure Storage**: a replikált adatok tárolásához Azure Storage-tárfiókra lesz szüksége. Az Azure Portalon használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A klasszikus portálon csak klasszikus fiók használható. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.
* **Azure-hálózat**: be kell állítania a hálózatleképzést ahhoz, hogy az Azure virtuális gépek a megfelelő hálózatokhoz csatlakozzanak, amikor a rendszer létrehozza azokat a feladatátvételt követően.
* **VMM-kiszolgáló**: szüksége lesz a System Center 2012 R2 rendszeren futó egy vagy több helyszíni VMM-kiszolgálóra, és be kell állítania egy vagy több magánfelhőt. Ha az Azure Portalon végzi az üzembe helyezést, logikai és virtuálisgép-hálózatokat kell beállítania, hogy konfigurálhassa a hálózatleképezést. Ez a klasszikus portálon nem kötelező.  Egy virtuálisgép-hálózatot össze kell kötnie egy felhőhöz társított logikai hálózattal.
* **Hyper-V-gazdagép**: a VMM-felhőben szüksége lesz egy vagy több Windows Server 2012 R2 rendszerű Hyper-V gazdakiszolgálóra.
* **Hyper-V virtuális gépek**: egy vagy több virtuális gépre lesz szüksége a Hyper-V-gazdakiszolgálón.

**Általános architektúra**

![VMM-replikáció Azure-ba](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

* [További információk](site-recovery-vmm-to-azure.md#azure-requirements) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-vmm-to-azure-classic.md#before-you-start) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replikálás másodlagos helyre: VMware virtuális gépek vagy fizikai kiszolgálók
Ha VMware virtuális gépeket vagy fizikai kiszolgálókat szeretne egy másodlagos helyre replikálni, töltse le az Azure Site Recovery-előfizetés részét képező InMage Scout segédprogramot. A segédprogram az Azure Portalról vagy a klasszikus Azure portálról tölthető le.

Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt. A kezdeti replikációt követően a gépeken futó ügynök továbbítja a változásreplikálási adatokat a folyamatkiszolgálónak. A folyamatkiszolgáló optimalizálást követően átadja az adatokat a másodlagos helyen működő fő célkiszolgálónak. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

A következőkre lesz szüksége:

**Azure-fiók**: ennél az üzembe helyezési forgatókönyvnél az InMage Scout segédprogramot kell használnia. Ennek beszerzéséhez Azure-előfizetésre lesz szüksége. A Site Recovery-tároló létrehozását követően az üzemelő példány beállításához töltse le az InMage Scout segédprogramot, és telepítse hozzá a legújabb frissítéseket.
**Folyamatkiszolgáló (elsődleges hely)**: állítsa be a folyamatkiszolgáló összetevőt az elsődleges helyen. Ez végzi az adatok gyorsítótárazását, tömörítését és optimalizálását. Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.
**VMware ESX/ESXi- és vCenter-kiszolgáló (elsődleges hely)**: ha VMware virtuális gépeknek szeretne védelmet nyújtani, szüksége lesz egy EXS/ESXi-hipervizorra, illetve a hipervizorokat felügyelő VMware vCenter-kiszolgálóra (utóbbi válaszható).

* **Virtuális gépek/fizikai kiszolgálók (elsődleges hely)**: a védelemmel ellátni kívánt VMware virtuális gépeken, illetve a windowsos/linuxos fizikai kiszolgálókon telepítenie kell a Unified Agent ügynököt. Ezenfelül telepítse a Unified Agent ügynököt a fő célkiszolgálóként üzemelő gépekre is. Ez az ügynök valósítja meg az összetevők közötti kommunikációt.
* * **Konfigurációs kiszolgáló (másodlagos hely)**: a konfigurációs kiszolgáló az az összetevő, amelyet elsőként telepíteni kell a másodlagos helyre az üzemelő példánynak a felügyeleti weblapon vagy a vContinuum-konzolban való felügyelete, konfigurálása és figyelése céljából. Az üzemelő példány csupán egyetlen konfigurációs kiszolgálót tartalmaz. Ezt a Windows Server 2012 R2-es verziójával futó gépre kell telepíteni.
* **vContinuum-kiszolgáló (másodlagos hely)**: telepítse ugyanarra a helyre (a másodlagos helyre), mint a konfigurációs kiszolgálót. Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet. Alapértelmezés szerint a vContinuum-kiszolgáló az első fő célkiszolgáló. Erre is telepíteni kell a Unified Agent ügynököt.
* **Fő célkiszolgáló (másodlagos hely)**: a fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat. Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani. Ha szeretne feladat-visszavételt végrehajtani az elsődleges helyre, ott is üzemelnie kell egy fő célkiszolgálónak.

**Általános architektúra**

![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)

## <a name="replicate-to-a-secondary-site-hyperv-vms-managed-by-vmm"></a>Replikálás másodlagos helyre: a VMM által felügyelt Hyper-V virtuális gépek
A System Center VMM által felügyelt Hyper-V virtuális gépeket a következőképpen replikálhatja egy másodlagos adatközpontba a Site Recovery használatával:

* **Az Azure Portal használatával**: amikor a Site Recovery üzembe helyezését az Azure Portalon végzi. [További információk](site-recovery-hyper-v-site-to-azure.md).
* **A hagyományos portál használatával**: a Site Recovery üzembe helyezését a klasszikus portálon hajthatja végre. [További információk](site-recovery-hyper-v-site-to-azure-classic.md).

A két üzembe helyezés architektúrája hasonló, van azonban néhány eltérés:

* Ha az Azure Portalon végzi az üzembe helyezést, be kell állítania a hálózatleképezést. Ez a klasszikus portálon nem kötelező.
* Az üzembe helyezési folyamat egyszerűbb és nagyobb mértékben felhasználóbarát az Azure Portal webhelyen.
* * Ha a klasszikus Azure portálon végzi az üzembe helyezést, [tárolóleképezés](site-recovery-storage-mapping.md) is elérhető.

A következőkre lesz szüksége:

* **Azure-fiók**: szüksége lesz egy Microsoft Azure-fiókra.
* **VMM-kiszolgáló**: javasoljuk, hogy mind az elsődleges helyen, mind a másodlagos helyen hozzon létre egy VMM-kiszolgálót, és ezek tartalmazzanak legalább egy VMM-magánfelhőt. A kiszolgálón a System Center 2012 SP1-es vagy újabb verziója fusson, és telepítse rá a legújabb frissítéseket is. A kiszolgálón álljon rendelkezésre internetkapcsolat. A felhőkön állítsa be a Hyper-V-kapacitásprofilt. Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A Provider és az Azure közötti kommunikáció biztonságos, titkosított csatornákon történik.
* **Hyper-V-kiszolgáló**: a Hyper-V-kiszolgálóknak az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük. A gazdakiszolgálókon a Windows Server 2012-es vagy újabb verziója fusson, és telepítse rájuk a legújabb frissítéseket is. A kiszolgálókon álljon rendelkezésre internetkapcsolat. Az adatoknak az elsődleges és másodlagos Hyper-V gazdakiszolgálók közötti replikálását a rendszer LAN-on vagy VPN-en keresztül, Kerberos- vagy tanúsítványalapú hitelesítés használata mellett végzi.  
* **Védett gépek**: a forrás Hyper-V gazdakiszolgálókon legalább egy védeni kívánt virtuális gépnek kell futnia.

**Általános architektúra**

![Két helyszíni hely közötti replikálás](./media/site-recovery-components/arch-onprem-onprem.png)

* [További információk](site-recovery-vmm-to-vmm.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* * [További információk](site-recovery-vmm-to-vmm-classic.md#before-you-start) a klasszikus Azure portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-to-a-secondary-site-with-san-replication-hyperv-vms-managed-by-vmm"></a>Replikálás másodlagos helyre SAN-replikációval: a VMM által felügyelt Hyper-V virtuális gépek
A VMM-felhőkben felügyelt Hyper-V virtuális gépeket a klasszikus Azure portál használatával replikálhatja a SAN-replikációt alkalmazó másodlagos helyre. Ez a forgatókönyv jelenleg nem támogatott az új Azure Portalon.

E forgatókönyv esetében a Site Recovery üzembe helyezése során telepítse az Azure Site Recovery Providert a VMM-kiszolgálókra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A rendszer SAN-szinkronreplikálás használatával replikálja az adatokat az elsődleges és másodlagos tárolótömbök között.

A következőkre lesz szüksége:

**Azure-fiók**: szüksége lesz egy Azure-előfizetésre.

* **SAN-tömb**: rendelkeznie kell egy, a VMM-kiszolgáló által felügyelt, [támogatott SAN-tömbbel](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx). Ez a SAN közös hálózati infrastruktúrán osztozik egy másik, a másodlagos helyen található SAN-tömbbel.
* **VMM-kiszolgáló**: javasoljuk, hogy mind az elsődleges helyen, mind a másodlagos helyen hozzon létre egy VMM-kiszolgálót, és ezek tartalmazzanak legalább egy VMM-magánfelhőt. A kiszolgálón a System Center 2012 SP1-es vagy újabb verziója fusson, és telepítse rá a legújabb frissítéseket is. A kiszolgálón álljon rendelkezésre internetkapcsolat. A felhőkön állítsa be a Hyper-V-kapacitásprofilt.
* **Hyper-V-kiszolgáló**: a Hyper-V-kiszolgálóknak az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük. A gazdakiszolgálókon a Windows Server 2012-es vagy újabb verziója fusson, és telepítse rájuk a legújabb frissítéseket is. A kiszolgálókon álljon rendelkezésre internetkapcsolat.
* **Védett gépek**: a forrás Hyper-V gazdakiszolgálókon legalább egy védeni kívánt virtuális gépnek kell futnia.

**SAN-replikáció architektúrája**

![SAN-replikáció](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Itt megismerheti](site-recovery-vmm-san.md#before-you-start) az üzembe helyezés feltételeit.

### <a name="onpremises"></a>Helyszíni követelmények
## <a name="hyperv-protection-lifecycle"></a>A Hyper-V védelem életciklusa
Ez a munkafolyamat bemutatja a Hyper-V virtuális gépek védelmének, replikálásának és feladatátadásának lépéseit.

1. **Védelem engedélyezése**: állítsa be a Site Recovery-tárolót, adja meg a VMM-felhő vagy a Hyper-V-hely replikációs beállításait, majd engedélyezze a virtuális gépek védelmét. A szolgáltatás elindít egy **Védelem engedélyezése** nevű feladatot, amely a **Feladatok** lapon követhető nyomon. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, majd meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően létrehozza az Azure-ba történő replikációt. A **Védelem engedélyezése** nevű feladat ezenfelül meghívja a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódust is, amely teljes körű virtuálisgép-replikációt indít el.
2. **Kezdeti replikáció**: a rendszer pillanatképet készít a virtuális gépről, majd egyesével elvégzi a virtuális merevlemezek replikálását, és átmásolja ezeket az Azure-ba vagy a másodlagos adatközpontba. Az ehhez a művelethez szükséges időtartam a virtuális gép méretétől, a hálózati sávszélességtől, illetve a kezdeti replikáció módjától függ. Ha a lemezen változások történnek, amíg a kezdeti replikáció folyamatban van, a Hyper-V Replica Replication Tracker eszköz Hyper-V replikálási naplók (.hrl-fájlok) formájában jelentést készít a változásokról. A naplók ugyanabba a mappába kerülnek, mint a lemezek. Minden lemezhez tartozik egy .hrl-fájl, amelyet a rendszer továbbít a másodlagos tárterületre. Ne feledje, hogy a pillanatkép- és a naplófájlok a kezdeti replikáció futása közben is lemezerőforrásokat használnak. A kezdeti replikáció befejezését követően a rendszer törli a virtuális gépről készült pillanatképet, és szinkronizálja, illetve egyesíti a naplóban rögzített változásokat.
3. **Védelem véglegesítése**: A kezdeti replikáció befejezését követően elindul a **Védelem véglegesítése** feladat, amely beállítja a hálózati és a replikációt követő egyéb beállításokat, így védelmet nyújt a virtuális gépnek. Ha az Azure-ba végez replikálást, előfordulhat, hogy módosítania kell a virtuális gép beállításait, hogy az feladatátvételt tudjon végezni. Ezen a ponton érdemes lehet feladatátvételi tesztet futtatni, amellyel ellenőrizheti, hogy minden megfelelően működik-e.
4. **Replikáció**: a kezdeti replikációt követően a replikációs beállításoknak megfelelően elindul a változások szinkronizálása.
   * **Sikertelen replikáció**: ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer újraszinkronizálást végez. Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra. Az újraszinkronizálás kiszámítja a forrás és a cél virtuális gépek ellenőrzőösszegeit, és ezek alapján csak a változásokat továbbítja, így segít csökkenti az adatmennyiséget. Az újraszinkronizálás befejezését követően folytatódik a változásreplikálás. Alapértelmezés szerint a rendszer automatikusan munkaidőn kívüli időpontra ütemezi az újraszinkronizálást, de manuálisan is elvégezhető a virtuális gép újraszinkronizálása.
   * **Replikációs hiba**: ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Ha helyrehozhatatlan hiba, például hitelesítési vagy engedélyezési hiba lép fel, illetve, ha a replikagép érvénytelen állapotban van, a rendszer nem próbálkozik újra. Ha helyrehozható hiba, például hálózati hiba vagy alacsony lemezterülettel/memóriával kapcsolatos hiba lép fel, a rendszer újrapróbálkozik, és folyamatosan növeli a próbálkozások közötti időintervallumot (1, 2, 4, 8, 10, majd 30 perc).
5. **Tervezett/nem tervezett feladatátvételek**: igény szerint futtathat tervezett/nem tervezett feladatátvételeket. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. A replikaként létrehozott virtuális gépeket a rendszer függőben lévő állapotba helyezi. A feladatátvétel befejezéséhez véglegesíteni kell ezeket, kivéve, ha SAN-replikálást végez, amely automatikusan elvégzi a véglegesítést. Ha felállt az elsődleges hely, a rendszer elvégzi a feladatátvételt. Ha az Azure-ba végzett replikálást, a fordított replikáció automatikusan megtörténik. Ellenkező esetben indítsa el manuálisan a fordított replikációt.

![munkafolyamat](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Következő lépések
[Felkészülés az üzembe helyezésre](site-recovery-best-practices.md)




<!--HONumber=Nov16_HO2-->


