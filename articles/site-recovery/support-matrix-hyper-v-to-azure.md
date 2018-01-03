---
title: "A Hyper-V replikáció Azure támogatási mátrix |} Microsoft Docs"
description: "A támogatott összetevők és a Hyper-V replikálás az Azure-bA az Azure Site Recovery követelményei"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5918c56c2b7d01c884bf846e3a7d621b3393bb96
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>A Hyper-V replikáció az Azure támogatási mátrix


Ez a cikk bemutatja a támogatott összetevők és az Azure-ba, a helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítások használata a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="supported-scenarios"></a>Támogatott esetek

**A forgatókönyv** | **Részletek**
--- | --- 
**A Hyper-V a VMM-mel** | A System Center Virtual Machine Manager (VMM) hálóban kezelt Hyper-V-gazdagépeken futó virtuális gépek Azure-bA vész-helyreállítási végezheti el<br/><br/> Ebben a forgatókönyvben az Azure-portálon, vagy a PowerShell használatával telepítheti.<br/><br/> Ha a Hyper-V-gazdagépek a VMM által felügyelt, katasztrófa utáni helyreállítás egy másodlagos helyszíni helyre is elvégezheti. Olvasási [Ez a cikk](tutorial-vmm-to-vmm.md) további információt ebben a forgatókönyvben.
**A Hyper-V VMM nélkül** | A VMM által nem felügyelt Hyper-V-gazdagépeken futó virtuális gépek vész-helyreállítási az Azure-bA végezheti el.<br/><br/> Ebben a forgatókönyvben az Azure-portálon, vagy a Powershell használatával telepítheti. 


## <a name="on-premises-servers"></a>Helyszíni kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
**Hyper-V-(VMM nélkül fut)** | Windows Server 2016-ot, a Windows Server 2012 R2 legújabb frissítéseit. | Ha Hyper-V hely konfigurálása a Site Recovery szolgáltatásban, 2012 R2 és Windows Server 2016 rendszert futtató gazdagépeken keverése nem támogatott.<br/><br/> Windows Server 2016 rendszerű gazdagépen található virtuális gép esetében egy másik helyre történő helyreállítás nem támogatott.
**A Hyper-V (fut a VMM-mel)** | A VMM 2016 VMM 2012 R2-BEN. | A VMM használata esetén a Windows Server 2016 állomások VMM 2016 kell kezelni.<br/><br/> Windows Server 2016 és 2012 R2 rendszeren futó Hyper-V gazdagépek keveri VMM-felhő jelenleg nem támogatott.<br/><br/> Egy létező VMM 2012 R2-kiszolgálót a 2016 frissítését tartalmazó környezetek esetében nem támogatottak.


## <a name="replicated-vms"></a>A replikált virtuális gépek


A következő táblázat összefoglalja a VM-támogatási szolgálathoz. A Site Recovery bármilyen számítási feladatot, a támogatott operációs rendszeren futó támogatja. 

 **Összetevő** | **Részletek**
