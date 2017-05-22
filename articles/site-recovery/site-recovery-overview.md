---
title: Mi a Site Recovery? | Microsoft Docs
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
ms.date: 03/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 89921997f68bd72ee6086d2b635361eeeaba1c28
ms.contentlocale: hu-hu
ms.lasthandoff: 05/11/2017


---
# <a name="what-is-site-recovery"></a>Mi a Site Recovery?

Üdvözli az Azure Site Recovery szolgáltatás! Ebben a cikkben a szolgáltatás rövid áttekintését olvashatja el.

A leállásokat természeti események és működési hibák okozzák. A cégének szüksége van egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy védhetők meg az adatok és tarthatók üzemben az appok a tervezett és nem tervezett leállások során, illetve hogy miként lehet a lehető leggyorsabban visszaállni a normál üzleti működésre.

Az Azure helyreállítási szolgáltatásai segítenek a BCDR-stratégia kidolgozásában. Az [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) szolgáltatással biztosíthatja adatai védelmét és helyreállíthatóságát. A Site Recovery a számítási feladatok replikálására, feladatátvételére és helyreállítására szolgál, így azok meghibásodás esetében is elérhetőek maradnak.

## <a name="what-does-site-recovery-provide"></a>Mit nyújt a Site Recovery?

- **Vészhelyreállítás a felhőben** – A virtuális gépeken és fizikai kiszolgálókon futó számítási feladatokat egy másodlagos helyszín helyett az Azure-ba replikálhatja. Ezáltal kiküszöbölhetők a másodlagos adatközpontok fenntartásával járó bonyodalmak és költségek.
- **Rugalmas replikáció biztosítása hibrid környezetek számára** – A támogatott helyszíni Hyper-V-alapú virtuális gépeken, a VMware-alapú virtuális gépeken és a Windows-/Linux-alapú fizikai kiszolgálókon futó bármilyen számítási feladatot replikálhat.
- **Áttelepítés** – A Site Recovery használatával az AWS-példányokat Azure virtuális gépekre telepítheti át, illetve Azure virtuális gépeket telepíthet át Azure-régiók között.
- **A üzletmenet-folytonosság és vészhelyreállítás leegyszerűsítése** – Egyetlen helyről végezheti el a replikálás üzembe helyezését az Azure Portalon.  Egy vagy több gép egyszerű feladatátvételét és feladat-visszavételét is futtathatja.
- **Rugalmasság** – A Site Recovery koordinálja a replikációt és a feladatátvételt, de az alkalmazás adataihoz nem fér hozzá.
A replikált adatokat az Azure Storage tárolja, és az ezzel járó rugalmasságot is biztosítja. Feladatátvétel esetén a rendszer Azure-beli virtuális gépeket hoz létre a replikált adatok alapján.
- **Replikációs teljesítmény** – A Site Recovery által biztosított replikáció gyakorisága Hyper-V esetén akár 30 másodperc is lehet, VMware esetén pedig folyamatos lehet a replikáció. Helyreállítási időkorlát (RPO) küszöbértékeket állíthat be az adat-helyreállítási pontok létrehozási gyakoriságának szabályozására, illetve lecsökkentheti a helyreállítási időre vonatkozó célkitűzést (RTO) a Site Recovery automatikus helyreállítási folyamatával, és az [Azure Traffic Managerrel](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) való integrációval.
- **Alkalmazáskonzisztencia** – A gépek alkalmazáskonzisztens pillanatképek használatával végzik a replikálást. Az alkalmazáskonzisztens pillanatképek a lemez adatai mellett a memóriában lévő összes adatot és az összes folyamatban lévő tranzakciót is rögzítik.
- **Megszakítás nélküli tesztelés** – Egyszerűen futtathat feladatátvételi teszteket a vészhelyreállítási próbák támogatására anélkül, hogy ez hatással lenne az éles környezetekre.
- **Rugalmas feladatátvétel és helyreállítás** – Nulla adatvesztéssel járó tervezett feladatátvételeket is futtathat várt leállások esetére, illetve (a replikáció gyakoriságától függően) minimális adatvesztéssel járó nem tervezett feladatátvételeket a váratlan vészhelyzetek esetére. Amint az elsődleges hely megint elérhetővé válik, egyszerűen visszaadhatja a feladatokat.
- **Egyéni helyreállítási tervek** – A helyreállítási tervekkel modellezheti és testre szabhatja a több virtuális gépen futó többszintű alkalmazások feladatátvételét és helyreállítását. Elvégezheti a csoportok terveken belüli rendezését, valamint szkripteket és manuális műveleteket vehet fel. A helyreállítási tervek integrálhatók az Azure Automation-runbook használatával.
- **Többrétegű alkalmazások** – Helyreállítási terveket hozhat létre a többrétegű alkalmazások előkészített feladatátvételéhez és helyreállításához. A gépek különböző rétegek szerint csoportosíthatók (például adatbázis, webes, alkalmazás) egy helyreállítási terven belül, az egyes csoportok feladatátvétele és indítása pedig testreszabható.
* **Integrálás a meglévő BCDR-technológiákkal** – A Site Recovery integrálható más BCDR-technológiákkal. A Site Recovery segítségével megvédheti például a vállalati számítási feladatok SQL Server-háttérrendszerét, ideértve az SQL Server AlwaysOn natív támogatását is a rendelkezésre állási csoportok feladatátvételének kezelésében.
* **Integráció az automatizálási könyvtárral** – Az Azure Automation-könyvtár gazdag, éles használatra kész és alkalmazásspecifikus parancsfájlokat tartalmazó automatizálási könyvtár, amely letölthető, és beépíthető a Site Recovery szolgáltatásba.
* **Egyszerű hálózatkezelés** – A Site Recovery és az Azure fejlett hálózatkezelési funkciói leegyszerűsítik az alkalmazáshálózati követelményeket, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, ami hatékony hálózatváltást garantál.


