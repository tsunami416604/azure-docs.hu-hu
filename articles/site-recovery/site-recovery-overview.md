---
title: Tudnivalók az Azure Site Recovery szolgáltatásról | Microsoft Docs
description: Áttekintést nyújt az Azure Site Recovery szolgáltatásról, és összefoglalja az üzembehelyezési forgatókönyveket.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 03/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 57b191a5dbdea487a0105c6af2866cdc037864e2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="about-site-recovery"></a>Tudnivalók a Site Recovery szolgáltatásról

Üdvözli az Azure Site Recovery szolgáltatás! Ebben a cikkben a szolgáltatás rövid áttekintését olvashatja el.

Vállalatának szüksége van egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely biztosítja az adatok biztonságát, illetve az alkalmazások és a számítási feladatok folyamatos működését a tervezett és nem tervezett leállások során.

Az Azure Recovery Services segít a BCDR-stratégia kidolgozásában:

- **Site Recovery szolgáltatás**: A Site Recovery azzal segít fenntartani az üzletmenet folytonosságát, hogy leállás esetén tovább futtatja az üzleti alkalmazásokat és a számítási feladatokat. A Site Recovery képes a fizikai és virtuális gépeken futó számítási feladatok replikálására egy elsődleges helyről egy másodlagos helyre. Ha az elsődleges helyen kimaradás történik, a rendszer átadja a feladatokat a másodlagos helynek, és az alkalmazások onnan lesznek elérhetők. Amint az elsődleges hely megint elérhetővé válik, visszatérhet hozzá.  
- **Backup szolgáltatás**: Az [Azure Backup](https://docs.microsoft.com/azure/backup/) szolgáltatás az adatok biztonságáról és helyreállíthatóságáról gondoskodik azzal, hogy biztonsági másolatot készít az Azure-ban.

A Site Recovery a következők replikációját képes kezelni:

- Azure virtuális gépek replikációja Azure-régiók között.
- Helyszíni virtuális gépek és fizikai kiszolgálók replikációja az Azure-ba vagy egy másodlagos helyre.


## <a name="what-does-site-recovery-provide"></a>Mit nyújt a Site Recovery?


**Funkció** | **Részletek**
--- | ---
**Egyszerű BCDR-megoldás** | A Site Recovery segítségével beállítható és felügyelhető a replikáció, a feladatátvétel és feladat-visszavétel az Azure Portal egy helyéről.
**Azure-beli virtuális gép replikációja** | Beállíthatja az Azure-beli virtuális gépek vészhelyreállítását egy elsődleges régióról egy másodlagos régióra.
**Helyszíni virtuális gép replikációja** | Helyszíni virtuális gépeket és fizikai kiszolgálókat replikálhat az Azure-ba vagy egy másodlagos helyszíni adatközpontba. Az Azure-ba történő replikációval kiküszöbölhetők a másodlagos adatközpontok fenntartásával járó bonyodalmak és költségek.
**Számítási feladat replikációja** | A támogatott Azure-beli virtuális gépeken, helyszíni Hyper-V-alapú és VMware-alapú virtuális gépeken, valamint a Windows-/Linux-alapú fizikai kiszolgálókon futó bármilyen számítási feladat replikálható.
**Adatrugalmasság** | A Site Recovery koordinálja a replikációt, de az alkalmazás adataihoz nem fér hozzá. Az Azure-ba történő replikálás során az adatokat az Azure Storage tárolja, és az ezzel járó rugalmasságot is biztosítja. Feladatátvétel esetén a rendszer Azure-beli virtuális gépeket hoz létre a replikált adatok alapján.
**RTO- és RPO-célok** | A vállalati korlátokon belül tartja a helyreállítási idők célkitűzéseit (recovery time objectives, RTO-k) és a helyreállítási pont célkitűzéseit (recovery point objectives, RPO-k). A Site Recovery folyamatos replikációt biztosít Azure és VMware virtuális gépek esetén, és csupán 30 másodperces replikációs frekvenciát Hyper-V esetén. Az RTO tovább csökkenthető az [Azure Traffic Managerrel](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) való integrációval.
**Alkalmazás-konzisztencia feladatátvétel esetén** | Alkalmazáskonzisztens pillanatképekkel rendelkező helyreállítási pontokat használhat a replikáláshoz. Ezek a pillanatképek a lemez adatait, a memóriában lévő összes adatot és az összes folyamatban lévő tranzakciót is rögzítik.
**Megszakítás nélküli tesztelés** | Egyszerűen végrehajthat vészhelyreállítási próbákat anélkül, hogy ez hatással lenne a folyamatban lévő replikációra.
**Rugalmas feladatátvételek** | Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. Amint az elsődleges hely megint elérhetővé válik, egyszerűen visszaadhatja a feladatokat.
**Testre szabott helyreállítási tervek** | Helyreállítási tervek használatával több virtuális gépen futó többszintű alkalmazások feladatátvételének és helyreállításának műveletsora is egyénileg kialakítható. Csoportosíthatja a gépeket a helyreállítási terveken belül, valamint opcionálisan szkripteket és manuális műveleteket vehet fel. A helyreállítási tervek integrálhatók az Azure Automation-runbook használatával.
**BCDR-integráció** | A Site Recovery más BCDR-technológiákkal integrálható. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server-háttérrendszerét, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében.
**Azure Automation-integráció** | Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba.
**Hálózat-integráció** | A Site Recovery és az Azure integrációja leegyszerűsíti az alkalmazáshálózat kezelését, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, ami hatékony hálózatváltást garantál.


## <a name="what-can-i-replicate"></a>Miket replikálhatok?

**Támogatott** | **Részletek**
--- | ---
**Replikációs forgatókönyvek** | Azure-beli virtuális gépeket replikálhat egyik Azure-régióból a másikba.<br/><br/>  Helyszíni VMware virtuális gépeket, Hyper-V virtuális gépeket, valamint fizikai kiszolgálókat (Windows és Linux) replikálhat az Azure-ba.<br/><br/> Helyszíni VMware virtuális gépeket, a System Center VMM által felügyelt Hyper-V virtuális gépeket, valamint fizikai kiszolgálókat replikálhat egy másodlagos helyre.
**Régiók** | Áttekintheti a Site Recovery által [támogatott régiókat](https://azure.microsoft.com/regions/services/). |
**Replikált gépek** | Áttekintheti az [Azure-beli virtuális gépeknek](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions), a [helyszíni VMware virtuális gépeknek és fizikai kiszolgálóknak](vmware-physical-azure-support-matrix.md#replicated-machines), valamint a [helyszíni Hyper-V virtuális gépeknek](hyper-v-azure-support-matrix.md#replicated-vms) a replikációs követelményeit.
**VMware-kiszolgálók/-gazdagépek** | A replikálni kívánt VMware virtuális gépek a [támogatott gazdagépeken és virtualizációs kiszolgálókon](vmware-physical-azure-support-matrix.md) helyezhetők el.
**Számítási feladatok** | A támogatott replikációs gépeken futó bármilyen számítási feladat replikálható. Ezenkívül a Site Recovery csapata alkalmazásspecifikus tesztelést végzett [néhány alkalmazáshoz](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>További lépések
* További információk a [támogatott számítási feladatokról](site-recovery-workload.md).
* Első lépések: [Azure-beli virtuális gép replikálása régiók között](azure-to-azure-quickstart.md). 