--- | ---
Virtuálisgép-konfiguráció | Virtuális gépek replikálása Azure-ba, meg kell felelnie [Azure-követelményeknek](#failed-over-azure-vm-requirements).
Vendég operációs rendszer | A vendég operációs rendszer [használható az Azure-](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> Windows Server 2016 Nano Server nem támogatott.




## <a name="hyper-v-network-configuration"></a>A Hyper-v konfiguráció

**Összetevő** | **A Hyper-V a VMM-mel** | **A Hyper-V VMM nélkül**
--- | --- | ---
Gazdagép hálózati: hálózati adapterek összevonása | Igen
Gazdagép hálózati: VLAN | Igen
Gazdagép hálózati: IPv4 | Igen
Gazdagép hálózati: IPv6 | Nem
Vendég virtuális gép hálózati: hálózati adapterek összevonása | Nem
Vendég virtuális gép hálózati: IPv4 | Igen
Vendég virtuális gép hálózati: IPv6 | Nem
Vendég virtuális gép hálózati: statikus IP (Windows) | Igen
Vendég virtuális gép hálózati: statikus IP (Linux) | Nem
Vendég virtuális gép hálózati: több-a hálózati adapter | Igen



## <a name="azure-vm-network-configuration-after-failover"></a>Az Azure Virtuálisgép-hálózati konfiguráció (után feladatátvétel)

**Összetevő** | **A Hyper-V a VMM-mel** | **A Hyper-V VMM nélkül**
--- | --- | ---
ExpressRoute | Igen | Igen
ILB | Igen | Igen
ÜZEMBE HELYEZETT ELB | Igen | Igen
Traffic Manager | Igen | Igen
Több hálózati Adapterrel | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Tartsa meg a forrás IP-címe | Igen | Igen
Virtuális hálózat Szolgáltatásvégpontok<br/><br/> (Az azure storage tűzfalak és virtuális hálózatok) | Nem | Nem


## <a name="hyper-v-host-storage"></a>Hyper-V-gazdagép tárolási

**Storage** | **A Hyper-V a VMM-mel** | A Hyper-V VMM nélkül
--- | --- | --- | ---
NFS | NA | NA
SMB 3.0 | Igen | Igen
SAN (ISCSI) | Igen | Igen
Többutas (MPIO). Tesztelése:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> A CLARiiON DSM EMC PowerPath | Igen | Igen

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V Virtuálisgép-Vendég tároló

**Storage** | **A Hyper-V a VMM-mel** | A Hyper-V VMM nélkül
--- | --- | ---
VMDK-FÁJL | NA | NA
VHD/VHDX | Igen | Igen
2. generációs VM | Igen | Igen
EFI/UEFI| Igen | Igen
Megosztott fürtlemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | NA | NA
SMB 3.0 | Nem | Nem
RDM | NA | NA
> 1 TB méretű lemez | Igen, legfeljebb 4095 GB | Igen, legfeljebb 4095 GB
Lemez: 4 KB-os logikai és fizikai szektorba. | Nem támogatott: 1/generációból 2. generációs | Nem támogatott: 1/generációból 2. generációs
Lemez: 4 KB-os logikai és fizikai 512 bájtos szektort | Igen |  Igen
A csíkozott > 1 TB-os kötet<br/><br/> LVM logikai kötetkezelés | Igen | Igen
Tárolóhelyek | Igen | Igen
Gyakran használt adatok hozzáadása lemez | Nem | Nem
Lemez kizárása | Igen | Igen
(MPIO) többutas | Igen | Igen

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **A Hyper-V a VMM-mel** | **Hyper-V VMM nélkül)**
--- | --- | ---
LRS | Igen | Igen
GRS | Igen | Igen
RA-GRS | Igen | Igen
Ritkán használt adatok | Nem | Nem
Gyakran használt adatok| Nem | Nem
Blokkblobok | Nem | Nem
Rest(SSE) titkosítását| Igen | Igen
Prémium szintű Storage | Igen | Igen
Import/export szolgáltatás | Nem | Nem
Virtuális hálózat Szolgáltatásvégpontok (az Azure storage tűzfalak és a Vnetek) a cél-és a replikált adatok használt a gyorsítótár storage-fiók | Nem | Nem


## <a name="azure-compute-features"></a>Az Azure compute szolgáltatások

**Funkció** | **A Hyper-V a VMM-mel** | **A Hyper-V VMM nélkül**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
HUB | Igen | Igen  
Felügyelt lemezek | Igen, a feladatátvétel<br/><br/> Feladat-visszavétel felügyelt lemezek nem támogatott. | Igen, a feladatátvétel<br/><br/> Feladat-visszavétel felügyelt lemezek nem támogatott.

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelményeknek

A helyszíni virtuális gépek replikálása Azure-ba, meg kell felelnie Azure virtuális Gépen az alábbi táblázatban foglaltak.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
**Vendég operációs rendszer** | A Site Recovery minden operációs rendszereket támogatja [használható az Azure-](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Vendég operációs rendszer architektúrája** | 64 bites | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Operációs rendszert tároló lemez mérete** | 1. generációs virtuális gépek legfeljebb 2048 GB-ot.<br/><br/> 2. generációs virtuális gépek és 300 GB.  | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Operációs rendszer lemez száma** | 1 | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Adatlemez** | 16 vagy kevesebb  | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Adattároló lemez virtuális merevlemez mérete** | Legfeljebb 4095 GB | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Hálózati adapterek** | Több adapter támogatottak. |
**Megosztott virtuális merevlemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**FC-lemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Merevlemez formátuma** | VIRTUÁLIS MEREVLEMEZ <br/><br/> VHDX | A Site Recovery automatikusan átalakítja VHDX virtuális merevlemezen történő feladatátadást követően Azure-bA. Ha nem sikerül vissza a helyszíni virtuális gépek továbbra is a VHDX formátum.
**A BitLocker** | Nem támogatott | A BitLocker a virtuális gépek replikációjának engedélyezése előtt le kell tiltani.
**Virtuális gép neve** | 1 és 63 karakter közötti. Kizárólag betűket, számokat és kötőjeleket tartalmazhat. A virtuális gép nevét kell kezdődnie, és betűvel vagy számmal végződhet. | Frissítse az értéket a virtuális gép tulajdonságai, a Site Recovery szolgáltatásban.
**Virtuálisgép-típussá** | 1. generációs<br/><br/> Windows – a 2. generációs | 2. generációs virtuális gépek egy basic (amely egy vagy két adatkötetek VHDX formátumú tartalmazza) lemez típusa és kisebb, mint 300 GB lemezterület támogatottak.<br></br>Linux generációs 2 virtuális gépek nem támogatottak. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Helyreállítási szolgáltatások tároló műveletek

**Művelet** |  **A Hyper-V a VMM-mel** | **A Hyper-V VMM nélkül**
--- | --- | --- 
Erőforráscsoportok közötti áthelyezése közben tároló<br/><br/> Belül és között előfizetések | Nem | Nem 
Tárolási, hálózati, Azure virtuális gépek között erőforráscsoportok áthelyezéséhez<br/><br/> Belül és között előfizetések | Nem | Nem 


## <a name="provider-and-agent"></a>Provider és Agent

Arra, hogy a központi telepítés ebben a cikkben beállításokkal kompatibilis, bizonyosodjon meg arról, amelyen a legújabb szolgáltató és az ügynökverziókat.

**Name (Név)** | **Leírás** | **Részletek**
--- | --- | --- | --- | ---
**Az Azure Site Recovery Providert** | Koordinálja a helyszíni kiszolgálók és az Azure közötti kommunikáció <br/><br/> Hyper-V és a VMM: VMM-kiszolgálókon telepítve<br/><br/> Hyper-V nélkül VMM: telepített Hyper-V-gazdagépek| Legújabb verzió: 5.1.2700.1 (elérhető a portál)<br/><br/> [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**A Microsoft Azure Recovery Services (MARS) ügynök** | Koordinálja a Hyper-V virtuális gépek és az Azure közötti replikáció<br/><br/> Telepített helyszíni Hyper-V kiszolgálón (a vagy VMM nélkül) | Elérhető a portál legújabb ügynök






## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [Azure előkészítése](tutorial-prepare-azure.md) vész-helyreállítási helyszíni Hyper-V virtuális gépek. 