## <a name="whats-supported"></a>Mik támogatottak?

**Támogatott** | **Részletek**
--- | ---
**Mely régiók támogatottak a Site Recoveryhez?** | [Támogatott régiók](https://azure.microsoft.com/en-us/regions/services/) |
**Miket replikálhatok?** | Helyszíni VMware virtuális gépek, Hyper-V virtuális gépek, valamint Windows- és Linux-alapú fizikai kiszolgálók.
**Mely operációs rendszerek használhatóak a replikált gépeken?** | VMware virtuális gépek [támogatott operációs rendszerei](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> A Hyper-V virtuális gépek esetében az Azure által támogatott bármely [vendég operációs rendszer](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) és a Hyper-V is támogatott.<br/><br/> Fizikai kiszolgálók [operációs rendszerei](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**Hova replikálhatok?** | Az Azure Storage-ba vagy másodlagos adatközpontba<br/><br/> Hyper-V esetén kizárólag a System Center VMM-felhőkben kezelt, Hyper-V-gazdagépeken lévő virtuális gépek replikálhatóak másodlagos adatközpontba.
**Milyen VMware-kiszolgálókra/-gazdagépekre van szükségem?** | A replikálni kívánt VMware virtuális gépek kezelése a [támogatott vSphere-gazdagépekkel vagy vCenter-kiszolgálókkal](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) valósítható meg
**Milyen számítási feladatokat replikálhatok?** | A támogatott replikációs gépeken futó bármilyen számítási feladat replikálható. Ezenkívül a Site Recovery csapata alkalmazásspecifikus tesztelést végzett [néhány alkalmazáshoz](site-recovery-workload.md#workload-summary).


## <a name="which-azure-portal"></a>Melyik Azure Portalon?

* A Site Recovery az újabb [Azure Portalon](https://portal.azure.com) és a [klasszikus Azure portálon](https://manage.windowsazure.com/) egyaránt üzembe helyezhető.
* A klasszikus Azure portálon a Site Recovery a klasszikus szolgáltatáskezelési modellel támogatható.
* Az Azure Portal a klasszikus modellt és az újabb [Resource Manager-alapú üzemi modellt](../azure-resource-manager/resource-manager-deployment-model.md) is támogatja.
- A klasszikus portál csak a meglévő Site Recovery-környezetek fenntartására használható. Új tárolók nem hozhatók létre a klasszikus portálon.

## <a name="next-steps"></a>Következő lépések
* További információk a [támogatott számítási feladatokról](site-recovery-workload.md)
* További információk a [Site Recovery architektúrájáról és összetevőiről](site-recovery-components.md)

