---
title: Hogy működik a Site Recovery? | Microsoft Docs
description: Ebben a cikkben a Site Recovery architektúráját mutatjuk be.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: raynew

---
# <a name="how-does-azure-site-recovery-work?"></a>Hogy működik az Azure Site Recovery?
Ebből a cikkből megismerheti az Azure Site Recovery felépítését, illetve az azt működtető összetevőket.

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="overview"></a>Áttekintés
Szükség van egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak a számítási feladatok és az adatok üzemben maradni a tervezett és nem tervezett leállások során, illetve, hogy hogyan lehet minél gyorsabban visszaállni a normál működésre. 

Az Azure Site Recovery segít a BCDR-stratégia kidolgozásában. Ez a szolgáltatás a helyszíni fizikai kiszolgálóknak és virtuális gépeknek az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálását koordinálja. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, a rendszer átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak le. Ha az elsődleges helyen helyreáll a normál működés, a rendszer visszaadja a feladatokat. A téma áttekintése: [Mi a Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery az Azure Portalon
Az Azure két különböző [üzemi modellel](../resource-manager-deployment-model.md) rendelkezik az erőforrások létrehozásához és használatához: az Azure Resource Manager-modellel és a klasszikus szolgáltatáskezelési modellel. Az Azure-ban két különböző portál érhető el: a [klasszikus Azure portál](https://manage.windowsazure.com/), amely a klasszikus üzembe helyezési modellt, és az [Azure Portal](https://portal.azure.com), amely mindkét modellt támogatja.

* A Site Recovery a klasszikus portálon és az Azure Portalon egyaránt elérhető.
* A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatáskezelési modellel támogatható.
* Az Azure Portalon a klasszikus modellen és az erőforrásmodellen alapuló üzemelő példányok is támogatottak. 

A cikk információi a klasszikus és az Azure Portalon üzemelő példányokra egyaránt vonatkoznak. A különbségeket az alábbi táblázat foglalja össze.

| **Replikálás** | **Azure Portal** | **Klasszikus portál** |
| --- | --- | --- |
| **VMware virtuális gép replikálása az Azure-ba** |Egyszerűsített üzembe helyezési folyamat.<br/><br/> Virtuális gépek feladatainak átadása klasszikus vagy Resource Manager-alapú tárolónak.<br/><br/> Replikálás klasszikus vagy Resource Manager-alapú tárolóba.<br/><br/> A feladatátvételt követően az Azure virtuális gépek csatlakoztatásához használjon klasszikus vagy Resource Manager-alapú hálózatokat.<br/><br/> Használjon LRS- vagy GRS-tárolót. |Kizárólag klasszikus tárolóba történő feladatátvétel.<br/><br/> A feladatátvételt követően a virtuális gépek csatlakoztatásához kizárólag klasszikus hálózatokat használjon.<br/><br/> Használjon GRS-tárolót. |
| **Hyper-V virtuális gép replikációja (VMM nélkül) az Azure-ba** |Egyszerűsített üzembe helyezési folyamat.<br/><br/> Virtuális gépek feladatainak átadása klasszikus vagy Resource Manager-alapú tárolónak.<br/><br/> Replikálás klasszikus vagy Resource Manager-alapú tárolóba.<br/><br/> A feladatátvételt követően az Azure virtuális gépek csatlakoztatásához használjon klasszikus vagy Resource Manager-alapú hálózatokat. | |
| **Hyper-V virtuális gép replikációja (VMM-mel) az Azure-ba** |Egyszerűsített üzembe helyezési folyamat.<br/><br/> Virtuális gépek feladatainak átadása klasszikus vagy Resource Manager-alapú tárolónak.<br/><br/> Replikálás klasszikus vagy Resource Manager-alapú tárolóba.<br/><br/> A feladatátvételt követően az Azure virtuális gépek csatlakoztatásához használjon klasszikus vagy Resource Manager-alapú hálózatokat.<br/><br/> Be kell állítania a hálózatleképezést |Kizárólag klasszikus tárolóba történő feladatátvétel.<br/><br/> A feladatátvételt követően a virtuális gépek csatlakoztatásához kizárólag klasszikus hálózatokat használjon. |
| **Hyper-V virtuális gép replikációja (VMM-mel) másodlagos helyre** |Egyszerűsített üzembe helyezési folyamat.<br/><br/> Virtuális gépek feladatainak átadása klasszikus vagy Resource Manager-alapú tárolónak.<br/><br/> Replikálás klasszikus vagy Resource Manager-alapú tárolóba.<br/><br/> A feladatátvételt követően az Azure virtuális gépek csatlakoztatásához használjon klasszikus vagy Resource Manager-alapú hálózatokat.<br/><br/> Be kell állítania a hálózatleképezést |Kizárólag klasszikus tárolóba történő feladatátvétel.<br/><br/> A feladatátvételt követően a virtuális gépek csatlakoztatásához kizárólag klasszikus hálózatokat használjon.<br/><br/> Beállíthatja a tárolóleképezést. <br/><br/> A SAN-replikáció nem támogatott. |

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek
A Site Recovery számos forgatókönyv esetében használható replikációra:

* **VMware virtuális gépek replikálása**: A helyszíni VMware virtuális gépek replikálása Azure-tárolóba vagy másodlagos adatközpontba is elvégezhető.
* **Fizikai gépek replikálása**: A Windows vagy Linux rendszert futtató fizikai gépek replikálása Azure-tárolóba vagy másodlagos adatközpontba is elvégezhető. A fizikai gépek és a VMware virtuális gépek replikálási folyamata csaknem megegyezik.
* **Hyper-V virtuális gépek replikálása**: Ha a Hyper-V-gazdagépek kezelése a System Center VMM-felhőben történik, a virtuális gépek replikálása Azure-tárolóba vagy másodlagos adatközpontba is elvégezhető. Ha a gazdagépek kezelését nem a VMM végzi, kizárólag az Azure-ba történő replikálás hajtható végre. A nem a VMM által felügyelt Hyper-V virtuális gépek replikálása Azure-tárolóba végezhető el.
* **Virtuális gépek**: a Site Recovery az [Azure-beli IaaS típusú virtuális gépek](site-recovery-migrate-azure-to-azure.md) régiók közötti áttelepítésére, illetve [AWS Windows-példányok](site-recovery-migrate-aws-to-azure.md) Azure-beli IaaS típusú virtuális gépekre való áttelepítésére is használható. A teljes replikáció jelenleg nem támogatott. A replikálás elvégezhető áttelepítési (feladatátvételi) céllal, a feladatok visszavétele azonban nem valósítható meg. 

A Site Recovery a fent megnevezett típusú virtuális gépeken és fizikai kiszolgálókon futó legtöbb alkalmazást is képes replikálni. A támogatott alkalmazások teljes körű összefoglalóját megtalálja a [Milyen számítási feladatokat tud védeni az Azure Site Recovery?](site-recovery-workload.md) című cikkben.

## <a name="replicate-vmware-virtual-machines-to-azure"></a>VMware virtuális gépek replikálása az Azure-ba
![Továbbfejlesztett](./media/site-recovery-components/arch-enhanced.png)

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Rendelkeznie kell egy Azure-fiókkal.<br/><br/> **Tárterület:** A replikált adatok tárolásához Azure-tárfiókra van szükség. Használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A fiók lehet LRS vagy GRS, amikor az Azure Portal webhelyen végzi az üzembe helyezést. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.<br/><br/> **Hálózat:** Szüksége van egy olyan Azure-beli virtuális hálózatra, amelyhez az Azure-beli virtuális gépek csatlakozni fognak a feladatátvétel közbeni létrehozáskor. Az Azure Portalon a hálózatokat a klasszikus szolgáltatáskezelői modellben, vagy az erőforrás-kezelői modellben lehet létrehozni. |
| **Helyszíni konfigurációs kiszolgáló** |Szüksége van egy helyszíni Windows Server 2012 R2 rendszert futtató gépre, amely a konfigurációs kiszolgálót, valamint más Site Recovery-összetevőket tesz elérhetővé.<br/><br/> A gépnek magas rendelkezésre állású VMware virtuális gépnek kell lennie.<br/><br/> A kiszolgálón található összetevők:<br/><br/> **Konfigurációs kiszolgáló**: a helyszíni környezet és az Azure közötti kommunikáció koordinálását, valamint az adatreplikáció és -helyreállítás kezelését végzi.<br/><br/> **Folyamatkiszolgáló**: replikációs átjáróként üzemel. Ez az összetevő fogadja a védett forrásgépek adatait, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, majd továbbítja az adatokat az Azure Storage-nak. Ezenfelül kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését. Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.<br/><br/> **Fő célkiszolgáló**: az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat. |
| **Helyszíni virtualizálási kiszolgálók** |Egy vagy több vSphere-gazdagép. A gazdagépek felügyeletéhez egy vCenter-kiszolgáló üzembe állítását is javasoljuk. |
| **Replikálandó virtuális gépek** |Minden Azure-ba replikálni kívánt VMware virtuális gépen telepítve kell lennie a mobilitási szolgáltatás-összetevőnek. A szolgáltatás észleli a gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. Az összetevő manuálisan is telepíthető, de azt is beállíthatja, hogy a folyamatkiszolgáló automatikusan, leküldéses módszerrel telepítse, amikor engedélyezi az adott gép replikálását. |

* [További információk](site-recovery-vmware-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-failback-azure-to-vmware.md) a feladat-visszavételről az Azure Portal webhelyen.

## <a name="replicate-physical-servers-to-azure"></a>Fizikai kiszolgálók replikálása az Azure-ba
![Továbbfejlesztett](./media/site-recovery-components/arch-enhanced.png)

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Rendelkeznie kell egy Azure-fiókkal.<br/><br/> **Tárterület:** A replikált adatok tárolásához Azure-tárfiókra van szükség. Használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A fiók lehet LRS vagy GRS, amikor az Azure Portal webhelyen végzi az üzembe helyezést. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.<br/><br/> **Hálózat:** Szüksége van egy olyan Azure-beli virtuális hálózatra, amelyhez az Azure-beli virtuális gépek csatlakozni fognak a feladatátvétel közbeni létrehozáskor. Az Azure Portalon a hálózatokat a klasszikus szolgáltatáskezelői modellben, vagy az erőforrás-kezelői modellben lehet létrehozni. |
| **Helyszíni konfigurációs kiszolgáló** |Szüksége van egy helyszíni Windows Server 2012 R2 rendszert futtató gépre, amely a konfigurációs kiszolgálót, valamint más Site Recovery-összetevőket tesz elérhetővé.<br/><br/> A kiszolgáló virtuális vagy fizikai is lehet.<br/><br/> A kiszolgálón található összetevők:<br/><br/> **Konfigurációs kiszolgáló**: a helyszíni környezet és az Azure közötti kommunikáció koordinálását, valamint az adatreplikáció és -helyreállítás kezelését végzi.<br/><br/> **Folyamatkiszolgáló**: replikációs átjáróként üzemel. Ez az összetevő fogadja a védett forrásgépek adatait, gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, majd továbbítja az adatokat az Azure Storage-nak. Ezenfelül kezeli a mobilitási szolgáltatás leküldéses telepítését a védett gépekre, és elvégzi a WMware virtuális gépek automatikus felderítését. Az üzemelő példány bővülésével további, önálló és dedikált folyamatkiszolgálókat helyezhet üzembe, amelyek segítségével képes lesz a megnövekedett replikációs forgalom kezelésére is.<br/><br/> **Fő célkiszolgáló**: az Azure-ból történő feladat-visszavétel során kezeli a replikációs adatokat. |
| **Replikálandó gépek** |Minden Azure-ba replikálni kívánt fizikai, Windows vagy Linux rendszerű gépen telepítve kell lennie a mobilitási szolgáltatás összetevőjének. A szolgáltatás észleli a gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. Az összetevő manuálisan is telepíthető, de azt is beállíthatja, hogy a folyamatkiszolgáló automatikusan, leküldéses módszerrel telepítse, amikor engedélyezi az adott gép replikálását. |
| **Feladat-visszavétel** |A fizikai kiszolgálóról másik fizikai kiszolgálóra történő feladat-visszavétel nem támogatott. Ez azt jelenti, hogy ha fizikai kiszolgálók feladatait adja át az Azure-nak, majd vissza szeretné adni a feladatokat, egy VMware-alapú virtuális gépnek kell visszaadnia azokat. Fizikai kiszolgáló nem használható a feladat-visszavételhez. A feladat-visszavételhez szüksége lesz egy Azure-beli virtuális gépre. Ha nem VMware-alapú virtuális gépként helyezte üzembe a konfigurációs kiszolgálót, létre kell hoznia egy különálló fő célkiszolgálót egy VMware-alapú virtuális gép formájában. Ez azért szükséges, mert a fő célkiszolgáló együttműködik és kapcsolatba lép a VMware-tárral a lemezeknek a VMware virtuális gépre történő visszaállításához. |

* [További információk](site-recovery-vmware-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-failback-azure-to-vmware.md) a feladat-visszavételről az Azure Portal webhelyen.

## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Nem a VMM által felügyelt Hyper-V-virtuálisgépek replikálása az Azure-ba
![Replikálás Hyper-V-helyről az Azure-ba](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Rendelkeznie kell egy Azure-fiókkal.<br/><br/> **Tárterület:** A replikált adatok tárolásához Azure-tárfiókra van szükség. Használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A fióknak GRS-nek kell lennie. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.<br/><br/> **Hálózat:** Szüksége van egy olyan Azure-beli virtuális hálózatra, amelyhez az Azure-beli virtuális gépek csatlakozni fognak a feladatátvétel közbeni létrehozáskor. |
| **Hyper-V-gazdagépek/virtuális gépek** |Legalább egy Hyper-V-gazdagép, amely egy vagy több virtuális gépet futtat.<br/><br/> A rendszer az üzembe helyezés során minden gazdagépre telepíti a Site Recovery Providert és a Recovery Services-ügynököt. |

* [További információk](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>VMM által felügyelt Hyper-V-virtuálisgépek replikálása az Azure-ba
![VMM-replikáció Azure-ba](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Rendelkeznie kell egy Azure-fiókkal.<br/><br/> **Tárterület:** A replikált adatok tárolásához Azure-tárfiókra van szükség. Használhat klasszikus fiókot vagy Resource Manager-tárfiókot. A fióknak GRS-nek kell lennie. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre.<br/><br/> **Hálózat:** Szüksége van egy olyan Azure-beli virtuális hálózatra, amelyhez az Azure-beli virtuális gépek csatlakozni fognak a feladatátvétel közbeni létrehozáskor. |
| **VMM-kiszolgáló** |Szüksége van legalább egy helyszíni VMM-kiszolgálóra, amely egy vagy több magánfelhővel van ellátva. A rendszer az üzembe helyezés során minden kiszolgálóra telepíti a Site Recovery Providert. |
| **Hyper-V-gazdagépek/virtuális gépek** |Legalább egy Hyper-V-gazdagép, amely egy vagy több virtuális gépet futtat.<br/><br/> A rendszer az üzembe helyezés során minden gazdagépre telepíti a Recovery Services-ügynököt. |

* [További információk](site-recovery-vmm-to-azure.md#azure-requirements) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-vmm-to-azure-classic.md#before-you-start) a klasszikus portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>VMware-alapú virtuális gépek vagy fizikai kiszolgálók replikálása másodlagos helyre
![VMware és VMware közötti replikáció](./media/site-recovery-components/vmware-to-vmware.png)

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Ezt a forgatókönyvet az InMage Scouttal kell telepítenie. A beszerzéséhez rendelkeznie kell egy Azure-fiókkal.<br/><br/> Miután létrehozott egy Site Recovery-tárolót, töltse le az InMage Scoutot, és telepítse a legújabb frissítéseket a telepítés előkészítéséhez. |
| **Elsődleges hely** |**Folyamatkiszolgáló**: állítsa be a folyamatkiszolgáló összetevőt az elsődleges helyen. Ez végzi az adatok gyorsítótárazását, tömörítését és optimalizálását. Ezenfelül ez az összetevő kezeli a Unified Agent ügynöknek a védeni kívánt gépekre történő leküldéses telepítését.<br/><br/> **VMware ESX/ESXi és vCenter:** Szüksége van egy VMware EXS/ESXi-hipervizorra, és igény szerint egy VMware vCenter-kiszolgálóra.<br/><br/> **Virtuális gépek** |
| **Másodlagos hely** |**Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló az az összetevő, amelyet elsőként telepíteni kell a másodlagos helyre az üzemelő példánynak a felügyeleti weblapon vagy a vContinuum-konzolban való felügyelete, konfigurálása és figyelése céljából. Az üzemelő példány csupán egyetlen konfigurációs kiszolgálót tartalmaz. Ezt a Windows Server 2012 R2-es verziójával futó gépre kell telepíteni.<br/><br/> **vContinuum-kiszolgáló (másodlagos hely):** Ugyanoda kell telepíteni, mint a konfigurációs kiszolgálót. Ez az összetevő elérhetővé tesz egy konzolt, amelyről felügyelheti és figyelheti a védett környezetet. Alapértelmezett telepítés esetén a vContinuum-kiszolgáló az első fő célkiszolgáló, és telepítve van rá a Unified Agent ügynök.<br/><br/> **Fő célkiszolgáló**: a fő célkiszolgáló tárolja a replikált adatokat. Ez fogadja a folyamatkiszolgáló által küldött adatokat, létrehozza a replika gépet a másodlagos helyen, és tárolja az adatmegőrzési pontokat. Az, hogy hány fő célkiszolgálóra van szükség, attól függ, hogy hány gépnek kíván védelmet biztosítani. Ha vissza szeretné adnia a feladatokat az elsődleges helynek, azon is szüksége lesz egy fő célkiszolgálóra. |

A VMware-alapú virtuális gépek vagy fizikai kiszolgálók másodlagos helyre történő replikálásához töltse le az Azure Site Recovery-előfizetéshez járó InMage Scoutot. Ezt az Azure Portalról vagy a klasszikus Azure portálról lehet letölteni.

Állítsa be mindkét oldalon az összetevőkiszolgálókat (konfigurációs, folyamat- és fő célkiszolgáló), majd telepítse a replikálni kívánt gépekre a Unified Agent ügynököt. A kezdeti replikációt követően a gépek ügynökei továbbítják a változásreplikálás módosításait a folyamatkiszolgálónak. A folyamatkiszolgáló optimalizálja az adatokat, majd átviszi őket a másodlagos hely fő célkiszolgálójára. A replikációs folyamatot a konfigurációs kiszolgáló kezeli.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>VMM által felügyelt Hyper-V-virtuálisgépek replikálása másodlagos helyre
![Két helyszíni hely közötti replikálás](./media/site-recovery-components/arch-onprem-onprem.png)

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Rendelkeznie kell egy Azure-fiókkal. |
| **VMM-kiszolgáló** |Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen. Minden kiszolgálónak legalább egy magánfelhővel kell rendelkeznie.<br/><br/> Az üzembe helyezés során telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. |
| **Hyper-V-gazdagépek/virtuális gépek** |Az elsődleges és a másodlagos hely VMM-felhőiben legalább egy Hyper-V-gazdagépnek kell futnia<br/><br/> Minden gazdagépnek rendelkeznie kell legalább egy replikálható virtuális géppel.<br/><br/>. A rendszer az üzembe helyezés során minden gazdagépre telepíti a Recovery Services-ügynököt. |

* [További információk](site-recovery-vmm-to-vmm.md#azure-prerequisites) az Azure Portalon végzett üzembe helyezésre vonatkozó követelményekről.
* [További információk](site-recovery-vmm-to-vmm-classic.md#before-you-start) a klasszikus Azure portálon végzett üzembe helyezésre vonatkozó követelményekről.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>VMM által felügyelt Hyper-V-virtuálisgépek replikálása másodlagos helyre SAN-replikálással
![SAN-replikáció](./media/site-recovery-components/arch-onprem-onprem-san.png)

A VMM-felhőkben felügyelt Hyper-V-virtuálisgépeket a klasszikus portálon lehet másodlagos helyre replikálni SAN-replikálással. Az Azure Portal jelenleg nem támogatja ezt a forgatókönyvet.

| **Összetevő** | **Részletek** |
| --- | --- |
| **Azure** |**Fiók:** Rendelkeznie kell egy Azure-fiókkal. |
| **VMM-kiszolgáló** |Javasoljuk, hogy legyen egy VMM-kiszolgáló az elsődleges helyen, és egy a másodlagos helyen. Minden kiszolgálónak legalább egy magánfelhővel kell rendelkeznie.<br/><br/> Az üzembe helyezés során telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. |
| **SAN** |Egy támogatott SAN-tömb, amelyet az elsődleges VMM-kiszolgáló felügyel.<br/><br/> A SAN-nak közös hálózati infrastruktúrát kell használnia a másodlagos hely egyik SAN-tömbjével. |
| **Hyper-V-gazdagépek/virtuális gépek** |Az elsődleges és a másodlagos hely VMM-felhőiben legalább egy Hyper-V-gazdagépnek kell futnia<br/><br/> Minden gazdagépnek rendelkeznie kell legalább egy replikálható virtuális géppel.<br/><br/>. A rendszer az üzembe helyezés során minden gazdagépre telepíti a Recovery Services-ügynököt. |

E forgatókönyv esetében a Site Recovery üzembe helyezése során telepítse az Azure Site Recovery Providert a VMM-kiszolgálókra. A Provider az interneten keresztül koordinálja és valósítja meg a Site Recovery szolgáltatással történő replikációt. A rendszer SAN-szinkronreplikálás használatával replikálja az adatokat az elsődleges és másodlagos tárolótömbök között.

[Itt megismerheti](site-recovery-vmm-san.md#before-you-start) az üzembe helyezés feltételeit.

## <a name="hyper-v-protection-lifecycle"></a>A Hyper-V védelem életciklusa
Ez a munkafolyamat bemutatja a Hyper-V virtuális gépek védelmének, replikálásának és feladatátadásának lépéseit.

1. **Védelem engedélyezése**: állítsa be a Site Recovery-tárolót, adja meg a VMM-felhő vagy a Hyper-V-hely replikációs beállításait, majd engedélyezze a virtuális gépek védelmét. A szolgáltatás elindít egy **Védelem engedélyezése** nevű feladatot, amely a **Feladatok** lapon követhető nyomon. A feladat ellenőrzi, hogy a gép megfelel-e az előfeltételeknek, majd meghívja a [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) metódust, amely az Ön által megadott beállításoknak megfelelően létrehozza az Azure-ba történő replikációt. A **Védelem engedélyezése** nevű feladat ezenfelül meghívja a [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) metódust is, amely teljes körű virtuálisgép-replikációt indít el.
2. **Kezdeti replikáció:** Egy pillanatkép készül a virtuális gépről, majd a rendszer egyenként replikálja a virtuális merevlemezeket, amíg az összes át nem lesz másolva az Azure-ba vagy a másodlagos adatközpontba. Az ehhez a művelethez szükséges időtartam a virtuális gép méretétől, a hálózati sávszélességtől, illetve a kezdeti replikáció módjától függ. Ha módosítják a lemezt, miközben a kezdeti replikáció folyamatban van, a Hyper-V-replikációkövető nyomon követi a módosításokat replikálási naplók (.hrl) formájában, amelyek ugyanabban a mappában találhatók, mint a lemezek. Minden lemezhez tartozik egy .hrl-fájl, amelyet a rendszer továbbít a másodlagos tárterületre. Ne feledje, hogy a pillanatkép- és a naplófájlok a kezdeti replikáció futása közben is lemezerőforrásokat használnak. A kezdeti replikáció befejeztével a rendszer törli a virtuális gép pillanatképét, valamint szinkronizálja és egyesíti a naplózott lemezmódosításokat.
3. **Védelem véglegesítése:** A kezdeti replikáció befejezése után a **Védelem véglegesítése** feladat konfigurálja a hálózatot és más replikáció utáni beállításokat a virtuális gép védelméhez. Azure-ba történő replikálás esetén előfordulhat, hogy módosítania kell a virtuális gép beállításait, hogy az készen álljon a feladatátvételre. Ezen a ponton érdemes lehet feladatátvételi tesztet futtatni, amellyel ellenőrizheti, hogy minden megfelelően működik-e.
4. **Replikáció**: a kezdeti replikációt követően a replikációs beállításoknak megfelelően elindul a változások szinkronizálása.
   * **Sikertelen replikáció**: ha nem sikerül a változások replikálása, és a teljes replikáció túl sok sávszélességet vagy időt venne igénybe, a rendszer újraszinkronizálást végez. Ha például a .hrl-fájlok mérete eléri a lemezkapacitás 50%-át, a rendszer kijelöli a virtuális gépet újraszinkronizálásra. Az újraszinkronizálás kiszámítja a forrás és a cél virtuális gépek ellenőrzőösszegeit, és ezek alapján csak a változásokat továbbítja, így segít csökkenti az adatmennyiséget. Az újraszinkronizálás befejezését követően folytatódik a változásreplikálás. Alapértelmezés szerint a rendszer automatikusan munkaidőn kívüli időpontra ütemezi az újraszinkronizálást, de manuálisan is elvégezhető a virtuális gép újraszinkronizálása.
   * **Replikációs hiba**: ha hiba lép fel a replikáció során, a rendszer automatikusan újrapróbálkozik. Ha nem helyrehozható hiba, például hitelesítési vagy engedélyezési hiba történik, vagy egy replikagép érvénytelen állapotban van, a rendszer nem próbálkozik újra. Ha helyrehozható hiba, például hálózati hiba vagy alacsony lemezterülettel/memóriával kapcsolatos hiba lép fel, a rendszer újrapróbálkozik, és folyamatosan növeli a próbálkozások közötti időintervallumot (1, 2, 4, 8, 10, majd 30 perc).
5. **Tervezett/nem tervezett feladatátvételek**: igény szerint futtathat tervezett/nem tervezett feladatátvételeket. Ha tervezett feladatátvételt végez, a forrás virtuális gépek leállnak, így nincs adatvesztés. A replikaként létrehozott virtuális gépeket a rendszer függőben lévő állapotba helyezi. A feladatátvétel befejezéséhez véglegesíteni kell ezeket, kivéve, ha SAN-replikálást végez, amely automatikusan elvégzi a véglegesítést. Ha felállt az elsődleges hely, a rendszer elvégzi a feladatátvételt. Ha az Azure-ba végzett replikálást, a fordított replikáció automatikusan megtörténik. Ellenkező esetben indítsa el manuálisan a fordított replikációt.

![munkafolyamat](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Következő lépések
[Felkészülés az üzembe helyezésre](site-recovery-best-practices.md)

<!--HONumber=Oct16_HO3-->


