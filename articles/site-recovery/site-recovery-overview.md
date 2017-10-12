---
title: Mi az Azure Site Recovery? | Microsoft Docs
description: "Áttekintést nyújt az Azure Site Recovery szolgáltatásról, és összefoglalja az üzembehelyezési forgatókönyveket."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-site-recovery"></a>Mi a Site Recovery?

Üdvözli az Azure Site Recovery szolgáltatás! Ebben a cikkben a szolgáltatás rövid áttekintését olvashatja el.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Üzletmenet-folytonosság és vészhelyreállítás (BCDR) az Azure Recovery Services használatával

Egy szervezetnek gondolnia kell az adatok biztonságára és az alkalmazások/munkafolyamatok folyamatos futására tervezett vagy nem tervezett leállások esetén.

Az Azure Recovery Services segít a BCDR-stratégia kidolgozásában:

- **Site Recovery szolgáltatás**: A Site Recovery azzal segít fenntartani az üzletmenet folytonosságát, hogy egy hely leállása esetén tovább futtatja az alkalmazásokat az elérhető virtuális gépeken és fizikai kiszolgálókon. A Site Recovery replikálja a virtuális gépeken és fizikai kiszolgálókon futó folyamatokat, hogy azok egy másodlagos helyen elérhetők maradjanak az elsődleges hely elérhetetlenné válása esetén. Amikor az elsődleges hely ismét üzembe áll, helyreállítja rajta a munkafolyamatokat.
- **Biztonsági másolat szolgáltatás**: Emellett az [Azure Backup](https://docs.microsoft.com/azure/backup/) szolgáltatás az adatok biztonságáról és helyreállíthatóságáról gondoskodik azzal, hogy biztonsági másolatot készít az Azure-ban.

A Site Recovery a következők replikációját képes kezelni:

- Azure virtuális gépek replikációja Azure-régiók között.
- Helyszíni virtuális gépek és fizikai kiszolgálók replikációja az Azure-ba, vagy egy másodlagos helyre.


## <a name="what-does-site-recovery-provide"></a>Mit nyújt a Site Recovery?

**Funkció** | **Részletek**
--- | ---
**Egyszerű BCDR-megoldás üzembe helyezése** | A Site Recovery segítségével beállítható és felügyelhető a replikáció, a feladatátvétel és feladat-visszavétel az Azure Portal egy helyéről.
**Azure-beli virtuális gépek replikálása** | A BCDR-stratégia beállítható úgy, hogy az Azure virtuális gépek Azure-régiók között replikálódjanak.
**Helyszíni virtuális gépek telephelyen kívüli replikációja** | Helyszíni virtuális gépeket és fizikai kiszolgálókat replikálhat az Azure-ba vagy egy másodlagos helyre. Az Azure-ba történő replikációval kiküszöbölhetők a másodlagos adatközpontok fenntartásával járó bonyodalmak és költségek.
**Bármilyen számítási feladat replikálható** | A támogatott Azure virtuális gépeken, helyszíni Hyper-V-alapú virtuális gépeken, a VMware-alapú virtuális gépeken és a Windows-/Linux-alapú fizikai kiszolgálókon futó bármilyen számítási feladat replikálható.
**Adatok biztonságos, rugalmas megőrzése** | A Site Recovery koordinálja a replikációt, de az alkalmazás adataihoz nem fér hozzá. A replikált adatokat az Azure Storage tárolja, és az ezzel járó rugalmasságot is biztosítja. Feladatátvétel esetén a rendszer Azure-beli virtuális gépeket hoz létre a replikált adatok alapján.
**RTO és RPO** | A helyreállítási idők célkitűzéseinek (recovery time objectives, RTO-k) és a helyreállítási pont célkitűzéseinek (recovery point objectives, RPO-k) a szervezeti korlátokon belül tartása. A Site Recovery folyamatos replikációt biztosít Azure és VMware virtuális gépek esetén, és csupán 30 másodperces replikációs frekvenciát Hyper-V esetén. A helyreállítási idők célkitűzései tovább csökkenthetők az [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)rel való integrációval.
**Alkalmazás-konzisztencia feladatátvétel esetén** | Helyreállítási pontok konfigurálhatók alkalmazás-konzisztens pillanatképekkel. Az alkalmazáskonzisztens pillanatképek a lemez adatait, a memóriában lévő összes adatot és az összes folyamatban lévő tranzakciót is rögzítik.
**Megszakítás nélküli teszt** | Egyszerűen futtathat feladatátvételi teszteket a vészhelyreállítási próbák támogatására anélkül, hogy ez hatással lenne a folyamatos replikációra.
**Rugalmas feladatátadások futtatása** | Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. Amint az elsődleges hely megint elérhetővé válik, egyszerűen visszaadhatja a feladatokat.
**Helyreállítási tervek létrehozása** | Helyreállítási tervekkel több virtuális gépen futó többszintű alkalmazások feladatátvételének és helyreállításának műveletsora is egyénileg kialakítható. Elvégezheti a gépek terveken belüli csoportosítását, valamint szkripteket és manuális műveleteket vehet fel. A helyreállítási tervek integrálhatók az Azure Automation-runbook használatával.
**Integráció meglévő BCDR-technológiákkal** | A Site Recovery más BCDR-technológiákkal integrálható. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server-háttérrendszerét, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében.
**Integráció az automatizálási könyvtárral** | Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba.
**Hálózati beállítások kezelése** | A Site Recovery és az Azure integrációja leegyszerűsíti az alkalmazáshálózat kezelését, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, ami hatékony hálózatváltást garantál.


## <a name="what-can-i-replicate"></a>Miket replikálhatok?

**Támogatott** | **Részletek**
--- | ---
**Miket replikálhatok?** | Azure virtuális gépek Azure-régiók között (előzetes)<br/><br/>  Helyszíni VMware virtuális gépek, Hyper-V virtuális gépek, valamint fizikai kiszolgálók (Windows és Linux) az Azure-ba.<br/> Helyszíni VMware virtuális gépek, Hyper-V virtuális gépek, valamint fizikai kiszolgálók egy másodlagos helyre. Hyper-V virtuális gépek másodlagos helyre történő replikációja csak abban az esetben támogatott, ha a Hyper-V gazdagépeket a System Center VMM felügyeli.
**Mely régiók támogatottak a Site Recoveryhez?** | [Támogatott régiók](https://azure.microsoft.com/regions/services/) |
**Mely operációs rendszerek használhatóak a replikált gépeken?** | [Azure virtuálisgép-követelmények](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>[VMware virtuálisgép-követelmények](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> A Hyper-V virtuális gépek esetében az Azure által támogatott bármely [vendég operációs rendszer](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) és a Hyper-V is támogatott.<br/><br/> [Fizikai kiszolgáló követelmények](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**Milyen VMware-kiszolgálókra/-gazdagépekre van szükségem?** | VMware virtuális gépek elhelyezhetők [támogatott vSphere gazdagépeken/vCenter kiszolgálókon](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Milyen számítási feladatokat replikálhatok?** | A támogatott replikációs gépeken futó bármilyen számítási feladat replikálható. Ezenkívül a Site Recovery csapata alkalmazásspecifikus tesztelést végzett [néhány alkalmazáshoz](site-recovery-workload.md#workload-summary).


## <a name="azure-portal-considerations"></a>Azure Portal tudnivalók

* A Site Recovery az [Azure Portalon](https://portal.azure.com) helyezhető üzembe.
* A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatáskezelési modellel kezelhető.
- A klasszikus portál csak a meglévő Site Recovery-környezetek fenntartására használható. Új tárolók nem hozhatók létre a klasszikus portálon.

## <a name="next-steps"></a>Következő lépések
* További információk a [támogatott számítási feladatokról](site-recovery-workload.md)
* Első lépések: [Azure virtuális gép replikációja régiók között](site-recovery-azure-to-azure.md), [VMware replikáció az Azure-ba](vmware-walkthrough-overview.md), vagy [Hyper-V replikáció az Azure-ba](hyper-v-site-walkthrough-overview.md).
