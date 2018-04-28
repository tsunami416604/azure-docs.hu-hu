---
title: A Hyper-V replikáció az Azure támogatási mátrix |} Microsoft Docs
description: A támogatott összetevők és a Hyper-V replikálás az Azure-bA az Azure Site Recovery követelményei
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: d2c637dc742ee854c7787cf7cd883930c4eaa8bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>A Hyper-V replikáció az Azure támogatási mátrix


Ez a cikk bemutatja a támogatott összetevők és az Azure-bA helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítások használatával [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Részletek**
--- | --- 
A Hyper-V Virtual Machine Managerrel | A System Center Virtual Machine Manager hálóban felügyelt Hyper-V-gazdagépeken futó virtuális gépek Azure-bA vész-helyreállítási végezheti el.<br/><br/> Ebben a forgatókönyvben, az Azure portálon vagy a PowerShell használatával telepítheti.<br/><br/> Ha Hyper-V-gazdagépek Virtual Machine Manager által felügyelt, is elvégezhet katasztrófa utáni helyreállítás egy másodlagos helyszíni helyre. Ebben a forgatókönyvben kapcsolatos további tudnivalókért olvassa el [ebben az oktatóanyagban](tutorial-vmm-to-vmm.md).
A Hyper-V nélkül a Virtual Machine Manager | Vész-helyreállítási Azure Virtual Machine Manager által nem felügyelt Hyper-V-gazdagépeken futó virtuális gépek végezheti el.<br/><br/> Ebben a forgatókönyvben, az Azure portálon vagy a PowerShell használatával telepítheti. 


## <a name="on-premises-servers"></a>Helyszíni kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
Hyper-V (fut a Virtual Machine Manager nélkül) | Windows Server 2016-ot, a Windows Server 2012 R2 legújabb frissítéseit | Ha Hyper-V hely konfigurálása a Site Recovery szolgáltatásban, 2012 R2 és Windows Server 2016 rendszert futtató gazdagépeken keverése nem támogatott.<br/><br/> Windows Server 2016 rendszerű gazdagépen található virtuális gép esetében egy másik helyre történő helyreállítás nem támogatott.
Hyper-V (fut a Virtual Machine Managerrel) | A Virtual Machine Manager 2016-ot, a Virtual Machine Manager 2012 R2 | A Virtual Machine Manager használata esetén a Windows Server 2016-állomások a Virtual Machine Manager 2016 kell kezelni.<br/><br/> A Virtual Machine Manager felhő, amelyek a Windows Server 2016 és 2012 R2 rendszeren futó Hyper-V gazdagépek keveri jelenleg nem támogatott.<br/><br/> Egy meglévő Virtual Machine Manager 2012 R2 Server 2016 frissítés tartalmazó környezetek esetében nem támogatottak.


## <a name="replicated-vms"></a>A replikált virtuális gépek


A következő táblázat összefoglalja a VM-támogatási szolgálathoz. A Site Recovery bármilyen számítási feladatot, a támogatott operációs rendszeren futó támogatja. 

 **Összetevő** | **Részletek**
--- | ---
Virtuálisgép-konfiguráció | Virtuális gépek replikálása Azure-ba, meg kell felelnie [Azure-követelményeknek](#failed-over-azure-vm-requirements).
Vendég operációs rendszer | A vendég operációs rendszer [használható az Azure-](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016 Nano Server nem támogatott.




## <a name="hyper-v-network-configuration"></a>A Hyper-v konfiguráció

**Összetevő** | **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | ---
Gazdagép hálózati: hálózati összevonása | Igen
Gazdagép hálózati: VLAN | Igen
Gazdagép hálózati: IPv4 | Igen
Gazdagép hálózati: IPv6 | Nem
Vendég Virtuálisgép-hálózathoz: hálózati összevonása | Nem
Vendég Virtuálisgép-hálózathoz: IPv4 | Igen
Vendég Virtuálisgép-hálózathoz: IPv6 | Nem
Vendég Virtuálisgép-hálózathoz: statikus IP-címet (Windows) | Igen
Vendég Virtuálisgép-hálózathoz: statikus IP-címet (Linux) | Nem
Vendég Virtuálisgép-hálózathoz: több hálózati Adapterrel | Igen



## <a name="azure-vm-network-configuration-after-failover"></a>Az Azure Virtuálisgép-hálózati konfiguráció (után feladatátvétel)

**Összetevő** | **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Igen | Igen
ILB | Igen | Igen
ELB | Igen | Igen
Azure Traffic Manager | Igen | Igen
Multi-NIC | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Tartsa meg a forrás IP-címe | Igen | Igen
Azure virtuális hálózat szolgáltatás-végpontok<br/><br/> (Az azure Storage tűzfalak és virtuális hálózatok) | Nem | Nem


## <a name="hyper-v-host-storage"></a>Hyper-V-gazdagép tárolási

**Storage** | **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | --- | ---
NFS | NA | NA
SMB 3.0 | Igen | Igen
SAN (ISCSI) | Igen | Igen
Többutas (MPIO). Tesztelése:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> A CLARiiON DSM EMC PowerPath | Igen | Igen

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V Virtuálisgép-Vendég tároló

**Storage** | **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Igen | Igen
2. generációs VM | Igen | Igen
EFI/UEFI| Igen | Igen
Megosztott fürtlemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | NA | NA
SMB 3.0 | Nem | Nem
RDM | NA | NA
Lemez > 1 TB-os | Igen, legfeljebb 4095 GB | Igen, legfeljebb 4095 GB
Lemez: 4 KB-os logikai és fizikai szektorba. | Nem támogatott: 1/generációból 2. generációs | Nem támogatott: 1/generációból 2. generációs
Lemez: 4 KB-os logikai és fizikai 512 bájtos szektort | Igen |  Igen
Csíkozott kötetet > 1 TB-os<br/><br/> Logikai kötetkezelés (LVM) | Igen | Igen
Tárolóhelyek | Igen | Igen
Gyakran használt adatok hozzáadása lemez | Nem | Nem
Lemez kizárása | Igen | Igen
(MPIO) többutas | Igen | Igen

## <a name="azure-storage"></a>Azure Storage

**Összetevő** | **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | ---
Helyileg redundáns tárolás | Igen | Igen
Georedundáns tárolás | Igen | Igen
Írásvédett georedundáns tárolás | Igen | Igen
Ritkán használt adatok | Nem | Nem
Gyakran használt adatok| Nem | Nem
Blokkblobok | Nem | Nem
Titkosítását (SSE)| Igen | Igen
Prémium szintű Storage | Igen | Igen
Import/export szolgáltatás | Nem | Nem
Az Azure virtuális hálózat Szolgáltatásvégpontok (Azure Storage tűzfalak és virtuális hálózatok) a cél-és a replikált adatok használt a gyorsítótár storage-fiók | Nem | Nem


## <a name="azure-compute-features"></a>Az Azure compute szolgáltatások

**Funkció** | **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
HUB | Igen | Igen  
Felügyelt lemezek | Igen, a feladatátvételre.<br/><br/> Feladat-visszavétel felügyelt lemezek nem támogatott. | Igen, a feladatátvételre.<br/><br/> Feladat-visszavétel felügyelt lemezek nem támogatott.

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelményeknek

A helyszíni virtuális gépek replikálása Azure-ba, meg kell felelnie Azure virtuális Gépen az alábbi táblázatban foglaltak.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A Site Recovery minden operációs rendszereket támogatja [használható az Azure-](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációs rendszert tároló lemez mérete | 1. generációs virtuális gépek legfeljebb 2048 GB-ot.<br/><br/> 2. generációs virtuális gépek és 300 GB.  | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációs rendszer lemez száma | 1 | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemez | 16 vagy kevesebb  | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adattároló lemez virtuális merevlemez mérete | Legfeljebb 4095 GB | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott |
Megosztott virtuális merevlemez | Nem támogatott | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Merevlemez formátuma | VHD <br/><br/> VHDX | A Site Recovery automatikusan átalakítja VHDX virtuális merevlemezen történő feladatátadást követően Azure-bA. Ha nem sikerül vissza a helyszíni, a virtuális gépeket leállítaná a VHDX formátum használatára.
BitLocker | Nem támogatott | A BitLocker a virtuális gépek replikációjának engedélyezése előtt le kell tiltani.
a virtuális gép neve | 1 és 63 karakter között. Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie. | Frissítse az értéket a virtuális gép tulajdonságai, a Site Recovery szolgáltatásban.
Virtuálisgép-típussá | 1. generációs<br/><br/> Windows – a 2. generációs | 2. generációs virtuális gépek egy basic (amely egy vagy két adatkötetek VHDX formátumú tartalmazza) lemez típusa és kisebb, mint 300 GB lemezterület támogatottak.<br></br>Linux generációs 2 virtuális gépek nem támogatottak. [További információk](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Helyreállítási szolgáltatások tároló műveletek

**Művelet** |  **A Hyper-V Virtual Machine Managerrel** | **A Hyper-V nélkül a Virtual Machine Manager**
--- | --- | --- 
Erőforráscsoportok közötti áthelyezése közben tároló<br/><br/> Belül és között előfizetések | Nem | Nem 
Tárolási, hálózati, Azure virtuális gépek között erőforráscsoportok áthelyezéséhez<br/><br/> Belül és között előfizetések | Nem | Nem 


## <a name="provider-and-agent"></a>Provider és Agent

Arra, hogy a központi telepítés ebben a cikkben beállításokkal kompatibilis, bizonyosodjon meg arról, amelyen a legújabb szolgáltató és az ügynökverziókat.

**Name (Név)** | **Leírás** | **Részletek**
--- | --- | --- | --- | ---
Az Azure Site Recovery provider | Koordinálja a helyszíni kiszolgálók és az Azure közötti kommunikáció <br/><br/> Hyper-V Virtual Machine Managerrel: telepítve a Virtual Machine Manager-kiszolgálón<br/><br/> Hyper-V nélkül a Virtual Machine Manager: telepített Hyper-V-gazdagépek| Legújabb verzió: 5.1.2700.1 (az Azure portálról érhető el)<br/><br/> [Legújabb funkcióit és javításokat](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
A Microsoft Azure Recovery Services Agent ügynök | Koordinálja a Hyper-V virtuális gépek és az Azure közötti replikáció<br/><br/> Telepített helyszíni Hyper-V kiszolgálón (a vagy a Virtual Machine Manager nélkül) | A portálon elérhető legújabb ügynök






## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [Azure előkészítése](tutorial-prepare-azure.md) vész-helyreállítási helyszíni Hyper-V virtuális gépek. 
